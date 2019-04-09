# MPAndroidChart 笔记：修改视窗

## 概述

MPAndroidChart 图表具有多种不同的方法来修改视窗 (图表上那些是可见的，以及 aim of the view)。 注意，这些方法仅适用于 `LineChart`、`BarChart`、`ScatterChart` 和 `CandleStickChart`。

> 请注意：修改视口的所有方法需要在 **为Chart 设置数据之后** 调用。

## 什么是可见的

- `setVisibleXRangeMinimum(float minXRange)`: 设定x轴最大可见区域范围的大小。如果设置为17，则不可能进一步放大视图 (在x轴超过17的值)。
  
![]()  ![]()

```java
// 上面左图不做任何处理
// 上面右图：
chart.setVisibleXRangeMinimum(17);
```

- `setVisibleXRangeMaximum(float maxXRange)`: 设定x轴最大可见区域范围的大小。 如果设定为3，则在x轴超过3的值被视为不可见 (不滑动 chart 的话)。
  
![]() ![]()

```java
chart.setVisibleXRangeMaximum(3); // 效果如上面左图

// 这时你会发现可以移动 chart 了
// 手指稍微左滑下效果图如上面右图
```

- `setVisibleYRangeMaximum(float maxYRange, AxisDependency axis)`: 设定y轴最大可见区域范围的大小。您还需要提供要对应的轴 (`YAxis.AxisDependency.LEFT` 或 `YAxis.AxisDependency.RIGHT`)
  
![]() ![]()

```java
// 上面左效果图对应代码
chart.setVisibleYRangeMaximum(50f,YAxis.AxisDependency.LEFT);

// 上面右效果图对应代码
chart.setVisibleYRangeMaximum(70f,YAxis.AxisDependency.LEFT);
```

- `setViewPortOffsets(float left, float top, float right, float bottom)`: 设置当前视图的偏移量 (实际图表窗口的两侧偏移量)。 设置这个，将阻止图表自动计算它的偏移量。使用 `resetViewPortOffsets()` 撤消此设置。
  
![]() ![]()
![]() ![]()

```java   
chart.setViewPortOffsets(50,0,0,0);     // 图1
chart.setViewPortOffsets(0,50,0,0);     // 图2
chart.setViewPortOffsets(50,50,0,0);    // 图3
chart.setViewPortOffsets(50,50,50,50);  // 图4
```

- `resetViewPortOffsets()`: 撤销所有通过 `setViewPortOffsets(...)` 方法设置的偏移量 。 允许图表再次自动计算所有偏移。
- `setExtraOffsets(float left, float top, float right, float bottom)`: 设置额外的偏移，将被添加到自动计算的偏移。 这不会改变自动计算的偏移量，但增加了额外的空间给它们。 

## 移动视图 (它的目的)

- `fitScreen()`: 重设所有缩放和拖动，使图表完全适合它的边界 (完全缩小)。
- `moveViewToX(float xIndex)`: 将当前视口的左侧 (边)到指定的 x 值。
  
![]() ![]()

```java
chart.setVisibleXRangeMaximum(3);

chart.moveViewToX(2);  // 上面左效果图,Mar是三月
chart.moveViewToX(5);  // 上面右效果图,Jun是六月
```

- `moveViewToY(float yValue, AxisDependency axis)`: 使得指定的y值显示在对应y轴( 左or右 )的中间。
  
![]() ![]()

```java
YAxis leftAxis = chart.getAxisLeft();
leftAxis.setTextSize(18f);
chart.setVisibleYRangeMaximum(50f,YAxis.AxisDependency.LEFT);

chart.moveViewToY(40,YAxis.AxisDependency.LEFT); // 上面左效果图
chart.moveViewToY(40,YAxis.AxisDependency.LEFT); // 上面右效果图
```

- `moveViewTo(float xIndex, float yValue, AxisDependency axis)`: 相当于`setVisibleXRange(...)` 和 `setVisibleYRange(...)` 组合使用。
- `centerViewTo(int xIndex, float yValue, AxisDependency axis)`: 相当于 `setVisibleXRange(...)` 和 `setVisibleYRange(...) ` 组合使用。

注：所有 `moveViewTo(...)` 方法会自动 `invalidate()` 刷新图表。没有必要进行进一步调用 `invalidate()`。


## 变焦 (编程)

- `zoomIn()`: Zooms in by 1.4f, into the charts center.
- `zoomOut()`: Zooms out by 0.7f, from the charts center.
- `zoom(float scaleX, float scaleY, float x, float y)`: 根据所给的参数进行放大或缩小。 参数 `x` 和 `y` 是变焦中心的坐标 (单位：像素)。 记住，**1f = 无放缩**。 
  

## 完整示例

```java
chart.setData(...); // first set data

// now modify viewport
// allow 20 values to be displayed at 
once on the x-axis, not more
chart.setVisibleXRangeMaximum(20); 
// set the left edge of the chart to x-index 10
chart.moveViewToX(10); 

// moveViewToX(...) also calls invalidate()
```