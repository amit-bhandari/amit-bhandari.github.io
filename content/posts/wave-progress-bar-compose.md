+++
title = 'Building Wave Progress Bar with Jetpack Compose'
date = 2024-11-12T19:37:49+05:30
draft = false
tags = ['Android', 'Jetpack Compose', 'Custom View', 'Kotlin']
description = "Description"
metaImage = "/images/wave-progress-bar-compose/google.webp"
+++

## TLDR

I created a customizable wave progress bar using Jetpack Compose, as shown below. You can view the code on {{< newtabref href=https://github.com/amit-bhandari/Wave-Progress-Compose title="Github" >}}. The component can be wrapped within any Jetpack View (here, it's wrapped in a circular card). You can easily adjust various properties of the progress bar, including frequency, amplitude, direction, and speed, to fit your design needs.

{{< figure src="/images/wave-progress-bar-compose/progress.webp" alt="Wave Progress Bar" width="400">}}

## Implementation 

### Sine Wave
Closest representation of wave like animation can be rendered using Sine wave, we can draw sine wave by using {{< newtabref href=https://developer.android.com/reference/android/graphics/Path title="path" >}} and {{< newtabref href=https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.math/sin.html title="sin" >}} api. Utility function to generate sine path is shown below with some customisation parameters being provided as parameters. 


```kotlin
fun prepareSinePath(
    path: Path,
    size: Size,
    frequency: Int,
    amplitude: Float,
    phaseShift: Float,
    position: Float,
    step: Int
) {
    for (x in 0..size.width.toInt().plus(step) step step) {
        val y = position + amplitude * sin(x * frequency * Math.PI / size.width + phaseShift).toFloat()
        if (path.isEmpty)
            path.moveTo(x.toFloat(), max(0f, min(y, size.height)))
        else
            path.lineTo(x.toFloat(), max(0f, min(y, size.height)))
    }
}
```

We can use call above utility function from within drawBehind api of modifier to draw Sine wave (definition of all the parameters omitted for brevity).

```kotlin
Box(
    modifier = modifier
        .drawBehind {
            val yPos = (1 - progress) * size.height
            Path()
                .apply {
                    prepareSinePath(this, size, waveFrequency, amplitude, phaseShift, yPos, waveSteps)
                }
                .also {
                    drawPath(path = it, brush = SolidColor(Color.Cyan), style = Stroke(width = 10f))
                }
        }
)
```

This piece of code gives us following output.

{{< figure src="/images/wave-progress-bar-compose/sine-wave.webp" alt="Sine Wave" width="400">}}

### Complete Path
To be able to fill all the area from left bottom to right bottom of Sine Wave, we need to start path at left bottom, finish it at right bottom and then close it. We can use lineTo and close api of Path object to achieve that. 

```kotlin
{
    prepareSinePath(this, size, waveFrequency, amplitude.value, phaseShiftLocal, yPos, waveSteps)
    lineTo(size.width, size.height)
    lineTo(0f, size.height)
    close()
}
```

{{< figure src="/images/wave-progress-bar-compose/complete-path.webp" alt="Complete Path" width="400">}}

We can use Gradient style to fill it out. 

```kotlin
drawPath(path = it, brush = Brush.horizontalGradient(listOf(Color.Magenta, Color.Cyan), style = Fill)
```

{{< figure src="/images/wave-progress-bar-compose/gradient-path.webp" alt="Gradient Path" width="400">}}

### Animation 
To make this sine wave feel like a wave, we need to add multiple animations to it. One animation to change amplitude of wave continuously from low level to high level. Another animation is to move wave horizontally. We can use `animateTo` api along with LaunchedEffect (for allowing change of parameter values dynamically from outside component) to achieve that. 

```kotlin
LaunchedEffect(amplitudeRange, amplitudeDuration) {
    coroutineScope.launch {
        amplitude.stop()
        amplitude.snapTo(amplitudeRange.start)
        amplitude.animateTo(
            targetValue = amplitudeRange.endInclusive,
            animationSpec = infiniteRepeatable(
                animation = tween(durationMillis = amplitudeDuration, easing = LinearEasing),
                repeatMode = RepeatMode.Reverse
            )
        )
    }
}

LaunchedEffect(phaseShiftDuration) {
    coroutineScope.launch {
        phaseShift.stop()
        phaseShift.snapTo(0f)
        phaseShift.animateTo(
            targetValue = (2 * PI).toFloat(),
            animationSpec = infiniteRepeatable(
                animation = tween(durationMillis = phaseShiftDuration, easing = LinearEasing),
                repeatMode = RepeatMode.Restart
            )
        )
    }
}
```

{{< figure src="/images/wave-progress-bar-compose/animating-sine-wave.webp" alt="Wave Progress Bar" width="400">}}

## Library
Wave progress component is written as separate module {{< newtabref href=https://github.com/amit-bhandari/Wave-Progress-Compose/tree/main/wave_progress title="here" >}}. Repo contains Activity will all the sliders to change various configurable parameters of component. 

{{< figure src="/images/wave-progress-bar-compose/customizations.webp" alt="Customizations" width="400">}}

## Thank you!
I have uploaded complete code {{< newtabref href=https://github.com/amit-bhandari/Wave-Progress-Compose title="here" >}} in case you want to run it locally or use it in one of your projects. Remember to ⭐ if you like it! 