# 处理文本输入

`TextInput` 是一个允许用户输入文本的基本组件，它有着一个 `onChangeText` 方法属性，每次文本发生变化时都会回调该方法，也有着一个 `onSubmitEditing` 方法属性，当文本被 “submit” 时会触发调用。

举个栗子，现在我们来时一个实时文本翻译小程序：把非空格的输入字符转换为 `1`，输入的空格则转换为 `0`。代码片段如下：
```js
export default class App extends Component<Props> {
  constructor(props) {
    super(props)
    this.state = {
      text: ''
    }
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.textff}>
          {this.state.text.split('').map(char => {
            return char === ' ' ? '0' : '1'
          }).join('')}
        </Text>
        <TextInput
          style={styles.textInput}
          placeholder='请输入你要翻译的字符'
          onChangeText={(text) => {
            this.setState({
              text: text
            })
          }}
        />
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
  textInput: {
    width: 300,
    height: 60,
    marginTop: 40,
    padding: 20,
    backgroundColor: 'yellow',
  }
});
```

![](./res/1.gif)

在上述示例中，我们将 `text` 存储在 `state` 中，因为它会随着用户的输入发生变化。通过文本输入我们可以做很多东西，比如说在用户输入的时候进行验证等。
