+++
title = 'Benchmarking impact of Flutter Plugins on startup latency'
date = 2025-03-30T19:55:26+05:30
draft = false
tags = ['Flutter', 'Plugins', 'Benchmarking', 'Dart']
description = 'Benchmarking flutter plugins impact on startup time in Flutter Application.'
metaImage = "/images/flutter-plugins-startup-latency/flutter.webp"
+++

## TLDR

Registering many plugins during Flutter engine initialization or app startup can negatively impact app startup latency. Having tens of plugins should not cause an alarming effect on startup latency, but it's always a good idea to keep the number in check and benchmark to be certain. Find all the code [here](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-1-plugin).

## Context

Recently, one of my colleagues asked in a Google internal forum about the impact of plugins on startup latency – specifically, whether using a single, common plugin for multiple utility functions is more beneficial than having individual plugins for each function in terms of memory usage and startup latency. I couldn't find much information regarding this in the official Flutter documentation, and one of the answers on the forum suggested benchmarking this to be certain. I decided to do some benchmarking myself to see the impact.

## Sample Plugin

Writing hundreds of plugins manually wasn't practical, so I decided to create one plugin manually and use an LLM to write a script to duplicate this plugin n times within the codebase. I used [pigeon](https://pub.dev/packages/pigeon) which helps generate type-safe plugin code. I created a plugin with a single method that returns a custom object.

```kotlin
@ConfigurePigeon(
  PigeonOptions(
    dartOut: 'lib/plugins/hello_pigeon/hello_pigeon_1.g.dart',
    kotlinOut:
        'android/app/src/main/kotlin/com/example/expense_manager/plugins/hello_pigeon_1/HelloPigeon_1.g.kt',
    kotlinOptions: KotlinOptions(
      errorClassName: 'PigeonError1',
    ),
  ),
)

class Hello1 {
  String? hello;
  String? world;
}

@HostApi()
abstract class HelloPigeon1 {
  Hello1 sayHello();
}
```

The suffix `1` is used in class names and file paths to facilitate easy duplication of these plugin files using the script. The code generator takes care of generating the necessary files for communication between the Dart and Kotlin layers.

The plugin is registered at application launch in `MainActivity.kt`

```kotlin
class MainActivity : FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        HelloPigeon1.setUp(flutterEngine.dartExecutor.binaryMessenger, HelloPigeonImpl1())
    }
}
```

## Plugin Duplication

I wrote a small (and admittedly hacky) [bash script](https://github.com/amit-bhandari/Flutter-Template/blob/plugin-benchmark-1-plugin/pigeons/script.sh) to duplicate this plugin n times, differentiating each copy using a numerical prefix. Running this script takes a lot of time for larger values of `n` as it involves code generation. Consequently, I created a few branches in the repository corresponding to 1, 50, 500, and 1000 plugins:

- [`1`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-1-plugin)
- [`50`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-50-plugin)
- [`500`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-500-plugin)
- [`1000`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-1000-plugin)

## Benchmarking

You can run the Flutter app using the command `flutter run --trace-startup --release` to measure startup time. I ran this command 10 times for each plugin count (n) on a real Android device (Pixel 4a). The following are the average results, showing the increase compared to the baseline of 1 plugin:

- `1` plugin: `167.7ms`
- `50` plugins: `185.2ms` `+10.44%`
- `500` plugins: `361.8ms` `+95.36%`
- `1000` plugins: `655.3ms` `+81.12%`

> Note: The percentage increases shown here are relative to the 1-plugin baseline for clarity. Your original percentages showed the increase from the previous step.

As the data shows, a larger number of plugins directly correlates with higher startup latency. Practically, if you have tens of plugins, the change in startup latency is likely insignificant. However, as the codebase and the number of plugins grow, it's important to benchmark startup latency if it's a critical metric for your application.

## Impact on memory

Every plugin creates a binary message channel for communication between Dart and Kotlin, along with a bunch of auto-generated classes, which contribute to both heap and code memory usage. Although I haven't benchmarked memory usage, I suspect it is also impacted. Feel free to conduct your own benchmarks if you need to assess the impact of plugins on memory.

## Mitigation

There are a couple of ways to mitigate this impact:

1. Lazy registration of plugins: Instead of registering all plugins at app launch, explore the possibility of registering plugins dynamically, only when they are needed. This could reduce the impact on startup latency.
   I haven't tried this approach yet (and I'm unsure if Flutter fully supports dynamic registration). I plan to investigate this and will share any interesting findings.
2. Common plugin: Instead of creating separate plugins for every small use case, consider creating a single 'utility' plugin that combines the functionality of several smaller ones.
   The obvious downside is creating a potentially bulky plugin that violates the Single Responsibility Principle (SRP) of software engineering.

## Conclusion

If your app uses many plugins, always benchmark its startup performance before deciding whether this kind of optimization is necessary. After all, **premature optimization** is the root of all evil!
