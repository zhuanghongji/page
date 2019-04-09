# MPAndroidChart 笔记：动画

## 效果图

### LineChart 效果图

![](./res/001.gif) ![](./res/002.gif)

![](./res/003.gif) ![](./res/004.gif)

```java
// 设置动画
chart.animateX(8000);         // 图1
chart.animateY(8000);         // 图2
chart.animateXY(8000, 8000);  // 图3
chart.animateY(8000, Easing.EasingOption.EaseInElastic );   // 图4
```

### BarChart 效果图

![](./res/005.gif) ![](./res/006.gif)

![](./res/007.gif) ![](./res/008.gif)

```java
// 设置动画
chart.animateX(8000);         // 图1
chart.animateY(8000);         // 图2
chart.animateXY(8000, 8000);  // 图3
chart.animateY(8000, Easing.EasingOption.EaseInSine);   // 图4
```

### PieChart 效果图

![](./res/009.gif) ![](./res/010.gif)

![](./res/011.gif) ![](./res/012.gif)

```java
// 设置动画
chart.animateX(8000); // 图1
chart.animateY(8000); // 图2
chart.animateXY(8000,8000); // 图3
chart.animateY(8000, Easing.EasingOption.EaseOutBounce); // 图4
```

## 概述

MPAndroidChart 的动画机制只在Android `API 11 (Android 3.0.x)` 和以上有效。
在低于 **Android 3.0.x** 的版本中动画不会执行 (但不会引起 crash)。

所有图表类型都支持动画，可以用来创建/建立图表在一个很棒的方法。三种不同的动画方法存在,动画，或者 x 轴和 y 轴分别:
- `animateX(int durationMillis)` : 水平轴的图表值动画，这意味着在指定的时间内从左到右 **建立图表**。
- `animateY(int durationMillis)` : 垂直轴的图表值动画，这意味着在指定的时间内从下到上 **建立图表**。
- `animateXY(int xDuration, int yDuration)` : 两个轴的图表值动画，从左到右，从下到上 **建立图表**。
  
```java
mChart.animateX(3000); // animate horizontal 3000 milliseconds
// or:
mChart.animateY(3000); // animate vertical 3000 milliseconds
// or:
mChart.animateXY(3000, 3000); // animate horizontal and vertical 3000     milliseconds
```

任意一种 `animate(...)` 动画方法被调用后，无需再调用 `invalidate()` 方法。


## ChartAnimator 动画源码分析

```java
/**
 * Object responsible for all animations in the Chart. ANIMATIONS ONLY WORK FOR
 * API LEVEL 11 (Android 3.0.x) AND HIGHER.
 */
@SuppressLint("NewApi")
public class ChartAnimator {

    /** object that is updated upon animation update */
    private AnimatorUpdateListener mListener;

    public ChartAnimator() {}

    public ChartAnimator(AnimatorUpdateListener listener) {
        mListener = listener;
    }

    /** the phase that is animated and influences the drawn values on the y-axis */
    protected float mPhaseY = 1f;

    /** the phase that is animated and influences the drawn values on the x-axis */
    protected float mPhaseX = 1f;

    // 下面的动画方法
}
```

### animateX(...) 方法的源码

```java
/**
  * Animates the rendering of the chart on the x-axis with the specified
  * animation time. If animate(...) is called, no further calling of
  * invalidate() is necessary to refresh the chart.
  *
  * @param durationMillis
  */
public void animateX(int durationMillis) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorX = ObjectAnimator.ofFloat(this, "phaseX", 0f, 1f);
    animatorX.setDuration(durationMillis);
    animatorX.addUpdateListener(mListener);
    animatorX.start();
}

public void animateX(int durationMillis, Easing.EasingOption easing) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorX = ObjectAnimator.ofFloat(this, "phaseX", 0f, 1f);
    animatorX.setInterpolator(Easing.getEasingFunctionFromOption(easing));
    animatorX.setDuration(durationMillis);
    animatorX.addUpdateListener(mListener);
    animatorX.start();
}


public void animateX(int durationMillis, EasingFunction easing) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorX = ObjectAnimator.ofFloat(this, "phaseX", 0f, 1f);
    animatorX.setInterpolator(easing);
    animatorX.setDuration(durationMillis);
    animatorX.addUpdateListener(mListener);
    animatorX.start();
}
```

