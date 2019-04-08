# MPAndroidChart 笔记：与图表进行手势交互

## 概述 
MPAndroidChart 这个库完全支持图表进行触摸和手势的交互，通过回调方法做出对应的操作。

## 启用/禁止手势交互
- `setTouchEnabled(boolean enabled)`: 启用/禁用与图表的所有可能的触摸交互。
- `setDragEnabled(boolean enabled)`: 启用/禁用拖动（平移）图表。
- `setScaleEnabled(boolean enabled)`: 启用/禁用缩放图表上的两个轴。
- `setScaleXEnabled(boolean enabled)`: 启用/禁用缩放在 x 轴上。
- `setScaleYEnabled(boolean enabled)`: 启用/禁用缩放在 y 轴。
- `setPinchZoom(boolean enabled)`: 如果设置为 `true`，捏缩放功能。 如果 `false`，x 轴和 y 轴可分别放大。
- `setDoubleTapToZoomEnabled(boolean enabled)`: 设置为 `false` 以禁止通过在其上双击缩放图表。
- `setHighlightPerDragEnabled(boolean enabled)`: 设置为 `true`，允许每个图表表面拖过，当它完全缩小突出。默认值：`true`。
- `setHighlightPerTapEnabled(boolean enabled)`: 设置为 `false`，以防止值由敲击姿态被突出显示。 值仍然可以通过拖动或编程方式突出显示。默认值：`true`。


## 图表的抛掷/减速

- `setDragDecelerationEnabled(boolean enabled)`: 如果设置为 `true`，手指滑动抛掷图表后继续减速滚动。默认值：`true`。
- `setDragDecelerationFrictionCoef(float coef)`: 减速的摩擦系数在 `[0, 1]` 区间，数值越高表示速度会缓慢下降，例如，如果将其设置为 0，将立即停止。1 是一个无效的值，会自动转换至 0.9999。 
  

## 高亮

- `highlightValues(Highlight[] highs)`: 高亮显示值，高亮显示的点击的位置在数据集中的值。 设置 `null` 或空数组则撤消所有高亮。
- `highlightValue(int xIndex, int dataSetIndex)`: 高亮给定 `xIndex` 在数据集的值。 设置 `xIndex` 或 `dataSetIndex` 为 -1 撤消所有高亮。
- `getHighlighted()`: 返回一个 `Highlight[]` 其中包含所有高亮对象的信息，`xIndex` 和 `dataSetIndex`。 

以 Java 编程方式使得值高亮不会回调 `OnChartValueSelectedListener `。


## 选择回调

MPAndroidChart 提供了许多用于交互回调的方法，其中 `OnChartValueSelectedListener` 在点击高亮值时回调。

```java
public interface OnChartValueSelectedListener {
    /**
    * Called when a value has been selected inside the chart.
    *
    * @param e The selected Entry.
    * @param dataSetIndex The index in the datasets array of the data object
    * the Entrys DataSet is in.
    * @param h the corresponding highlight object that contains information
    * about the highlighted position
    */
    public void onValueSelected(Entry e, int dataSetIndex, Highlight h);
    /**
    * Called when nothing has been selected or an "un-select" has been made.
    */
    public void onNothingSelected();
}
```

让你的类实现该接口并设置对 chart 进行监听，即可接受回调。

Simply let your class that should receive the callbacks implement this interface and set it as a listener to the chart:

```java
chart.setOnChartValueSelectedListener(this);
```


## 手势回调

监听器 `OnChartGestureListener` 可以使得 chart 与手势操作进行交互：

```java
public interface OnChartGestureListener {

    /**
     * Callbacks when a touch-gesture has started on the chart (ACTION_DOWN)
     *
     * @param me
     * @param lastPerformedGesture
     */
    void onChartGestureStart(MotionEvent me, ChartTouchListener.ChartGesture lastPerformedGesture);

    /**
     * Callbacks when a touch-gesture has ended on the chart (ACTION_UP, ACTION_CANCEL)
     *
     * @param me
     * @param lastPerformedGesture
     */
    void onChartGestureEnd(MotionEvent me, ChartTouchListener.ChartGesture lastPerformedGesture);

    /**
     * Callbacks when the chart is longpressed.
     * 
     * @param me
     */
    public void onChartLongPressed(MotionEvent me);

    /**
     * Callbacks when the chart is double-tapped.
     * 
     * @param me
     */
    public void onChartDoubleTapped(MotionEvent me);

    /**
     * Callbacks when the chart is single-tapped.
     * 
     * @param me
     */
    public void onChartSingleTapped(MotionEvent me);

    /**
     * Callbacks then a fling gesture is made on the chart.
     * 
     * @param me1
     * @param me2
     * @param velocityX
     * @param velocityY
     */
    public void onChartFling(MotionEvent me1, MotionEvent me2, float velocityX, float velocityY);

   /**
     * Callbacks when the chart is scaled / zoomed via pinch zoom gesture.
     * 
     * @param me
     * @param scaleX scalefactor on the x-axis
     * @param scaleY scalefactor on the y-axis
     */
    public void onChartScale(MotionEvent me, float scaleX, float scaleY);

   /**
    * Callbacks when the chart is moved / translated via drag gesture.
    *
    * @param me
    * @param dX translation distance on the x-axis
    * @param dY translation distance on the y-axis
    */
    public void onChartTranslate(MotionEvent me, float dX, float dY);
}
```

