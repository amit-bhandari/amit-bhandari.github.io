+++
title = 'Benchmarking impact of Flutter Plugins on startup latency'
date = 2025-03-09T19:55:26+05:30
draft = false
tags = ['Flutter', 'Plugins', 'Benchmarking', 'Dart']
description = "Benchmarking flutter plugins impact on startup time in Flutter Application."
+++

## TLDR

Having lots of plugins which are registered during flutter engine initialization/app startup can nagative impact app startup latency. Having 10s of plugins should not cause alarming effect on startup latency, but its always a good idea to keep them in check and benchmark to be 100% sure. Find all the code [here](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-1-plugin).

## Context

Recently one of my collegue asked in Google's internal forum a question regarding plugins impact on startup latency, whether having common plugin for including one of utility functions over individual plugin for each of such function is beneficial in terms of memory usage and startup latency. I could not find much info regarding this in official flutter docs and one of the answer on the forum suggested to benchmark this to be sure. I decided to do some benchmarking to see the impact myself.

## Sample Plugin

Writing 100s of plugins manually was not practical, so decided to create one plugin manually and make use of LLM to write script to duplicate this plugin `n` times in codebase. I used [pigeon](https://pub.dev/packages/pigeon) which helps generating type safe plugin code. Created a plugin with single method which returns custom object.

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

Suffix `1` is used at class names and files paths to ensure easy duplication of all these plugin files using script. Code generator takes care of generating necessary files for communication between dart and kotlin layer.

Plugin is registered at the launch of application in MainActivity.kt

```kotlin
class MainActivity : FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        HelloPigeon1.setUp(flutterEngine.dartExecutor.binaryMessenger, HelloPigeonImpl1())
    }
}
```

## Plugin Duplication

Wrote small (and highly hacky) [bash script](https://github.com/amit-bhandari/Flutter-Template/blob/plugin-benchmark-1-plugin/pigeons/script.sh) to duplicate this plugin `n` number of times by differentiating each one with the help of prefix. Running this script takes lot of time for bigger `n` as it involves code generation, so created few branches in the repo for 1, 50, 500 and 1000 plugins.

- [`1`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-1-plugin)
- [`50`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-50-plugin)
- [`500`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-500-plugin)
- [`1000`](https://github.com/amit-bhandari/Flutter-Template/tree/plugin-benchmark-1000-plugin)

## Benchmarking

You can run flutter app using command `flutter run --trace-startup --release` to get startup time. I ran this command on real android device (Pixel 4a) to compare latency for 10 times each for every `n` and following are the results.

- `1` plugin: `167.7ms`
- `50` plugins: `185.2ms` `+10.44%`
- `500` plugins: `361.8ms` `+95.36%`
- `1000` plugins: `655.3ms` `+81.12%`

As seen from the data above, more number of plugins directly leads to higher startup latency. Practically, if you have 10s of plugins, changes in startup latency should be insignificant enough to be ignored, but as codebase grows and number of plugins grows, one should always think about benchmarking startup latency if its a critical health metric for your application.

## Impact on memory

Every plugin creates a binary message channel to pass messages to and from dart and kotlin along with bunch of auto generated classes which contributes to both heap as well as code memory usage. Although I haven't benchmarked this, I am sure this will have impact on memory usage as well. Feel free to benchmark this if you are looking to see impact of plugins on memory usage.

## Mitigation

There are couple of ways one can mitigate this impact.

1. Lazy registration of plugins
   - Instead of registering all plugins on app launch, one can explore possibility to register plugins on the fly when needed to avoid impact on startup latency. I haven't yet tried this approach (not sure if flutter even allows this) yet. I am going to try it out and share if something interesting comes up.
2. Common plugin
   - Instead of creating plugin for every small use case, one can think of creating a utility plugin by combining all small plugins together. Obvious downside to this would be a bulky plugin with no respect for single responsibiltiy principal of software engineering at all.

## Conclusion

Always benchmark if your app has lots of plugins before taking call whether this optimisation is needed in the first place or not. After all, premature optimization is the root of all evil!