### animateY(...) 方法的源码

```java
/**
  * Animates the rendering of the chart on the y-axis with the specified
  * animation time. If animate(...) is called, no further calling of
  * invalidate() is necessary to refresh the chart.
  *
  * @param durationMillis
  */
public void animateY(int durationMillis) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorY = ObjectAnimator.ofFloat(this, "phaseY", 0f, 1f);
    animatorY.setDuration(durationMillis);
    animatorY.addUpdateListener(mListener);
    animatorY.start();
}

public void animateY(int durationMillis, Easing.EasingOption easing) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorY = ObjectAnimator.ofFloat(this, "phaseY", 0f, 1f);
    animatorY.setInterpolator(Easing.getEasingFunctionFromOption(easing));
    animatorY.setDuration(durationMillis);
    animatorY.addUpdateListener(mListener);
    animatorY.start();
}

public void animateY(int durationMillis, EasingFunction easing) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorY = ObjectAnimator.ofFloat(this, "phaseY", 0f, 1f);
    animatorY.setInterpolator(easing);
    animatorY.setDuration(durationMillis);
    animatorY.addUpdateListener(mListener);
    animatorY.start();
}
```

### animateXY(...) 方法的源码

```java
/**
  * Animates the drawing / rendering of the chart on both x- and y-axis with
  * the specified animation time. If animate(...) is called, no further
  * calling of invalidate() is necessary to refresh the chart.
  *
  * @param durationMillisX
  * @param durationMillisY
  */
public void animateXY(int durationMillisX, int durationMillisY) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorY = ObjectAnimator.ofFloat(this, "phaseY", 0f, 1f);
    animatorY.setDuration(
            durationMillisY);
    ObjectAnimator animatorX = ObjectAnimator.ofFloat(this, "phaseX", 0f, 1f);
    animatorX.setDuration(
            durationMillisX);

    // make sure only one animator produces update-callbacks (which then
    // call invalidate())
    if (durationMillisX > durationMillisY) {
        animatorX.addUpdateListener(mListener);
    } else {
        animatorY.addUpdateListener(mListener);
    }

    animatorX.start();
    animatorY.start();
}

public void animateXY(int durationMillisX, int durationMillisY, Easing.EasingOption easingX,
        Easing.EasingOption easingY) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorY = ObjectAnimator.ofFloat(this, "phaseY", 0f, 1f);
    animatorY.setInterpolator(Easing.getEasingFunctionFromOption(easingY));
    animatorY.setDuration(
            durationMillisY);
    ObjectAnimator animatorX = ObjectAnimator.ofFloat(this, "phaseX", 0f, 1f);
    animatorX.setInterpolator(Easing.getEasingFunctionFromOption(easingX));
    animatorX.setDuration(
            durationMillisX);

    // make sure only one animator produces update-callbacks (which then
    // call invalidate())
    if (durationMillisX > durationMillisY) {
        animatorX.addUpdateListener(mListener);
    } else {
        animatorY.addUpdateListener(mListener);
    }

    animatorX.start();
    animatorY.start();
}

public void animateXY(int durationMillisX, int durationMillisY, EasingFunction easingX,
        EasingFunction easingY) {

    if (android.os.Build.VERSION.SDK_INT < 11)
        return;

    ObjectAnimator animatorY = ObjectAnimator.ofFloat(this, "phaseY", 0f, 1f);
    animatorY.setInterpolator(easingY);
    animatorY.setDuration(
            durationMillisY);
    ObjectAnimator animatorX = ObjectAnimator.ofFloat(this, "phaseX", 0f, 1f);
    animatorX.setInterpolator(easingX);
    animatorX.setDuration(
            durationMillisX);

    // make sure only one animator produces update-callbacks (which then
    // call invalidate())
    if (durationMillisX > durationMillisY) {
        animatorX.addUpdateListener(mListener);
    } else {
        animatorY.addUpdateListener(mListener);
    }

    animatorX.start();
    animatorY.start();
}
```

