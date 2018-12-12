# 使用 Swift 开发 iOS 应用：将 UI 连接到代码 

在本课程中，你将把 FoodTracker 应用程序的基本用户界面（UI）连接到代码并定义用户可以在该 UI 中执行的一些操作。

<img src="./res/CUIC_sim_finalUI_2x.png" width="320" />

内容索引：
* [学习目标](#学习目标)
* [将 UI 连接到源代码](#将-ui-连接到源代码)
* [为 UI 元素创建插座](#为-ui-元素创建插座)
* [定义要执行的操作](#定义要执行的操作)
* [处理用户输入](#处理用户输入)
* [包起来](#包起来)


## 学习目标

在课程结束时，你将能够：
* 解释故事板中的场景与基础视图控制器之间的关系
* 在故事板和源代码中的 UI 元素之间创建插座和操作连接
* 处理来自文本字段的用户输入并在 UI 中显示结果
* 使类符合协议
* 了解委托模式
* 在设计应用程序架构时遵循目标操作模式


## 将 UI 连接到源代码

故事板中的 元素链接到源代码。了解故事板与你编写的代码之间的关系非常重要。

在故事板中，场景表示一个内容屏幕，通常是一个视图控制器。视图控制器实现应用程序的行为。视图控制器管理具有子视图层次结构的单个内容视图。视图控制器协调应用程序数据模型之间的信息流，该数据模型封装应用程序的数据，显示该数据的视图，管理其内容视图的生命周期，处理设备旋转时的方向更改，定义你的导航应用程序，并实现响应用户输入的行为。iOS 中的所有视图控制器对象都是类型 UIViewController 或其子类之一。

你可以通过创建和实现自定义视图控制器子类来定义视图控制器在代码中的行为。然后，你可以在故事板中的这些类和场景之间创建连接，以获取你在代码中定义的行为以及在故事板中定义的用户界面。

Xcode 已经创建了一个你之前看过的类 `ViewController.swift`，并将它连接到你现在在故事板中处理的场景。将来，当你添加更多场景时，你将在 Identity 检查器中自行建立此连接。该身份检查可以让你在故事板与该对象的身份，比如什么类的对象属于编辑对象的属性。

<img src="./res/CUIC_inspector_identity_2x.png" width="320" />

在运行时，你的故事板会创建 `ViewController` 自定义视图控制器子类的实例。故事板中的场景显示在设备的屏幕上，用户界面的行为定义在 `ViewController.swift`。

虽然场景已连接到 `ViewController.swift`，但这不是唯一需要进行的连接。要在应用程序中定义交互，你的视图控制器源代码需要能够与故事板中的视图进行通信。你可以通过在故事板中的视图和视图控制器源代码文件之间定义其他连接（称为插座和操作）来完成此操作。


## 为 UI 元素创建插座

[Outlets](https://developer.apple.com/library/archive/referencelibrary/GettingStarted/DevelopiOSAppsSwift/GlossaryDefinitions.html#//apple_ref/doc/uid/TP40015214-CH12-SW55) 提供了一种从源代码文件引用接口对象（即添加到故事板中的对象）的方法。要创建插座，请从故事板中的特定对象按住 Control 键拖动到视图控制器文件。此操作为视图控制器文件中的对象创建一个属性，该属性允许你在运行时从代码访问和操作该对象。

你需要在文本字段中创建出口并在用户界面中标记以便能够引用它们。

### 将文本字段连接到 ViewController.swift 代码

**1. 打开你的故事板 Main.storyboard。**

**2. 单击 Xco​​de 右上角附近的 Xcode 工具栏中的“助手”按钮以打开助理编辑器。**

<img src="./res/assistant_editor_toggle_2x.png" width="320" />

**3. 如果需要更多空间，可以通过单击 Xco​​de 工具栏中的“导航器和实用程序”按钮来折叠项目导航器和实用程序区域。**

<img src="./res/navigator_utilities_toggle_on_2x.png" width="320" />

你也可以折叠大纲视图。

**4. 在助理编辑器顶部显示的编辑器选择器栏中，将助理编辑器从“预览”更改为“自动”> `ViewController.swift`。**

![](./res/CUIC_switchtoviewcontroller_2x.png)

ViewController.swift 显示在右侧的编辑器中。

**5. 在ViewController.swift，找到该class行，它应该是这样的：**

```swift 
class ViewController: UIViewController {
```

**6. 在该 `class` 行下方添加以下内容：**

```swift 
//MARK: Properties
```

你刚刚在源代码中添加了注释。回想一下，注释是源代码文件中的一段文本，它不会作为程序的一部分进行编译，而是提供有关各个代码段的上下文或有用信息。

以字符开头的注释 `//MARK:` 是一种特殊类型的注释，用于组织代码并帮助你（以及读取代码的任何其他人）浏览它。你稍后会看到这个。具体来说，你添加的注释表明这是列出属性的代码部分。

**7. 在故事板中，选择文本字段。**

**8. 按住Ctrl键并从画布上的文本字段拖动到右侧编辑器中的代码显示，停止在刚刚添加的注释下方的行拖动 ViewController.swift。**

![](./res/CUIC_textfield_dragoutlet_2x.png)

**9. 在出现的对话框中，键入名称 nameTextField。保留其余选项。**

![](./res/CUIC_textfield_addoutlet_2x.png)

**10. 单击连接。**

Xcode 添加必要的代码来 ViewController.swift 存储对文本字段的引用，并配置故事板以设置该连接。

```swift
@IBOutlet weak var nameTextField: UITextField!
```

花一点时间来了解这行代码中发生了什么。

该 `IBOutlet` 属性告诉 Xcode 你可以 `nameTextField` 从 Interface Builder 连接到该属性（这就是该属性具有 `IB` 前缀的原因）。该 `weak` 关键字指示该参考文献没有阻止系统解除分配被引用对象。弱引用有助于防止参考周期; 但是，要保持对象存活并且在内存中，你需要确保应用程序的其他部分具有对该对象的强引用。在这种情况下，它是文本字段的超级视图。superview 保持对其所有子视图的强引用。只要超级视图保持活力并且在内存中，所有子视图也保持活跃状态​​。类似地，视图控制器具有对其内容视图的强引用 - 使整个视图层次结构保持活动并在内存中。

声明的其余部分定义了一个名为的隐式未包装的可选变量。请注意类型声明末尾的感叹号。此感叹号表示该类型是隐式展开的可选项，它是一个可选类型，在首次设置后始终具有值。当你访问隐式展开的可选项时，系统会假定它具有有效值并自动为你解包。请注意，如果尚未设置变量的值，则会导致应用程序终止。 

从故事板加载视图控制器时，系统会实例化视图层次结构并将适当的值分配给所有视图控制器的出口。在 `viewDidLoad()` 调用视图控制器的方法时，系统已为所有控制器的插座分配了有效值，你可以安全地访问其内容。

现在，将标签连接到你的代码，方法与连接文本字段的方式相同。

### 将标签连接到 ViewController.swift 代码

**1. 在故事板中，选择标签。**

**2. 按住 Ctrl 键从画布上的标签拖动到右侧编辑器中的代码显示，停止在 nameTextField 属性下方的行拖动 ViewController.swift。**

![](./res/CUIC_label_dragoutlet_2x.png)

**3. 在出现的对话框中，键入名称 mealNameLabel。保留其余选项。**

![](./res/CUIC_label_addoutlet_2x.png)

**4. 单击连接。**

同样，Xcode 添加必要的代码来 `ViewController.swift` 存储对标签的引用，并配置故事板以设置该连接。此出口类似于文本字段，除了其名称和类型（`UILabel` 与故事板中的对象类型相匹配）。

```swift 
@IBOutlet weak var mealNameLabel: UILabel!
```

如果你计划从接口对象访问值或修改代码中的接口对象，则只需要接口对象的插座。在这种情况下，你需要设置文本字段的 `delegate` 属性并设置标签的text属性。你不会修改按钮，因此没有理由为它创建插座。

Outlets 允许你在代码中引用你的界面元素，但是当用户与元素交互时，你仍然需要一种方式来响应。这就是行动的来源。


## 定义要执行的操作

iOS应用程序基于事件驱动编程。也就是说，应用程序的流程由事件决定：系统事件和用户操作。用户在界面中执行触发应用程序中的事件的操作。这些事件导致应用程序逻辑的执行和对其数据的操纵。然后，应用程序对用户操作的响应将反映在用户界面中。因为用户而不是开发人员可以控制应用程序代码的某些部分何时执行，所以你希望确切地识别用户可以执行哪些操作以及响应这些操作会发生什么。

的动作（或动作方法）是一段代码已连结可以发生在你的应用程序的事件。当该事件发生时，系统执行动作的代码。你可以定义一个操作方法来完成从操作数据到更新用户界面的任何操作。你可以使用操作来驱动应用程序流以响应用户或系统事件。

你可以像创建插座一样创建操作：按住 - 从故事板中的特定对象拖动到视图控制器文件。此操作在视图控制器文件中创建一个方法，当用户与附加操作方法的对象进行交互时，该方法会被触发。

首先创建一个简单的操作，`Default Text` 只要用户点击“设置默认文本”按钮，就会将标签设置为。（将标签设置为文本字段中文本的代码更复杂，因此你将在“ 处理用户输入”部分中编写该代码。）

### 在 ViewController.swift 代码中创建 setDefaultLabelText 操作

**1. 在 `ViewController.swift` 最后一个花括号（`}`）的上方，添加以下内容：**

```swift 
//MARK: Actions
```

此注释表示这是代码中列出操作的部分。

**2. 在故事板中，选择“设置默认标签文本”按钮。**

**3. 按住Control键并从画布上的“设置默认标签文本”按钮拖动到右侧编辑器中的代码显示，停止在刚刚添加的注释下方的行拖动 `ViewController.swift`。**

![](./res/CUIC_button_dragaction_2x.png)

**4. 在出现的对话框中，对于Connection选择Action。**

**5. 对于名称，请键入 `setDefaultLabelText`。**

**6. 对于类型，请选择 `UIButton`。**

你可能已经注意到 Type 字段的值默认为 `AnyObject`。在 Swift 中，`AnyObject` 是一种用于描述可以属于任何类的对象的类型。指定此操作方法的类型 `UIButton` 意味着只有按钮对象可以连接到此操作。虽然这对你现在正在创作的动作并不重要，但重要的是要记住以后再做。

保留其余选项。

![](./res/CUIC_button_addaction_2x.png)

**7. 单击连接。**

Xcode 添加了必要的代码来 `ViewController.swift` 设置 action 方法。

```swift 
@IBAction func setDefaultLabelText(_ sender: UIButton) {
}
```

该 `sender` 参数指的是负责触发操作的对象 - 在本例中是一个按钮。该 `IBAction` 属性表示该方法是你可以从 Interface Builder 中的故事板连接到的操作。声明的其余部分通过名称声明一个方法 `setDefaultLabelText(_:)`。

现在，方法声明为空。重置标签值的代码非常简单。


### 在 ViewController 代码中实现标签重置操作

**1. 在 `ViewController.swift`，找到 `setDefaultLabelText` 刚刚添加的操作方法。**

**2. 在方法实现中，在花括号（`{}`）之间添加以下代码行：**

```swift
mealNameLabel.text = "Default Text"
```

正如你可能猜到的，此代码将标签的 text 属性设置为默认文本。

请注意，你不必指定默认文本的类型，因为Swift的类型推断可以看到你正在分配某种类型String并且可以正确推断类型。

iOS 会为你处理所有重绘代码，因此这实际上是你现在需要编写的所有代码。你的 `setDefaultLabelText(_:)` 操作方法应如下所示：

```swift
@IBAction func setDefaultLabelText(_ sender: UIButton) {
    mealNameLabel.text = "Default Text"
}
```

检查点：通过运行模拟器测试你的更改。单击“设置默认标签文本”按钮时，将 `setDefaultLabelText(_:)` 调用你的方法，并且 `mealNameLabel` 对象的 `text` 值将从 `Meal Name`（故事板中设置的值）更改为（操作设置 `Default Text` 的值）。你应该会在用户界面中看到更改。

<img src="./res/CUIC_sim_defaulttext_2x.png" width="320" />

虽然将餐的名称更改为“默认文本”并不是特别有用，但它确实说明了重要的一点。你刚刚实现的行为是iOS应用程序设计中的目标操作模式的示例。目标操作是一种设计模式，其中一个对象在发生特定事件时将消息发送到另一个对象。

在这种情况下：
* 该事件是用户点击“设置默认文本”按钮。
* 行动是 `setDefaultLabelText(_)`。
* 目标是 `ViewController`（定义了操作方法）。
* 发件人是“设置默认标签文本”按钮。

系统通过调用目标上的 action 方法并传入 sender 对象来发送消息。发件人通常是一个控件 - 例如按钮，滑块或开关 - 可以触发事件以响应用户交互，例如点击，拖动或值更改。这种模式在 iOS 应用程序编程中非常常见，你将在整个课程中看到更多内容。


## 处理用户输入

此时，用户可以将膳食名称标签重置为默认值，但你确实希望让用户使用文本字段输入他们自己的膳食名称。为了简单起见，只要用户在文本字段中输入文本并点击 Return ，你就会更新 `mealNameLabel` 对象的 `text` 值。

当你处理从文本字段接受用户输入时，你需要来自文本字段委托的一些帮助。甲代表是一种作用于代，或与另一个对象的协调的对象。委托对象（在本例中为text字段）保留对另一个对象（委托）的引用，并在适当的时候，委托对象向委托发送消息。该消息告诉委托有关委托对象即将处理或刚刚处理的事件。代理可以通过例如更新应用程序中自身或其他对象的外观或状态，或返回影响如何处理即将发生的事件的值来做出响应。

当用户正在编辑文本时，文本字段的委托与文本字段通信，并且知道何时发生重要事件 - 例如当用户开始或停止编辑文本时。代理可以使用此信息在适当的时间保存或清除数据，关闭键盘等。

任何对象都可以充当另一个对象的委托，只要它符合适当的协议即可。调用定义文本字段委托的协议 `UITextFieldDelegate`。将视图控制器作为它管理的对象的委托是很常见的。在这种情况下，你将使你的 `ViewController` 实例成为文本字段的委托。

首先，`ViewController` 需要采用 `UITextFieldDelegate` 协议。你通过将协议列为类声明行的一部分来采用协议。

### 采用 UITextFieldDelegate 协议

**1. 如果助理编辑器已打开，请单击“标准”按钮返回标准编辑器。**

<img src="./res/standard_toggle_2x.png" width="320" />

**2. 单击Xco​​de工具栏中的“导航器和实用程序”按钮，展开项目导航器和实用程序区域。**

**3. 在项目导航器中，选择 `ViewController.swift`。**

**4. 在 `ViewController.swift`，找到该 `class` 行，它应该是这样的：**

```swift
class ViewController: UIViewController {
```

**5. 之后UIViewController，添加一个逗号（,）并UITextFieldDelegate采用该协议。**

```swift
class ViewController: UIViewController, UITextFieldDelegate {
```

通过采用该 `UITextFieldDelegate` 协议，你告诉编译器ViewController该类可以充当有效的文本字段委托。这意味着你可以实现协议的方法来处理文本输入，并且可以将ViewController类的实例指定为文本字段的委托。


### 将 ViewController 对象设置为其 nameTextField 属性的委托

**1. 在 `ViewController.swift`，找到 `viewDidLoad()` 方法，它应该是这样的：**

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
}
```

此方法的模板实现包括注释。你在方法实现中不需要此注释，因此请继续并删除它。

**2. 在该 `super.viewDidLoad()` 行下方添加一个空行和以下内容：**

```swift
// Handle the text field’s user input through delegate callbacks.
nameTextField.delegate = self
```

该 `self` 指 `ViewController` 类，因为它的范围内引用的 `ViewController` 类定义。

你可以添加自己的注释，以帮助你了解代码中发生的情况。

你的 `viewDidLoad()` 方法应如下所示：

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    // Handle the text field’s user input through delegate callbacks.
    nameTextField.delegate = self
}
```

当一个 `ViewController` 实例被加载时，它本身将作为其委托 `nameTextField` 财产。

该 `UITextFieldDelegate` 协议定义了八种可选方法。只需实现你需要的那些来获得你想要的行为。目前，你需要实现以下两种方法：

```swift
func textFieldShouldReturn(_ textField: UITextField) -> Bool
func textFieldDidEndEditing(_ textField: UITextField)
```

要了解何时调用这些方法以及需要执行哪些操作，了解文本字段如何响应用户事件非常重要。当用户点击文本字段时，它会自动成为第一个响应者。在应用程序中，第一个响应者是第一个在线上用于接收多种应用事件的对象，包括关键事件，运动事件和动作消息等。换句话说，用户生成的许多事件最初被路由到第一响应者。

由于文本字段成为第一个响应者，iOS显示键盘并开始该文本字段的编辑会话。使用该键盘的用户键入的内容将插入到文本字段中。

当用户想要完成文本字段的编辑时，文本字段需要重新签名其第一响应者状态。由于文本字段将不再是应用程序中的活动对象，因此事件需要路由到更合适的对象。

这是你的 `UITextFieldDelegate` 方法实现的用武之地。你需要指定当用户点击按钮以在文本字段中结束编辑时，文本字段应该重新签名其第一响应者状态。你可以在 `textFieldShouldReturn(_:)` 方法中执行此操作，当用户在键盘上点击Return（或在本例中为Done）时会调用该方法。


### 实现 UITextFieldDelegate 协议方法 textFieldShouldReturn（_ :)

**1. 在 `ViewController.swift` 该 `//MARK: Actions` 部分正上方，添加以下内容：**

```swift
//MARK: UITextFieldDelegate
```

此注释用于组织代码并帮助你（以及任何阅读代码的人）浏览它。

到目前为止，你已添加了其中一些评论。Xcode将这些注释中的每一个列为源代码文件的“ 功能”菜单中的节标题，如果单击编辑器区域顶部的文件名，则会显示该标题。使用“功能”菜单可以快速跳转到代码中的某个部分。你会注意到你 `//MARK:` 在这里列出的部分。你可以单击其中一个部分标题以跳转到该文件中的该部分。

![](./res/CUIC_functionsmenu_2x.png)

**2. 在评论下方，添加以下方法：**

```swift
func textFieldShouldReturn(_ textField: UITextField) -> Bool {
}
```

**3. 在此方法中，添加以下代码以重新签名文本字段的第一响应者状态，并添加注释以描述代码的作用：**

```swift
// Hide the keyboard.
textField.resignFirstResponder()
```

尝试键入第二行而不是仅复制和粘贴。你会发现代码完成是Xcode的一个很好的省时功能。当Xcode显示可能的完成列表时，滚动列表直到找到所需的完成，然后按Return键。Xcode为你插入整行。

![](./res/CUIC_code_completion_2x.png)

**4. 在此方法中，添加以下代码行：**

```swift
return true
```

此方法返回一个布尔值，指示系统是否应处理按Return键的操作。在这种情况下，你总是希望响应用户按下 Return 键，所以只需返回 `true`。

你的 `textFieldShouldReturn(_:)` 方法应如下所示：

```swift
func textFieldShouldReturn(_ textField: UITextField) -> Bool {
    // Hide the keyboard.
    textField.resignFirstResponder()
    return true
}
```

`textFieldDidEndEditing(_:)` 在文本字段重新启动其第一响应者状态后调用 你需要实现的第二种方法。由于你将第一响应者状态重新签名 `textFieldShouldReturn`，系统在调用后立即调用此方法 `textFieldShouldReturn`。

`该textFieldDidEndEditing(_:)` 方法使你有机会阅读输入到文本字段中的信息并对其执行某些操作。在你的情况下，你将获取文本字段中的文本并使用它来更改标签的值。


### 实现 UITextFieldDelegate 协议方法 textFieldDidEndEditing（_ :)

**1. 在 `ViewController.swift` 该 `textFieldShouldReturn(_:)` 方法之后，添加以下方法：**

```swift
func textFieldDidEndEditing(_ textField: UITextField) {
}
```

**2. 在此方法中，添加以下代码行：**

```swift
mealNameLabel.text = textField.text
```

这就是你需要做的就是看到结果。你的 `textFieldDidEndEditing(_:)` 方法应如下所示：

```swift
func textFieldDidEndEditing(_ textField: UITextField) {
    mealNameLabel.text = textField.text
}
```

检查点：通过运行模拟器测试你的更改。你可以选择文本字段并在其中键入文本。单击键盘上的“完成”按钮时，键盘将被关闭，标签文本将更改为在文本字段中显示文本。单击“设置默认标签文本”按钮时，标签会从标签中当前显示的内容更改为 `Default Text`（你之前定义的操作设置的值）。

<img src="./res/CUIC_sim_finalUI_2x.png" width="320" />

## 包起来

在本课程中，你已使用助理编辑器向源代码添加插座和操作。当用户与控件交互时，你还添加了用于更新用户界面的代码。该项目仍然只是一个相对简单的单一场景，但你将继续添加功能并增加其余课程的复杂性。