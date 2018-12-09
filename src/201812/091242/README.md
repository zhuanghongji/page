# 使用 Swift 开发 iOS 应用：将 UI 连接到代码 

将UI连接到代码
在本课程中，您将把FoodTracker应用程序的基本用户界面（UI）连接到代码并定义用户可以在该UI中执行的一些操作。

![](./res/CUIC_sim_finalUI_2x.png)
学习目标
在课程结束时，您将能够：

解释故事板中的场景与基础视图控制器之间的关系

在故事板和源代码中的UI元素之间创建插座和操作连接

处理来自文本字段的用户输入并在UI中显示结果

使类符合协议

了解委托模式

在设计应用程序架构时遵循目标操作模式

将UI连接到源代码
故事板中的 元素链接到源代码。了解故事板与您编写的代码之间的关系非常重要。

在故事板中，场景表示一个内容屏幕，通常是一个视图控制器。视图控制器实现应用程序的行为。视图控制器管理具有子视图层次结构的单个内容视图。视图控制器协调应用程序数据模型之间的信息流，该数据模型封装应用程序的数据，显示该数据的视图，管理其内容视图的生命周期，处理设备旋转时的方向更改，定义您的导航应用程序，并实现响应用户输入的行为。iOS中的所有视图控制器对象都是类型UIViewController或其子类之一。

您可以通过创建和实现自定义视图控制器子类来定义视图控制器在代码中的行为。然后，您可以在故事板中的这些类和场景之间创建连接，以获取您在代码中定义的行为以及在故事板中定义的用户界面。

Xcode已经创建了一个你之前看过的类ViewController.swift，并将它连接到你现在在故事板中处理的场景。将来，当您添加更多场景时，您将在Identity检查器中自行建立此连接。该身份检查可以让你在故事板与该对象的身份，比如什么类的对象属于编辑对象的属性。

![](./res/CU_inspector_identity_2x.png)
在运行时，您的故事板会创建ViewController自定义视图控制器子类的实例。故事板中的场景显示在设备的屏幕上，用户界面的行为定义在ViewController.swift。

虽然场景已连接到ViewController.swift，但这不是唯一需要进行的连接。要在应用程序中定义交互，您的视图控制器源代码需要能够与故事板中的视图进行通信。您可以通过在故事板中的视图和视图控制器源代码文件之间定义其他连接（称为插座和操作）来完成此操作。

为UI元素创建插座
Outlets提供了一种从源代码文件引用接口对象（即添加到故事板中的对象）的方法。要创建插座，请从故事板中的特定对象按住Control键拖动到视图控制器文件。此操作为视图控制器文件中的对象创建一个属性，该属性允许您在运行时从代码访问和操作该对象。

您需要在文本字段中创建出口并在用户界面中标记以便能够引用它们。

将文本字段连接到ViewController.swift代码

打开你的故事板Main.storyboard。

单击Xco​​de右上角附近的Xcode工具栏中的“助手”按钮以打开助理编辑器。

![](./res/assistant_editor_toggle_2x.png)
如果需要更多空间，可以通过单击Xco​​de工具栏中的“导航器和实用程序”按钮来折叠项目导航器和实用程序区域。

![](./res/navigator_utilities_toggle_on_2x.png)
您也可以折叠大纲视图。

在助理编辑器顶部显示的编辑器选择器栏中，将助理编辑器从“预览”更改为“自动”> ViewController.swift。

![](./res/CU_witchtoviewcontroller_2x.png)
ViewController.swift 显示在右侧的编辑器中。

在ViewController.swift，找到该class行，它应该是这样的：

