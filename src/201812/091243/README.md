# 使用 Swift 开发 iOS 应用：使用视图控制器 

使用视图控制器
在本课程中，您将继续使用FoodTracker应用程序中的用餐界面的用户界面（UI）。您将向场景添加图像视图，并使用图像选择器让用户选择用餐图像。

![](./res/WWVC_sim_finalUI_2x.png)
学习目标
在课程结束时，您将能够：

理解视图控制器生命周期及其回调（例如viewDidLoad，  viewWillAppear 和  viewDidAppear） 

在视图控制器之间传递数据

关闭视图控制器

使用手势识别器生成事件

基于UIView/ UIControl class层次结构 预测对象行为 

使用资产目录将图像资源添加到项目中

了解View Controller生命周期
到目前为止，FoodTracker应用程序只有一个场景，其用户界面由单个视图控制器管理。当您构建更复杂的应用程序时，您将创建更多场景，并且需要管理加载和卸载视图，因为它们在屏幕上移动和移出。

UIViewController类（及其子类） 的对象带有一组管理其视图层次结构的方法。当视图控制器在状态之间转换时，iOS会在适当的时候自动调用这些方法。当您创建视图控制器子类（就像ViewController您一直使用的类）时，它继承了定义的方法，UIViewController并允许您为每个方法添加自己的自定义行为。了解系统何时调用这些方法非常重要，这样您就可以在过程中的相应步骤中设置或拆除您正在显示的视图 - 这些都是您在课程后面需要做的事情。

![](./res/WWVC_vclife_2x.png)
iOS调用UIViewController方法如下：

viewDidLoad() - 在从故事板创建和加载视图控制器的内容视图（其视图层次结构的顶部）时调用。在调用此方法时，视图控制器的出口保证具有有效值。使用此方法执行视图控制器所需的任何其他设置。

通常，iOS viewDidLoad()首次创建内容视图时只调用一次; 但是，在首次实例化控制器时，不一定要创建内容视图。相反，它是在系统或任何代码第一次访问控制器的view属性时延迟创建的。

viewWillAppear() - 在视图控制器的内容视图添加到应用程序的视图层次结构之前。使用此方法可以触发在屏幕上显示内容视图之前需要执行的任何操作。尽管名称，只是因为系统调用此方法，它不保证内容视图将变得可见。视图可能被其他视图遮挡或隐藏。此方法仅表示内容视图即将添加到应用程序的视图层次结构中。

viewDidAppear() - 在视图控制器的内容视图添加到应用程序的视图层次结构后立即调用。使用此方法可以在屏幕上显示视图时立即触发任何需要执行的操作，例如获取数据或显示动画。尽管名称，只是因为系统调用此方法，它不保证内容视图是可见的。视图可能被其他视图遮挡或隐藏。此方法仅表示内容视图已添加到应用程序的视图层次结构中。

viewWillDisappear() - 在视图控制器的内容视图从应用程序的视图层次结构中删除之前。使用此方法执行清除任务，例如提交更改或重新签署第一响应者状态。尽管名称如此，但系统不会仅仅因为内容视图被隐藏或隐藏而调用此方法。仅在将要从应用程序的视图层次结构中删除内容视图时调用此方法。

viewDidDisappear() - 在视图控制器的内容视图已从应用程序的视图层次结构中删除之后。使用此方法执行其他拆卸活动。尽管名称如此，但系统并不仅仅因为内容视图已隐藏或隐藏而调用此方法。仅在从应用程序的视图层次结构中删除内容视图时才调用此方法。

您将在FoodTracker应用程序中使用其中一些方法来加载和显示您的数据。事实上，如果你还记得，你已经在以下viewDidLoad()方法中编写了一些代码ViewController：

