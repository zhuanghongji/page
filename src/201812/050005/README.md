# Switch

一个跨平台通用的可以在两个状态中切换的组件。

注意：这是一个“受控组件”（controlled component）。
* 你必须使用 `onValueChange` 回调来更新 `value` 属性以响应用户的操作。
* 如果不更新 `value` 属性，组件只会按一开始给定的 `value` 值来渲染且保持不变，看上去就像完全点不动。

## 示例
```js
export default class App extends Component<Props> {
  constructor(props) {
    super(props)
    this.state = {
      value: false,
    }
  }

  render() {
    return (
      <View style={styles.container}>
        <View style={styles.row}>
          <Text style={styles.text}>正常状态</Text>
          <Switch/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>disabled true</Text>
          <Switch disabled={true}/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>thumbTintColor</Text>
          <Switch thumbTintColor='red'/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>tintColor</Text>
          <Switch tintColor='blue'/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>onTintColor</Text>
          <Switch onTintColor='orange'/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>{this.state.value ? "开" : "关"}</Text>
          <Switch
            value = {this.state.value}
            onValueChange={(value) => {
              this.setState({
                value: value
              })
            }}
          />
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
  row: {
    flexDirection: 'row',
    justifyContent: 'flex-start',
    alignItems: 'center',
    margin: 16,
  },
  text: {
    margin: 16,
  }
});
```

| iOS | Android
| -- | --
| ![](./res/ios.gif) | ![](./res/android.gif)

## 参数

### `disabled`
如果为 `true`，用户将不可以改变其开关状态。默认为 `false`。

### `onTintColor`
当处于「开」状态时，该组件的背景色。

### `onValueChange`
当开关状态发生变化时，将会回调的方法。

### `testID`

### `thumbTintColor`
「滑块」的颜色。If this is set on iOS, the switch grip will lose its drop shadow.

### `tintColor`
边框的颜色。
* iOS 平台：指的是组件的边缘颜色。
* Android 平台：指的是组件处于「关」状态时的背景色。

### `value`
组件的开关状态。如果为 `true` 组件将会显示为「开」状态，默认为 `false`。