class ViewController: UIViewController {
在该class行下方添加以下内容：

//MARK: Properties
您刚刚在源代码中添加了注释。回想一下，注释是源代码文件中的一段文本，它不会作为程序的一部分进行编译，而是提供有关各个代码段的上下文或有用信息。

以字符开头的注释//MARK:是一种特殊类型的注释，用于组织代码并帮助您（以及读取代码的任何其他人）浏览它。你稍后会看到这个。具体来说，您添加的注释表明这是列出属性的代码部分。

在故事板中，选择文本字段。

按住Ctrl键并从画布上的文本字段拖动到右侧编辑器中的代码显示，停止在刚刚添加的注释下方的行拖动ViewController.swift。

![](./res/CU_textfield_dragoutlet_2x.png)
在出现的对话框中，键入名称nameTextField。保留其余选项。

![](./res/CU_textfield_addoutlet_2x.png)
单击连接。

Xcode添加必要的代码来ViewController.swift存储对文本字段的引用，并配置故事板以设置该连接。

@IBOutlet weak var nameTextField: UITextField!
花一点时间来了解这行代码中发生了什么。

该IBOutlet属性告诉Xcode您可以nameTextField从Interface Builder 连接到该属性（这就是该属性具有IB前缀的原因）。该weak关键字指示该参考文献没有阻止系统解除分配被引用对象。弱引用有助于防止参考周期; 但是，要保持对象存活并且在内存中，您需要确保应用程序的其他部分具有对该对象的强引用。在这种情况下，它是文本字段的超级视图。superview保持对其所有子视图的强引用。只要超级视图保持活力并且在内存中，所有子视图也保持活跃状态​​。类似地，视图控制器具有对其内容视图的强引用 - 使整个视图层次结构保持活动并在内存中。

声明的其余部分定义了一个名为的隐式未包装的可选变量。请注意类型声明末尾的感叹号。此感叹号表示该类型是隐式展开的可选项，它是一个可选类型，在首次设置后始终具有值。当您访问隐式展开的可选项时，系统会假定它具有有效值并自动为您解包。请注意，如果尚未设置变量的值，则会导致应用程序终止。 UITextFieldnameTextField

从故事板加载视图控制器时，系统会实例化视图层次结构并将适当的值分配给所有视图控制器的出口。在viewDidLoad()调用视图控制器的方法时，系统已为所有控制器的插座分配了有效值，您可以安全地访问其内容。

现在，将标签连接到您的代码，方法与连接文本字段的方式相同。

将标签连接到ViewController.swift代码

在故事板中，选择标签。

按住Ctrl键从画布上的标签拖动到右侧编辑器中的代码显示，停止在nameTextField属性下方的行拖动ViewController.swift。

![](./res/CU_label_dragoutlet_2x.png)
在出现的对话框中，键入名称mealNameLabel。保留其余选项。

![](./res/CU_label_addoutlet_2x.png)
单击连接。

同样，Xcode添加必要的代码来ViewController.swift存储对标签的引用，并配置故事板以设置该连接。此出口类似于文本字段，除了其名称和类型（UILabel与故事板中的对象类型相匹配）。

@IBOutlet weak var mealNameLabel: UILabel!
如果您计划从接口对象访问值或修改代码中的接口对象，则只需要接口对象的插座。在这种情况下，您需要设置文本字段的delegate属性并设置标签的text属性。您不会修改按钮，因此没有理由为它创建插座。

Outlets允许您在代码中引用您的界面元素，但是当用户与元素交互时，您仍然需要一种方式来响应。这就是行动的来源。

定义要执行的操作
iOS应用程序基于事件驱动编程。也就是说，应用程序的流程由事件决定：系统事件和用户操作。用户在界面中执行触发应用程序中的事件的操作。这些事件导致应用程序逻辑的执行和对其数据的操纵。然后，应用程序对用户操作的响应将反映在用户界面中。因为用户而不是开发人员可以控制应用程序代码的某些部分何时执行，所以您希望确切地识别用户可以执行哪些操作以及响应这些操作会发生什么。

的动作（或动作方法）是一段代码已连结可以发生在你的应用程序的事件。当该事件发生时，系统执行动作的代码。您可以定义一个操作方法来完成从操作数据到更新用户界面的任何操作。您可以使用操作来驱动应用程序流以响应用户或系统事件。

您可以像创建插座一样创建操作：按住 - 从故事板中的特定对象拖动到视图控制器文件。此操作在视图控制器文件中创建一个方法，当用户与附加操作方法的对象进行交互时，该方法会被触发。

首先创建一个简单的操作，Default Text只要用户点击“设置默认文本”按钮，就会将标签设置为。（将标签设置为文本字段中文本的代码更复杂，因此您将在“ 处理用户输入”部分中编写该代码。）

在ViewController.swift代码中创建setDefaultLabelText操作

在ViewController.swift最后一个花括号（}）的上方，添加以下内容：