override func viewDidLoad() {
    super.viewDidLoad()
    
    // Handle the text field’s user input through delegate callbacks.
    nameTextField.delegate = self
}
这种应用程序设计风格称视图控制器作为视图和数据模型之间的通信管道，称为MVC（模型 - 视图 - 控制器）。在此模式中，模型会跟踪应用程序的数据，视图显示用户界面并构成应用程序的内容，控制器可以管理您的视图。通过响应用户操作并使用数据模型中的内容填充视图，控制器充当模型和视图之间通信的网关。MVC是任何iOS应用程序的良好设计的核心，到目前为止，FoodTracker应用程序已经按照MVC原则构建。

当您在应用程序设计的其余部分时牢记MVC模式时，是时候将您的基本用户界面提升到一个新的水平，并将图像添加到用餐场景中。

添加餐照片
完成用餐场景的下一步是添加一种显示特定膳食照片的方法。为此，您将使用图像视图（UIImageView），即显示图片的用户界面元素。

将图像视图添加到场景中

打开你的故事板，Main.storyboard。

在实用程序区域中 打开对象库。（或者，选择“视图”>“实用程序”>“显示对象库”。）

![](./res/object_library_2x.png)
在“对象库”中，键入image view过滤器字段以快速查找“图像视图”对象。

将Image View对象从Object库拖到场景中，使其位于按钮下方的堆栈视图中。

![](./res/WWVC_imageview_place_2x.png)
选择图像视图后，图片：../ Art /inspector_size_2x.png)在实用程序区域中打开“大小”检查器。

回想一下，当您从检查器选择器栏中的左侧选择第五个按钮时，将出现“ 大小 ”检查器。它允许您编辑故事板中对象的大小和位置。

![](./res/WWVC_inspector_size_2x.png)
在“内在大小”字段中，选择“占位符”。（此字段位于“大小”检查器的底部，因此您需要向下滚动到该字段。）

键入320“宽度”和“高度”字段。按Return键。

视图的内在内容大小是视图基于其内容的首选大小。空图像视图没有固有内容大小。只要将图像添加到视图，其内在内容大小就会设置为图像的大小。提供占位符大小可为图像提供临时内在内容大小，您可以在设计用户界面时使用该大小。该值仅在Interface Builder中设计界面时使用; 在运行时，布局引擎使用视图的实际内在内容大小。

![](./res/WWVC_placeholdersize_2x.png)
在画布的右下角，打开“Pin”菜单。

![](./res/AL_pinmenu_2x.png)
选中“纵横比”旁边的复选框。

![](./res/WWVC_imageview_aspectratio_2x.png)
在Pin菜单中，单击Add 1 Constraints按钮。

您的图片视图现在具有1：1的宽高比，因此它始终为正方形。

![](./res/WWVC_imageview_finalconstraints_2x.png)
选择图像视图后，打开“属性”检查器图片：../ Art /inspector_attributes_2x.png)。

回想一下，当您从检查器选择器栏中的左侧选择第四个按钮时，将显示“ 属性 ”检查器。它允许您编辑故事板中对象的属性。

在“属性”检查器中，找到“交互”字段，然后选中“启用用户交互”复选框。

稍后您将需要此功能，以便用户与图像视图进行交互。

显示默认照片
添加占位符图像，让用户知道他们可以与图像视图进行交互以选择照片。使用本Images/课程结尾处可下载文件的文件夹中的此图像，或使用您自己的图像。

![](./res/defaultphoto_2x.png)
将图像添加到项目中

在项目导航器中，选择Assets.xcassets查看资产目录。

该资产目录是存储和整理你的形象资产的应用程序的地方。

在左下角，单击加号（+）按钮，然后从弹出菜单中选择“新建图像集”。

![](./res/WWVC_assetcatalog_2x.png)
双击图像集名称并将其重命名为defaultPhoto。

在计算机上，选择要添加的图像。

将图像拖放到2x图像集的插槽中。

