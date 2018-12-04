# RefreshControl

这一组件可以用在 `ScrollView` 或 `FlatList` **内部**，为其添加下拉刷新的功能。当 `ScrollView` 处于竖直方向的起点位置（scrollY: 0），此时下拉会触发一个 `onRefresh` 事件。

### 示例

```javascript
class RefreshableList extends Component {
  constructor(props) {
    super(props);
    this.state = {
      refreshing: false,
    };
  }

  _onRefresh = () => {
    this.setState({refreshing: true});
    fetchData().then(() => {
      this.setState({refreshing: false});
    });
  }

  render() {
    return (
      <FlatList
        refreshControl={
          <RefreshControl
            refreshing={this.state.refreshing}
            onRefresh={this._onRefresh}
          />
        }
        ...
      />
    );
  }
  ...
}
```

**注意：** `refreshing`是一个受控属性， 所以必须在`onRefresh`函数中设置为true，否则loading指示器会立即停止。

示例效果如下 (示例代码大致如上，略)：

| iOS | Android
| -- | --
| ![](./res/refresh-ios.gif) | ![](./res/refresh-android.gif)

### 查看Props

* View props...

- [`refreshing`](#refreshing)
- [`onRefresh`](#onrefresh)
- [`colors`](#colors)
- [`enabled`](#enabled)
- [`progressBackgroundColor`](#progressbackgroundcolor)
- [`progressViewOffset`](#progressviewoffset)
- [`size`](#size)
- [`tintColor`](#tintcolor)
- [`title`](#title)
- [`titleColor`](#titlecolor)

---

# 文档

## Props

### `refreshing`

视图是否应该在刷新时显示指示器。

| 类型 | 必填 |
| ---- | ---- |
| bool | 是   |

---

### `onRefresh`

在视图开始刷新时调用。

| 类型     | 必填 |
| -------- | ---- |
| function | 否   |

---

### `colors`

指定至少一种颜色用来绘制刷新指示器。

| 类型                        | 必填 | 平台    |
| --------------------------- | ---- | ------- |
| array of [color](colors.md) | 否   | Android |

---

### `enabled`

指定是否要启用刷新指示器。

| 类型 | 必填 | 平台    |
| ---- | ---- | ------- |
| bool | 否   | Android |

---

### `progressBackgroundColor`

指定刷新指示器的背景色。

| 类型               | 必填 | 平台    |
| ------------------ | ---- | ------- |
| [color](colors.md) | 否   | Android |

---

### `progressViewOffset`

指定刷新指示器的垂直起始位置(top offset)。

| 类型   | 必填 | 平台    |
| ------ | ---- | ------- |
| number | 否   | Android |

---

### `size`

指定刷新指示器的大小，具体数值可参阅 RefreshControl.SIZE.

| 类型                                                                   | 必填 | 平台    |
| ---------------------------------------------------------------------- | ---- | ------- |
| enum(RefreshLayoutConsts.SIZE.DEFAULT, RefreshLayoutConsts.SIZE.LARGE) | 否   | Android |

---

### `tintColor`

指定刷新指示器的颜色。

| 类型               | 必填 | 平台 |
| ------------------ | ---- | ---- |
| [color](colors.md) | 否   | iOS  |

---

### `title`

指定刷新指示器下显示的文字。

| 类型   | 必填 | 平台 |
| ------ | ---- | ---- |
| string | 否   | iOS  |

---

### `titleColor`

指定刷新指示器下显示的文字的颜色。

| 类型               | 必填 | 平台 |
| ------------------ | ---- | ---- |
| [color](colors.md) | 否   | iOS  |