//MARK: Actions
此注释表示这是代码中列出操作的部分。

在故事板中，选择“设置默认标签文本”按钮。

按住Control键并从画布上的“设置默认标签文本”按钮拖动到右侧编辑器中的代码显示，停止在刚刚添加的注释下方的行拖动ViewController.swift。

![](./res/CUIC_button_dragaction_2x.png)
在出现的对话框中，对于Connection选择Action。

对于名称，请键入setDefaultLabelText。

对于类型，请选择UIButton。

您可能已经注意到Type字段的值默认为AnyObject。在Swift中，AnyObject是一种用于描述可以属于任何类的对象的类型。指定此操作方法的类型UIButton意味着只有按钮对象可以连接到此操作。虽然这对你现在正在创作的动作并不重要，但重要的是要记住以后再做。

保留其余选项。

![](./res/CUIC_button_addaction_2x.png)
单击连接。

Xcode添加了必要的代码来ViewController.swift设置action方法。

@IBAction func setDefaultLabelText(_ sender: UIButton) {
}
该sender参数指的是负责触发操作的对象 - 在本例中是一个按钮。该IBAction属性表示该方法是您可以从Interface Builder中的故事板连接到的操作。声明的其余部分通过名称声明一个方法setDefaultLabelText(_:)。

现在，方法声明为空。重置标签值的代码非常简单。

在ViewController代码中实现标签重置操作

在ViewController.swift，找到setDefaultLabelText刚刚添加的操作方法。

在方法实现中，在花括号（{}）之间添加以下代码行：

mealNameLabel.text = "Default Text"
正如您可能猜到的，此代码将标签的text属性设置为默认文本。

请注意，您不必指定默认文本的类型，因为Swift的类型推断可以看到您正在分配某种类型String并且可以正确推断类型。

iOS会为您处理所有重绘代码，因此这实际上是您现在需要编写的所有代码。您的setDefaultLabelText(_:)操作方法应如下所示：

@IBAction func setDefaultLabelText(_ sender: UIButton) {
    mealNameLabel.text = "Default Text"
}
检查点：通过运行模拟器测试您的更改。单击“设置默认标签文本”按钮时，将setDefaultLabelText(_:)调用您的方法，并且mealNameLabel对象的text值将从Meal Name（故事板中设置的值）更改为（操作设置Default Text的值）。您应该会在用户界面中看到更改。

![](./res/CUIC_sim_defaulttext_2x.png)
虽然将餐的名称更改为“默认文本”并不是特别有用，但它确实说明了重要的一点。您刚刚实现的行为是iOS应用程序设计中的目标操作模式的示例。目标操作是一种设计模式，其中一个对象在发生特定事件时将消息发送到另一个对象。

在这种情况下：

该事件是用户点击“设置默认文本”按钮。

行动是setDefaultLabelText(_)。

目标是ViewController（定义了操作方法）。

发件人是“设置默认标签文本”按钮。

系统通过调用目标上的action方法并传入sender对象来发送消息。发件人通常是一个控件 - 例如按钮，滑块或开关 - 可以触发事件以响应用户交互，例如点击，拖动或值更改。这种模式在iOS应用程序编程中非常常见，您将在整个课程中看到更多内容。