![](./res/WWVC_defaultphoto_drag_2x.png)
2x是您在这些课程中使用的iPhone 7 模拟器的显示分辨率，因此图像在此分辨率下看起来最佳。

进一步探索

有关图像分辨率的更多信息，请参阅iOS人机界面指南中的图形>图像大小和分辨率。

将默认占位符图像添加到项目中后，设置图像视图以显示它。

在图像视图中显示默认图像

打开你的故事板。

在故事板中，选择图像视图。

选择图像视图后，图片：../ Art /inspector_attributes_2x.png)在实用程序区域中打开“属性”检查器。

在“属性”检查器中，找到标记为“图像”的字段并选择defaultPhoto。

检查点：运行您的应用程序。默认图像显示在图像视图中。

![](./res/WWVC_sim_finalUI_2x.png)
将图像视图连接到代码
现在，您需要实现在运行时更改此图像视图中的图像的功能。首先，您需要将图像视图连接到代码中ViewController.swift。

将图像视图连接到ViewController.swift代码

单击Xco​​de右上角附近的Xcode工具栏中的“助手”按钮以打开助理编辑器。

图片：../ Art /assistant_editor_toggle_2x.png)
如果需要更多空间，可以通过单击Xco​​de工具栏中的“导航器和实用程序”按钮来折叠项目导航器和实用程序区域。

![](./res/navigator_utilities_toggle_on_2x.png)
您也可以折叠大纲视图。

在故事板中，选择图像视图。

从画布上的图像视图控制拖动到右侧编辑器中的代码显示，停止在现有插座正下方的线上拖动ViewController.swift。

![](./res/WWVC_imageview_dragoutlet_2x.png)
在出现的对话框中，键入名称photoImageView。保留其余选项。

![](./res/WWVC_imageview_addoutlet_2x.png)
单击连接。

Xcode添加了必要的代码来ViewController.swift存储对图像视图的引用，并配置故事板以设置该连接。

@IBOutlet weak var photoImageView: UIImageView!
您现在可以从代码访问图像视图以更改其图像，但您如何知道何时更改图像？您需要为用户提供一种方式来指示他们想要更改图像 - 例如，通过点击图像视图。然后，您将定义一个动作方法，以便在点击发生时更改图像。

视图和控件 之间存在细微差别，它们是以特定方式响应用户操作的视图的专用版本。视图显示内容，而控件用于以某种方式修改内容。control（UIControl）是的子类UIView。实际上，您已经在界面中使用了视图（标签，图像视图）和控件（文本字段，按钮）。

创建一个手势识别器
图像视图不是控件，因此它不是设计为以与按钮或滑块相同的方式响应输入。例如，您不能简单地创建在用户点击图像视图时触发的操作方法。（如果您尝试按住Control键 - 从图像视图拖动到代码，您会注意到无法在“连接”字段中选择“操作”。）

幸运的是，通过向其添加手势识别器，可以很容易地为视图提供与控件相同的功能。手势识别器是附加到视图的对象，允许视图以控件的方式响应用户。手势识别器解释触摸以确定它们是否对应于特定手势，例如滑动，捏合或旋转。您可以编写一个在手势识别器识别其指定手势时调用的操作方法，这正是您需要对图像视图执行的操作。

将点击手势识别器（UITapGestureRecognizer）附加到图像视图，该图像视图将识别用户何时点击图像视图。您可以在故事板中轻松完成此操作。

向图像视图添加点按手势识别器

打开对象库（选择“视图”>“实用程序”>“显示对象库”）。

在对象库中，键入tap gesture过滤器字段以快速查找Tap Gesture Recognizer对象。

将Tap Gesture Recognizer对象从Object库拖到场景中，并将其放在图像视图的顶部。

![](./res/WWVC_gesturerecognizer_drag_2x.png)
Tap Gesture Recognizer对象出现在用餐场景停靠栏中。

![](./res/WWVC_scenedock_2x.png)
将手势识别器连接到代码
现在，将该手势识别器连接到代码中的操作方法。

