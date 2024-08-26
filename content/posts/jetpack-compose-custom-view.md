+++
title = 'My First Encounter with Jetpack Compose'
date = 2024-08-25T22:54:15+05:30
draft = false
tags = ['Android', 'Jetpack Compose', 'Custom View', 'Kotlin']
description = "After spending 8 years building views using XML layouts, I tried Jetpack Compose first time ever and I think I am never going back to XML again."
+++
{{< newtabref href= title="" >}}

It's been 3 years since Jetpack Compose first production release came out and I finally got a chance to learn and understand how it works. I tried designing iOS style stopwatch timer to learn and understand how compose works and whether it should be a default choice for all the new UIs being written in Android.

## TLDR
Implemented Stopwatch timer clock UI in compose. Project can be found on {{< newtabref href=https://github.com/amit-bhandari/Stopwatch-Jetpack-Compose/tree/main title="here" >}} on Github. Remember to ⭐ the project if you like it. Its free! 

## Useful Resources
Following resources were really helpful for me to get started, sharing just in case someone is looking for them
- {{< newtabref href=https://www.youtube.com/watch?v=cDabx3SjuOY&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC title="Phillip Lackner Youtube Playlist" >}}
- {{< newtabref href=https://developer.android.com/develop/ui/compose/documentation title="Official Docs" >}}

## Custom views in Compose
While I did spend some time in understanding how {{< newtabref href=https://developer.android.com/develop/ui/compose/layouts/basics title="basic layouts" >}} are designed using Box, Row, Column and other components, I directly jumped into trying to write custom view with some canvas drawing to be done from scratch. Simplest UI which I could think of was stopwatch clock and I took inspiration from iOS Clock app. 

{{< figure src="/images/jetpack-compose-custom-view/iPhone-stopwatch.webp" alt="iPhone Stopwatch UI" width="400">}}

{{< newtabref href=https://developer.android.com/develop/ui/compose/graphics/draw/overview title="Graphics in compose" >}} is pretty simple and straightforward, it provides {{< newtabref href=https://developer.android.com/reference/kotlin/androidx/compose/ui/graphics/package-summary#Canvas(android.graphics.Canvas) title="Canvas" >}} composable api which provides all necessary apis to implement custom drawing.


## Stopwatch Implementation

### Drawing imaginary circle
For drawing imaginary circle, we need to calculate its centerX and centerY coordinates along with radius. Radius is calculated simply as half of the width of Canvas (skipping extra 16 dp padding). {{< newtabref href=https://developer.android.com/reference/kotlin/androidx/compose/ui/layout/OnGloballyPositionedModifier title="OnGloballyPositionedModifier" >}} provides us with view bounds which we can use to get necessary data as shown below.

```kotlin
var circleRadius by remember { mutableFloatStateOf(0f) }
var centerX by remember { mutableFloatStateOf(0f) }
var centerY by remember { mutableFloatStateOf(0f) }

Canvas(
    modifier = modifier
        .fillMaxSize()
        .background(Colors.BLACK.value)
        .padding(16.dp)
        .onGloballyPositioned {
            val bounds = it.boundsInWindow()
            centerX = bounds.size.width / 2f
            centerY = bounds.size.height / 2f
            circleRadius = it.size.width.toFloat() / 2
        }
)

```

### Circled notches
Now that we have center and radius for our imaginary circle, it's time to draw notches around the edges of circle which will form circular structure for clock. Circle is divided in 12 parts with each part having 21 notches, so total 21 * 12= 252 notches.

There are multiple ways to go about this, the one which I chose involves drawing a notch at the edge of circle and using {{< newtabref href=https://developer.android.com/reference/kotlin/androidx/compose/ui/graphics/drawscope/DrawScope#(androidx.compose.ui.graphics.drawscope.DrawScope).rotate(kotlin.Float,androidx.compose.ui.geometry.Offset,kotlin.Function1) title="rotate" >}} function to place it as its correct position. It took me some time {{< newtabref href=https://math.stackexchange.com/a/3367801 title="reading up" >}} on figuring out points at the edge of circle and come up with following piece of code which is repeated 252 times to draw a notch and then rotate it to place it at correct position at the edge of circle. 90f and 270f came up during my trial and error in Compose Preview.

```kotlin
repeat(NOTCH_COUNT) { notchNumber ->
    val circlePerimeterAngle = 270f + (360f / NOTCH_COUNT) * notchNumber
    rotate(
        circlePerimeterAngle + 90f,
        pivot = Offset(
            centerX + circleRadius * cos(circlePerimeterAngle.degreesToRadians()),
            centerY + circleRadius * sin(circlePerimeterAngle.degreesToRadians())
        )
    ) {
        drawRect(
            color = rectColor,
            topLeft = Offset(
                centerX + circleRadius * cos(circlePerimeterAngle.degreesToRadians()),
                centerY + circleRadius * sin(circlePerimeterAngle.degreesToRadians())
            ),
            size = Size(ROTATING_HAND_WIDTH / 2f, rectLength),
        )
    }
}
```

Result
{{< figure src="/images/jetpack-compose-custom-view/circle-with-knotches.webp" alt="Circle with Notches" width="400">}}


### Placement of seconds count 
In repeat loop, for every 5 second mark, need to draw a text beside that notch. Used drawText api to style the text and place it just inside the outer radius of circle using some offsets. 

```kotlin
if (notchNumber.isDivisible(hourInterval)) {
    val p1 = centerX + 0.8f * circleRadius * cos(circlePerimeterAngle.degreesToRadians())
    val p2 = centerY + 0.8f * circleRadius * sin(circlePerimeterAngle.degreesToRadians())

    val s = if (notchNumber == 0) "60"
    else (notchNumber.div(hourInterval).times(5)).toString()
    val tr = textMeasure.measure(
        AnnotatedString(s),
        style = TextStyle(fontSize = 28.sp, color = Colors.WHITE.value)
    )
    drawText(
        tr,
        topLeft = Offset(
            p1 - tr.size.width / 2,//centerX - tr.size.width / 2,
            p2 - tr.size.height / 2,//centerY - tr.size.height / 2
        )
    )
}
```

Result
{{< figure src="/images/jetpack-compose-custom-view/circle-with-numbers.webp" alt="Circle with Numbers" width="400">}}

### Timer text
Rendering timer text was pretty straightforward. Used hardcoded offset to push it vertically down from the center of circle. 

```kotlin
val tr = textMeasure.measure(
    AnnotatedString(currentTimeState.toHms()),
    style = TextStyle(fontSize = 24.sp, color = Colors.WHITE.value)
)
drawText(
    tr,
    topLeft = Offset(
        centerX - tr.size.width / 2,
        centerY - tr.size.height / 2 + 200f
    )
)
```
Result
{{< figure src="/images/jetpack-compose-custom-view/circle-with-timer.webp" alt="Circle with Timer" width="400">}}


### Rotating hand of clock
Now for the most important part of clock, rotating hand, need to draw a rect which extends from center of the circle towards both the sides. One side touches the edge of the circle while other just extends for some distance. Also, to be able to rotate this hand when timer starts, created a variable `currentTimeState` which can control its placement relative to current time. Pivot to rotate this hand would be center of circle. Following snippet draws hand placed at reset position (0), changing 270f to any other value between 0-360 range would make it move to given angle. 

```kotlin
rotate(
    currentTimeState.toRange0To360() + 270f,
    pivot = Offset(centerX, centerY)
) {
    drawRect(
        color = Colors.YELLOW.value,
        topLeft = Offset(
            centerX - ROTATING_HAND_EXTENSION,
            centerY - ROTATING_HAND_WIDTH / 2
        ),
        size = Size(circleRadius + ROTATING_HAND_EXTENSION, ROTATING_HAND_WIDTH),
    )
}
```
Result
{{< figure src="/images/jetpack-compose-custom-view/circle-with-rotating-hand.webp" alt="Circle with Rotating Hand" width="400">}}


### Center stroked circle 
At the center of clock, there is circle with stroke in iOS clock app. There were 2 paths in achieving this, drawing 2 rectangle to make up rotating hand separately or use some hack to draw stroked circle with single rotating hand. I used a hack to keep single rect for rotating hand. I drew 2 circles on top of each other to achieve the results. Black circle emulates hollow space by drawing over rotating hand. 

```kotlin
drawCircle(
    color = Colors.YELLOW.value,
    radius = 14f,
    center = Offset(centerX, centerY),
    style = Stroke(width = ROTATING_HAND_WIDTH)
)
drawCircle(
    Colors.BLACK.value,
    radius = 10f,
    center = Offset(centerX, centerY)
)
```
Result
{{< figure src="/images/jetpack-compose-custom-view/final-result.webp" alt="Final Result" width="400">}}


### Addition motion to rotating hand 
Now the most important part of rotating the hand of clock as timer starts. To be able to know current timer state, I used live data to send updates to Composable from activity. Observables can be converted to compose states by following this {{< newtabref href=https://developer.android.com/develop/ui/compose/state title="documentation" >}}. I used LiveData instead of Floe due to its simplicity to use, but anything can be used (Flow, RxJava etc)

```kotlin
fun StopWatch(
    modifier: Modifier = Modifier,
    currentTime: MutableLiveData<Long>,
){
    //UI
}
```

Used simple extension functions to convert milliseconds time to mm:ss:ms for timer text and angle for rotating hand.
```kotlin
@SuppressLint("DefaultLocale")
fun Long.toHms(): String {
    val minutes = this / 60000
    val seconds = (this % 60000) / 1000
    val milliseconds = (this % 1000) / 10

    return String.format("%02d:%02d:%02d", minutes, seconds, milliseconds)
}

fun Long.toRange0To360(): Float {
    return this / 1000f * (360f / 60f)
}
```

Added simple timer functionality in Activity to trigger timer on tap of Composable to achieve following result. 

{{< figure src="/images/jetpack-compose-custom-view/final-animation.webp" alt="Stopwatch Animation" width="400">}}


Composable took approx {{< newtabref href=https://github.com/amit-bhandari/Stopwatch-Jetpack-Compose/blob/main/app/src/main/java/com/bhandari/composeplayground/StopWatch.kt title="150 lines" >}} to achieve above result. Complete project can be found {{< newtabref href=https://github.com/amit-bhandari/Stopwatch-Jetpack-Compose/tree/main title="here" >}}.

### Takeaways 
There were a lot of takeaways for me from this small exercise
- 100% kotlin codebase
  - Biggest takeaway was achieving whole UI with 100% kotlin code without single line of XML. 
  - XML has been defacto standard for android views since forever and I really felt that compose made it easier and faster for me to write this custom view. 
  - Lot of boilerplate which comes with XML is gone like reading XML to kotlin, defining attributes files for custom view attributes etc. 
- Live Preview 
  - Live previews while we write composable functions makes it easier to follow along with trial and error. XMLs also provide previews, but they are not as good as this. Also one cannot compare writing kotlin vs writing XMLs. 
- Managing view state is easier
  - Some learning curve is there for understanding recommended way to manage states (specially side effects), but once comfortable, they are very powerful and helps us in writing better integration of data with ui states. 

### Thank you! 
I have uploaded complete project to {{< newtabref href=https://github.com/amit-bhandari/Stopwatch-Jetpack-Compose title="Github" >}} in case you want to run it yourself or use it in one of your projects. Remember to ⭐ the project if you like it! 