处理用户输入
此时，用户可以将膳食名称标签重置为默认值，但您确实希望让用户使用文本字段输入他们自己的膳食名称。为了简单起见，只要用户在文本字段中输入文本并点击Return ，您就会更新mealNameLabel对象的text值。

当您处理从文本字段接受用户输入时，您需要来自文本字段委托的一些帮助。甲代表是一种作用于代，或与另一个对象的协调的对象。委托对象（在本例中为text字段）保留对另一个对象（委托）的引用，并在适当的时候，委托对象向委托发送消息。该消息告诉委托有关委托对象即将处理或刚刚处理的事件。代理可以通过例如更新应用程序中自身或其他对象的外观或状态，或返回影响如何处理即将发生的事件的值来做出响应。

当用户正在编辑文本时，文本字段的委托与文本字段通信，并且知道何时发生重要事件 - 例如当用户开始或停止编辑文本时。代理可以使用此信息在适当的时间保存或清除数据，关闭键盘等。

任何对象都可以充当另一个对象的委托，只要它符合适当的协议即可。调用定义文本字段委托的协议UITextFieldDelegate。将视图控制器作为它管理的对象的委托是很常见的。在这种情况下，您将使您的ViewController实例成为文本字段的委托。

首先，ViewController需要采用UITextFieldDelegate协议。您通过将协议列为类声明行的一部分来采用协议。

采用UITextFieldDelegate协议

如果助理编辑器已打开，请单击“标准”按钮返回标准编辑器。

![](./res/standard_toggle_2x.png)
单击Xco​​de工具栏中的“导航器和实用程序”按钮，展开项目导航器和实用程序区域。

在项目导航器中，选择ViewController.swift。

在ViewController.swift，找到该class行，它应该是这样的：

class ViewController: UIViewController {
之后UIViewController，添加一个逗号（,）并UITextFieldDelegate采用该协议。

class ViewController: UIViewController, UITextFieldDelegate {
通过采用该UITextFieldDelegate协议，您告诉编译器ViewController该类可以充当有效的文本字段委托。这意味着您可以实现协议的方法来处理文本输入，并且可以将ViewController类的实例指定为文本字段的委托。

将ViewController对象设置为其nameTextField属性的委托

在ViewController.swift，找到viewDidLoad()方法，它应该是这样的：

override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
}
此方法的模板实现包括注释。您在方法实现中不需要此注释，因此请继续并删除它。

在该super.viewDidLoad()行下方添加一个空行和以下内容：

// Handle the text field’s user input through delegate callbacks.
nameTextField.delegate = self
该self指ViewController类，因为它的范围内引用的ViewController类定义。

您可以添加自己的注释，以帮助您了解代码中发生的情况。

您的viewDidLoad()方法应如下所示：

override func viewDidLoad() {
    super.viewDidLoad()
    
    // Handle the text field’s user input through delegate callbacks.
    nameTextField.delegate = self
}
当一个ViewController实例被加载时，它本身将作为其委托nameTextField财产。

该UITextFieldDelegate协议定义了八种可选方法。只需实现您需要的那些来获得您想要的行为。目前，您需要实现以下两种方法：

func textFieldShouldReturn(_ textField: UITextField) -> Bool
func textFieldDidEndEditing(_ textField: UITextField)
要了解何时调用这些方法以及需要执行哪些操作，了解文本字段如何响应用户事件非常重要。当用户点击文本字段时，它会自动成为第一个响应者。在应用程序中，第一个响应者是第一个在线上用于接收多种应用事件的对象，包括关键事件，运动事件和动作消息等。换句话说，用户生成的许多事件最初被路由到第一响应者。

由于文本字段成为第一个响应者，iOS显示键盘并开始该文本字段的编辑会话。使用该键盘的用户键入的内容将插入到文本字段中。

当用户想要完成文本字段的编辑时，文本字段需要重新签名其第一响应者状态。由于文本字段将不再是应用程序中的活动对象，因此事件需要路由到更合适的对象。

这是您的UITextFieldDelegate方法实现的用武之地。您需要指定当用户点击按钮以在文本字段中结束编辑时，文本字段应该重新签名其第一响应者状态。您可以在textFieldShouldReturn(_:)方法中执行此操作，当用户在键盘上点击Return（或在本例中为Done）时会调用该方法。

实现UITextFieldDelegate协议方法textFieldShouldReturn（_ :)