将手势识别器连接到ViewController.swift代码

按住Control键拖动从场景中的码头在右边的编辑器的代码显示手势识别，停在下面的一行拖拽//MARK: Actions 注释中ViewController.swift。

![](./res/WWVC_gesturerecognizer_dragaction_2x.png)
在出现的对话框中，对于Connection，选择Action。

对于名称，请键入selectImageFromPhotoLibrary。

对于类型，请选择UITapGestureRecognizer。

![](./res/WWVC_gesturerecognizer_addaction_2x.png)
单击连接。

Xcode添加了必要的代码ViewController.swift来设置操作。

@IBAction func selectImageFromPhotoLibrary(_ sender: UITapGestureRecognizer) {
}
创建一个图像选择器以响应用户点击
当用户点击图像视图时，他们应该能够从一组照片中选择一张照片，或者选择他们自己的照片。幸运的是，UIImagePickerController该类内置了这种行为。图像选择器控制器管理用户界面以拍摄照片和选择要在应用程序中使用的已保存图像。就像在处理文本字段时需要文本字段委托一样，您需要一个图像选择器控制器委托来处理图像选择器控制器。该委托协议的名称是UIImagePickerControllerDelegate，您将定义为图像选择器控制器的委托的对象ViewController.

首先，ViewController需要采用UIImagePickerControllerDelegate协议。因为ViewController将负责呈现图像选择器控制器，所以它还需要采用UINavigationControllerDelegate协议，这只是ViewController承担一些基本的导航责任。

采用UIImagePickerControllerDelegate和UINavigationControllerDelegate协议

单击“标准”按钮返回标准编辑器。

![](./res/standard_toggle_2x.png)
单击Xco​​de工具栏中的“导航器和实用程序”按钮，展开项目导航器和实用程序区域。

在项目导航器中，选择ViewController.swift。

在ViewController.swift，找到该class行，它应该是这样的：

class ViewController: UIViewController, UITextFieldDelegate {
之后UITextFieldDelegate，添加一个逗号（,）并UIImagePickerControllerDelegate采用该协议。

class ViewController: UIViewController, UITextFieldDelegate, UIImagePickerControllerDelegate {
之后UIImagePickerControllerDelegate，添加一个逗号（,）并UINavigationControllerDelegate采用该协议。

class ViewController: UIViewController, UITextFieldDelegate, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
此时，您可以返回到您定义的操作方法selectImageFromPhotoLibrary(_:)，并完成其实现。

实现selectImageFromPhotoLibrary（_ :)动作方法

在ViewController.swift，找到selectImageFromPhotoLibrary(_:)您之前添加的操作方法。

它应该如下所示：

@IBAction func selectImageFromPhotoLibrary(_ sender: UITapGestureRecognizer) {
}
在方法实现中，在花括号（{}）之间添加以下代码：

// Hide the keyboard.
nameTextField.resignFirstResponder()
此代码确保如果用户在文本字段中键入时轻触图像视图，则键盘将被正确解除。

添加此代码以创建图像选取器控制器：

// UIImagePickerController is a view controller that lets a user pick media from their photo library.
let imagePickerController = UIImagePickerController()
添加此代码：

// Only allow photos to be picked, not taken.
imagePickerController.sourceType = .photoLibrary
这行代码设置了图像选择器控制器的源，或它获取图像的位置。该.photoLibrary选项使用模拟器的相机胶卷。

imagePickerController.sourceType已知 的类型UIImagePickerControllerSourceType是枚举。这意味着您可以将其值写为缩写形式.photoLibrary而不是UIImagePickerControllerSourceType.photoLibrary。回想一下，只要枚举值的类型已知，就可以使用缩写形式。

添加此代码以将图像选择器控制器的委托设置为ViewController：

// Make sure ViewController is notified when the user picks an image.
imagePickerController.delegate = self
在上一行下方，添加以下代码行：

present(imagePickerController, animated: true, completion: nil)
present(_:animated:completion:)是一种被调用的方法ViewController。虽然它没有明确写入，但此方法是在隐式self对象上执行的。该方法要求ViewController呈现由定义的视图控制器imagePickerController。传递true给animated参数可以设置图像选取器控制器的显示动画。该completion参数引用完成处理程序，即在此方法完成后执行的一段代码。因为您不需要执行任何其他操作，所以表明您不需要通过传入nil来执行完成处理程序。

您的selectImageFromPhotoLibrary(_:)操作方法应如下所示：

@IBAction func selectImageFromPhotoLibrary(_ sender: UITapGestureRecognizer) {
    
    // Hide the keyboard.
    nameTextField.resignFirstResponder()
    
    // UIImagePickerController is a view controller that lets a user pick media from their photo library.
    let imagePickerController = UIImagePickerController()
    
    // Only allow photos to be picked, not taken.
    imagePickerController.sourceType = .photoLibrary
    
    // Make sure ViewController is notified when the user picks an image.
    imagePickerController.delegate = self
    present(imagePickerController, animated: true, completion: nil)
}
显示图像选择器控制器后，您将通过委托方法与其进行交互。为了让用户能够选择图片，您需要实现以下定义的两个委托方法UIImagePickerControllerDelegate：

func imagePickerControllerDidCancel(_ picker: UIImagePickerController)
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any])
imagePickerControllerDidCancel(_:)当用户点击图像选择器的“取消”按钮时，会调用 其中的第一个。此方法使您有机会解除UIImagePickerController（并且可选地执行任何必要的清理）。