## 缓动动画 (Animation easing)

这个库可以让你对动画应用"缓动函数"。

你可以选择以下预定义的静态 `Easing.EasingOption`: 

```java
public enum EasingOption {
    Linear,
    EaseInQuad,
    EaseOutQuad,
    EaseInOutQuad,
    EaseInCubic,
    EaseOutCubic,
    EaseInOutCubic,
    EaseInQuart,
    EaseOutQuart,
    EaseInOutQuart,
    EaseInSine,
    EaseOutSine,
    EaseInOutSine,
    EaseInExpo,
    EaseOutExpo,
    EaseInOutExpo,
    EaseInCirc,
    EaseOutCirc,
    EaseInOutCirc,
    EaseInElastic,
    EaseOutElastic,
    EaseInOutElastic,
    EaseInBack,
    EaseOutBack,
    EaseInOutBack,
    EaseInBounce,
    EaseOutBounce,
    EaseInOutBounce,
}
```

基本上，有以下两种方式进行 easing 你的动画。

* 预定义的缓动选项：(下面代码可在所有 Android 版本运行)
  
```java
public void animateY(int durationmillis, Easing.EasingOption option); 
```

* 例如，调用带有预定义缓动选项的动画方法：
  
```java
// animate both axes with easing
mChart.animateY(3000, Easing.EasingOption.EaseOutBack); 
```

* 当你想代码运行在 Android 3.0 (API 11) 以下时0，总是使用 `Easing.EasingOption` 。

自定义缓动函数 (在 Android 3.0 自定义缓动函数会使应用 crash)：

```java
public void animateY(int durationmillis, EasingFunction function); 
```

- **Create your own** easing functions by creating your own easing-function class and implementing the `EasingFunction` interface: 
  
```java
/**
* Interface for creating custom made easing functions. 
*/
public interface EasingFunction {
    /**
    * Called everytime the animation is updated.
    * @param input - the time passed since the animation started (value between 0 and 1)
    */
    public float getInterpolation(float input);
}
```

Then call it this way ( **be aware, this will not run below Android 3.0 and crash**):

```java
// animate both axes with easing
mChart.animateY(3000, new MyEasingFunction()); 
```

## Easing.EasingOption 所有效果图

### Linear , EaseInQuad

![](./res/013.gif) ![](./res/014.gif)


### EaseOutQuad , EaseInOutQuad

![](./res/015.gif) ![](./res/016.gif)


### EaseInCubic , EaseOutCubic

![](./res/017.gif) ![](./res/018.gif)


### EaseInOutCubic , EaseInQuart

![](./res/019.gif) ![](./res/020.gif)


### EaseOutQuart , EaseInOutQuart

![](./res/021.gif) ![](./res/022.gif)


### EaseInSine , EaseOutSine

![](./res/023.gif) ![](./res/024.gif)


### EaseInOutSine , EaseInExpo

![](./res/025.gif) ![](./res/026.gif)


### EaseOutExpo , EaseInOutExpo

![](./res/027.gif) ![](./res/028.gif)


### EaseInCirc , EaseOutCirc

![](./res/029.gif) ![](./res/030.gif)


### EaseInOutCirc , EaseInElastic

![](./res/031.gif) ![](./res/032.gif)


### EaseOutElastic , EaseInOutElastic

![](./res/033.gif) ![](./res/034.gif)


### EaseInBack , EaseOutBack

![](./res/035.gif) ![](./res/036.gif)


### EaseInOutBack , EaseInBounce

![](./res/037.gif) ![](./res/038.gif)


### EaseOutBounce , EaseInOutBounce

![](./res/039.gif) ![](./res/040.gif)


本节完。