在ViewController.swift该//MARK: Actions部分正上方，添加以下内容：

//MARK: UITextFieldDelegate
此注释用于组织代码并帮助您（以及任何阅读代码的人）浏览它。

到目前为止，您已添加了其中一些评论。Xcode将这些注释中的每一个列为源代码文件的“ 功能”菜单中的节标题，如果单击编辑器区域顶部的文件名，则会显示该标题。使用“功能”菜单可以快速跳转到代码中的某个部分。你会注意到你//MARK:在这里列出的部分。您可以单击其中一个部分标题以跳转到该文件中的该部分。

![](./res/CU_functionsmenu_2x.png)
在评论下方，添加以下方法：

func textFieldShouldReturn(_ textField: UITextField) -> Bool {
}
在此方法中，添加以下代码以重新签名文本字段的第一响应者状态，并添加注释以描述代码的作用：

// Hide the keyboard.
textField.resignFirstResponder()
尝试键入第二行而不是仅复制和粘贴。你会发现代码完成是Xcode的一个很好的省时功能。当Xcode显示可能的完成列表时，滚动列表直到找到所需的完成，然后按Return键。Xcode为您插入整行。

![](./res/CU_code_completion_2x.png)
在此方法中，添加以下代码行：

return true
此方法返回一个布尔值，指示系统是否应处理按Return键的操作。在这种情况下，您总是希望响应用户按下Return键，所以只需返回true。

您的textFieldShouldReturn(_:)方法应如下所示：

func textFieldShouldReturn(_ textField: UITextField) -> Bool {
    // Hide the keyboard.
    textField.resignFirstResponder()
    return true
}
textFieldDidEndEditing(_:)在文本字段重新启动其第一响应者状态后调用 您需要实现的第二种方法。由于您将第一响应者状态重新签名textFieldShouldReturn，系统在调用后立即调用此方法textFieldShouldReturn。

该textFieldDidEndEditing(_:)方法使您有机会阅读输入到文本字段中的信息并对其执行某些操作。在您的情况下，您将获取文本字段中的文本并使用它来更改标签的值。

实现UITextFieldDelegate协议方法textFieldDidEndEditing（_ :)

在ViewController.swift该textFieldShouldReturn(_:)方法之后，添加以下方法：

func textFieldDidEndEditing(_ textField: UITextField) {
}
在此方法中，添加以下代码行：

mealNameLabel.text = textField.text
这就是你需要做的就是看到结果。您的textFieldDidEndEditing(_:)方法应如下所示：

func textFieldDidEndEditing(_ textField: UITextField) {
    mealNameLabel.text = textField.text
}
检查点：通过运行模拟器测试您的更改。您可以选择文本字段并在其中键入文本。单击键盘上的“完成”按钮时，键盘将被关闭，标签文本将更改为在文本字段中显示文本。单击“设置默认标签文本”按钮时，标签会从标签中当前显示的内容更改为Default Text（您之前定义的操作设置的值）。

![](./res/CUIC_sim_finalUI_2x.png)
包起来
在本课程中，您已使用助理编辑器向源代码添加插座和操作。当用户与控件交互时，您还添加了用于更新用户界面的代码。该项目仍然只是一个相对简单的单一场景，但您将继续添加功能并增加其余课程的复杂性。