实现imagePickerControllerDidCancel（_ :)方法

在ViewController.swift该//MARK: Actions部分正上方，添加以下内容：

//MARK: UIImagePickerControllerDelegate
这是一条评论，可以帮助您（以及其他读取您代码的人）浏览您的代码并确定此部分适用于图像选择器实现。

在您刚添加的评论下方，添加以下方法：

func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
}
在此方法中，添加以下代码行：

// Dismiss the picker if the user canceled.
dismiss(animated: true, completion: nil)
此代码动画解雇图像选择器控制器。

您的imagePickerControllerDidCancel(_:)方法应如下所示：

func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
    // Dismiss the picker if the user canceled.
    dismiss(animated: true, completion: nil)
}
当用户选择照片时，UIImagePickerControllerDelegate需要实现 第二种方法imagePickerController(_:didFinishPickingMediaWithInfo:)。此方法使您有机会对用户从选取器中选择的图像执行某些操作。在您的情况下，您将拍摄所选图像并将其显示在图像视图中。

实现imagePickerController（_：didFinishPickingMediaWithInfo :)方法

在imagePickerControllerDidCancel(_:)方法下方，添加以下方法：

func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
}
在此方法中，添加以下代码行：

// The info dictionary may contain multiple representations of the image. You want to use the original.
guard let selectedImage = info[UIImagePickerControllerOriginalImage] as? UIImage else {
    fatalError("Expected a dictionary containing an image, but was provided the following: \(info)")
}
该info字典总是包含在机械臂选择的原始图像。它还可能包含该图像的编辑版本（如果存在）。为了简单起见，您将使用未经编辑的原始图像作为餐照。

此代码访问info字典中未经编辑的原始图像。它安全地解开字典返回的可选项并将其作为UIImage对象进行转换。期望是展开和铸造操作永远不会失败。如果他们这样做，则表示您的应用中需要在设计时修复的错误。该fatalError()方法将错误消息记录到控制台，包括info字典的内容，然后使应用程序终止 - 防止它继续处于无效状态。

添加以下代码行以在先前创建的图像视图插座中设置所选图像：

// Set photoImageView to display the selected image.
photoImageView.image = selectedImage
添加以下代码行以关闭图像选择器：