让你的类实现该接口并设置对 chart 进行监听，即可接受回调：

```java
chart.setOnChartGestureListener(this);
```

设置了监听器后，chart 会根据你的 `setXXXEnable()` 进行放缩移动等操作。不用在接口方法里对图表进行放缩移动等其他操作，接口方法可以让你实现其他对应功能，比如说你要打印放缩是的 `ScaleX，ScaleY`: 

```java
@Override
public void onChartScale(MotionEvent me, float scaleX, float scaleY) {
    Log.i("Scale / Zoom", "ScaleX: " + scaleX + ", ScaleY: " + scaleY);
}
```

日志输出如下：

```java
I/Gesture: START
I/Scale / Zoom: ScaleX: 1.0, ScaleY: 1.0
I/Scale / Zoom: ScaleX: 1.0, ScaleY: 1.0
I/Scale / Zoom: ScaleX: 1.0174584, ScaleY: 1.0174584
I/Scale / Zoom: ScaleX: 1.240304, ScaleY: 1.240304
I/Scale / Zoom: ScaleX: 1.4446417, ScaleY: 1.4446417
I/Scale / Zoom: ScaleX: 1.5617653, ScaleY: 1.5617653
I/Scale / Zoom: ScaleX: 1.0241176, ScaleY: 1.0241176
I/Scale / Zoom: ScaleX: 1.1038365, ScaleY: 1.1038365
I/Gesture: END, lastGesture: PINCH_ZOOM
```

下面是练习时写的一些 `OnChartGestureListener` 接口实现方法：

```java
{
    @Override
    public void onChartGestureStart(MotionEvent me, ChartTouchListener.ChartGesture lastPerformedGesture) {
        Log.i("Gesture", "START");
    }

    @Override
    public void onChartGestureEnd(MotionEvent me, ChartTouchListener.ChartGesture lastPerformedGesture) {
        Log.i("Gesture", "END, lastGesture: " + lastPerformedGesture);

        // un-highlight values after the gesture is finished and no single-tap
        if (lastPerformedGesture != ChartTouchListener.ChartGesture.SINGLE_TAP)
            mChart.highlightValues(null); // or highlightTouch(null) for callback to onNothingSelected(...)
    }

    @Override
    public void onChartLongPressed(MotionEvent me) {
        Log.i("LongPress", "Chart longpressed.");
    }

    @Override
    public void onChartDoubleTapped(MotionEvent me) {
        Log.i("DoubleTap", "Chart double-tapped.");
    }

    @Override
    public void onChartSingleTapped(MotionEvent me) {
        Log.i("SingleTap", "Chart single-tapped.");
    }

    @Override
    public void onChartFling(MotionEvent me1, MotionEvent me2, float velocityX, float velocityY) {
        Log.i("Fling", "Chart flinged. VeloX: " + velocityX + ", VeloY: " + velocityY);
    }

    @Override
    public void onChartScale(MotionEvent me, float scaleX, float scaleY) {
        Log.i("Scale / Zoom", "ScaleX: " + scaleX + ", ScaleY: " + scaleY);
    }

    @Override
    public void onChartTranslate(MotionEvent me, float dX, float dY) {
        Log.i("Translate / Move", "dX: " + dX + ", dY: " + dY);
    }

    @Override
    public void onValueSelected(Entry e, int dataSetIndex, Highlight h) {
        Log.i("Entry selected", e.toString());
        Log.i("", "low: " + mChart.getLowestVisibleXIndex() + ", high: " + mChart.getHighestVisibleXIndex());
    }

    @Override
    public void onNothingSelected() {
        Log.i("Nothing selected", "Nothing selected.");
    }
}
```