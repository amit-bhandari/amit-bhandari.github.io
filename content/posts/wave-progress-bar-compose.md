+++
title = 'Building Wave Progress Bar with Jetpack Compose'
date = 2024-11-12T19:37:49+05:30
draft = false
tags = ['Android', 'Jetpack Compose', 'Custom View', 'Kotlin']
description = 'Building custom wave progress bar using Jetpack compose in Android.'
metaImage = "/images/wave-progress-bar-compose/compose.webp"
+++

## TLDR

I created a customizable wave progress bar using Jetpack Compose, as shown below. You can view the code on {{< newtabref href=https://github.com/amit-bhandari/Wave-Progress-Compose title="Github" >}}. The component can be wrapped within any Jetpack View (here, it's wrapped in a circular card). You can easily adjust various properties of the progress bar, including frequency, amplitude, direction, and speed, to fit your design needs.

{{< figure src="/images/wave-progress-bar-compose/progress.webp" alt="Wave Progress Bar" width="400">}}

## Implementation

### Sine Wave

The closest way to render a wave-like animation is by using a sine wave, which can be drawn with the Android {{< newtabref href=https://developer.android.com/reference/android/graphics/Path title="path" >}} and {{< newtabref href=https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.math/sin.html title="sin" >}} APIs. The utility function below generates a sine wave path, allowing for customization through various parameters.

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

You can call the utility function within the drawBehind API of a modifier to draw the sine wave (parameter definitions are omitted for brevity).

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

{{< figure src="/images/wave-progress-bar-compose/sine-wave.webp" alt="Sine Wave" width="400">}}

### Complete Path

To fill the entire area from the left bottom to the right bottom of the sine wave, start the path at the left bottom, end it at the right bottom, and then close it. This can be achieved using the lineTo and close functions of the Path object.

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

To make the sine wave feel dynamic, we need to add multiple animations. One animation continuously changes the wave’s amplitude from low to high, while another moves the wave horizontally. This can be achieved using the animateTo API along with LaunchedEffect (to allow dynamic parameter updates from outside the component).

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

The wave progress component is implemented as a separate module, available {{< newtabref href=https://github.com/amit-bhandari/Wave-Progress-Compose/tree/main/wave_progress title="here" >}}. The repository includes an Activity with sliders to adjust various configurable parameters of the component.

{{< figure src="/images/wave-progress-bar-compose/customizations.webp" alt="Customizations" width="400">}}

## Thank you!

I have uploaded complete code {{< newtabref href=https://github.com/amit-bhandari/Wave-Progress-Compose title="here" >}} in case you want to run it locally or use it in one of your projects. Remember to ⭐ if you like it!