// Dismiss the picker.
dismiss(animated: true, completion: nil)
您的imagePickerController(_:didFinishPickingMediaWithInfo)方法应如下所示：

func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
    
    // The info dictionary may contain multiple representations of the image. You want to use the original.
    guard let selectedImage = info[UIImagePickerControllerOriginalImage] as? UIImage else {
        fatalError("Expected a dictionary containing an image, but was provided the following: \(info)")
    }
    
    // Set photoImageView to display the selected image.
    photoImageView.image = selectedImage
    
    // Dismiss the picker.
    dismiss(animated: true, completion: nil)
}
检查点：运行您的应用程序。单击图像视图时会发生什么？

![](./res/WWVC_app_abort_2x.png)
该应用终止于一个SIGABRT信号。这意味着发生的错误严重到足以导致应用程序中止。在这种情况下，当您尝试显示图像选择器时会出现问题。在访问照片库之前，系统必须要求用户许可。在iOS 10及更高版本中，您必须提供照片库使用说明。此说明解释了您的应用想要访问照片库的原因。

添加照片库使用说明

在项目导航器中，选择Info.plist。

Xcode在编辑器区域中显示属性列表编辑器。属性列表是一个结构化文本文件，其中包含有关应用程序的基本配置信息。属性列表的根是一个包含一组预定义键及其值的字典。

![](./res/WWVC_Property_List_Editor_2x.png)
进一步探索

有关可能info.plist键的更多信息，请参阅信息属性列表键参考。

如果属性列表中的最后一项是数组，请确保该数组已折叠。如果将项添加到展开的数组，则会添加子项。如果将项添加到折叠数组，则会向数组添加一个兄弟。

要添加新项目，请将鼠标悬停在属性列表中的最后一项上，然后在出现时单击添加按钮（或选择编辑器>添加项目）。

![](./res/WWVC_addInfoPlistItem_2x.png)
在弹出菜单中，向下滚动并选择隐私 - 照片库使用说明。

![](./res/WWVC_addphotolibrarydescription_2x.png)
在新行中，确保将Type设置为String。然后，双击“值”部分并输入Allows you to add photos to your meals.

![](./res/WWVC_addingDescriptionString_2x.png)
完成编辑描述字符串后，按Return键。

检查点：再次运行您的应用程序。这次你应该能够点击图像视图来拉出图像选择器。您需要在警告上单击“确定”，该警报要求允许FoodTracker应用程序访问照片。然后，您可以单击“取消”按钮以关闭选取器，或打开“相机胶卷”并单击图像以将其选中并将其设置为图像视图中的图像。

![](./res/WWVC_sim_imagepicker_2x.png)
如果您查看模拟器中提供的照片，您会发现它不包含任何食物照片。您可以将自己的图像直接添加到模拟器中，以使用适当的样本内容测试FoodTracker应用程序。您可以Images/在本课程结尾处的可下载文件的文件夹中找到示例图像，或使用您自己的图像。

将图像添加到iOS Simulator

如有必要，请在模拟器中运行您的应用程序。

在计算机上，选择要添加的图像。

将图像拖放到模拟器中。

![](./res/WWVC_sim_dragphoto_2x.png)
模拟器打开照片应用程序并显示您添加的图像。

![](./res/WWVC_sim_choosephoto_2x.png)
检查点：运行您的应用程序。您应该可以点按图像视图以拉出图像选择器。打开“相机胶卷”，然后单击您添加到模拟器中的一个图像以将其选中并将其设置为图像视图中的图像。

![](./res/WWVC_sim_selectedphoto_2x.png)
包起来
在本课程中，您已了解视图控制器生命周期方法，并使用它们来配置视图控制器的内容。您还学习了如何向视图添加手势识别器，以及如何从照片库中选择照片。场景开始看起来像一个真正的应用程序。在下一课中，您将向场景添加自定义控件。