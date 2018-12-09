# 使用 Swift 开发 iOS 应用：持久化数据

坚持数据
在本课程中，您将在FoodTracker应用程序会话中保存膳食列表。理解和实现数据持久性是iOS应用程序开发的重要组成部分。iOS有许多持久数据存储解决方案; 在本课程中，您将使用NSCodingFoodTracker应用程序中的数据持久性机制。NSCoding是一种协议，可以为存档对象和其他结构提供轻量级解决方案。存档的对象可以存储在磁盘上并在以后检索。

学习目标
在课程结束时，您将能够：

创建一个存储字符串常量的结构

了解静态属性和实例属性之间的区别

使用该NSCoding 协议读取和写入数据

保存并加载膳食
在此步骤中，您将实现Meal类中的行为以保存和加载膳食。使用该NSCoding方法， Meal该类负责存储和加载其每个属性。它需要通过将每个属性的值分配给特定键来保存其数据。然后，它通过查找与该密钥关联的信息来加载数据。

键只是一个字符串值。您可以根据应用中最有意义的内容选择自己的密钥。例如，您可以使用该键name来存储name属性的值。

为了清楚哪个编码键对应于每个数据，创建一个存储键字符串的结构。这样，当您需要在整个代码中的多个位置使用密钥时，您可以使用常量而不是重新键入字符串（这会增加出错的可能性）。

实现编码密钥结构

打开Meal.swift。

在Meal.swift该//MARK: Properties部分下方，添加此结构：

//MARK: Types
 
struct PropertyKey {
}
在PropertyKey结构中，添加以下属性：

static let name = "name"
static let photo = "photo"
static let rating = "rating"
每个常量对应于三个属性中的一个Meal。该static关键字表明这些常量属于结构本身，而不是结构的实例。您可以使用结构的名称访问这些常量（例如，PropertyKey.name）。

您的PropertyKey结构应如下所示：

struct PropertyKey {
    static let name = "name"
    static let photo = "photo"
    static let rating = "rating"
}
为了能够编码和解码本身和它的属性，所述Meal类需要符合的NSCoding 协议。要符合NSCoding，Meal需要子类 NSObject。NSObject被认为是定义运行时系统的基本接口的基类。

要子类化NSObject并符合NSCoding

在Meal.swift，找到class行：

class Meal {
之后Meal，从类中添加冒号（:）和NSObject子NSObject类：

class Meal: NSObject {
之后NSObject，添加一个逗号（,）并NSCoding采用该NSCoding协议：

class Meal: NSObject, NSCoding {
现在这Meal是它的子类NSObject，Meal类的初始值设定项必须调用NSObject类的指定初始值设定项之一。因为NSObject类是唯一的初始化程序init()，Swift编译器会自动为您添加调用，因此您无需更改代码; 但是，super.init()如果您愿意，可以随意添加电话。

进一步探索

有关Swift初始化规则的更多信息，请参阅Swift编程语言中的类继承和初始化以及初始化（Swift 3.0.1）。

该NSCoding协议声明了两个方法，任何采用它的类都必须实现，以便可以对该类的实例进行编码和解码：

encode(with aCoder: NSCoder)
init?(coder aDecoder: NSCoder)
该encode(with:)方法准备要归档的类的信息，初始化程序在创建类时取消归档数据。您需要为数据实现encode(with:)方法和初始化程序以便正确保存和加载。

实现encodeWithCoder NSCoding方法

在Meal.swift最后一个花括号（}）之前，添加以下内容：

//MARK: NSCoding
这是一条评论，可以帮助您（以及任何阅读您代码的人）知道本节中的代码与数据持久性有关。

在评论下方，添加此方法：

func encode(with aCoder: NSCoder) {
}
在该encode(with:)方法中，添加以下代码：

aCoder.encode(name, forKey: PropertyKey.name)
aCoder.encode(photo, forKey: PropertyKey.photo)
aCoder.encode(rating, forKey: PropertyKey.rating)
NSCoder类定义了许多encode(_:forKey:)方法，每个方法对第一个参数采用不同的类型。每种方法都对给定类型的数据进行编码。在上面显示的代码中，前两行传递一个String参数，而第三行传递一个Int。这些行对Meal类中每个属性的值进行编码，并使用相应的键存储它们。

该encode(with:)方法应如下所示：

func encode(with aCoder: NSCoder) {
    aCoder.encode(name, forKey: PropertyKey.name)
    aCoder.encode(photo, forKey: PropertyKey.photo)
    aCoder.encode(rating, forKey: PropertyKey.rating)
}
使用编写的编码方法，实现初始化器以解码编码数据。

实现初始化器来加载餐

在文件顶部，导入统一日志记录系统，位于导入UIKit的位置。

import os.log
在encodeWithCoder(_:)方法下方，添加以下初始值设定项：

required convenience init?(coder aDecoder: NSCoder) {
}
该required修饰符意味着这个初始化必须在每个子类实现，如果子类定义了自己的初始化。

所述convenience改性剂意味着这是一个二次初始化，而且它必须调用从相同类中的指定初始化。

问号（?）表示这是一个可以返回nil的可用初始值设定项。

在初始化程序中添加以下代码：

// The name is required. If we cannot decode a name string, the initializer should fail.
guard let name = aDecoder.decodeObject(forKey: PropertyKey.name) as? String else {
    os_log("Unable to decode the name for a Meal object.", log: OSLog.default, type: .debug)
    return nil
}
该decodeObject(forKey:)方法解码编码信息。

返回值decodeObjectForKey(_:)是Any?可选的。String在将赋值传递给name常量之前，guard语句都会解包可选的并将封闭类型向下转换为a 。如果这些操作中的任何一个失败，则整个初始化程序失败。

在上面的代码下面添加以下内容：

// Because photo is an optional property of Meal, just use conditional cast.
let photo = aDecoder.decodeObjectForKey(PropertyKey.photo) as? UIImage
您将返回的值向下转换decodeObject(forKey:)为a UIImage，并将其分配给photo常量。如果向下转发失败，它将分配nil给照片属性。这里不需要guard声明，因为该photo属性本身是可选的。

在上面的代码下面添加以下内容：

let rating = aDecoder.decodeIntegerForKey(PropertyKey.rating)
该decodeIntegerForKey(_:)方法取消归档整数。因为返回值decodeIntegerForKey是Int，所以不需要向下转换解码值，也没有可选的解包。

在实现结束时添加以下代码：

// Must call designated initializer.
self.init(name: name, photo: photo, rating: rating)
作为便利初始化程序，此初始化程序需要在完成之前调用其类的指定初始值设定项之一。作为初始化程序的参数，您可以在归档已保存的数据时传入您创建的常量的值。

新的init?(coder:)初始化程序应如下所示：

required convenience init?(coder aDecoder: NSCoder) {
    
    // The name is required. If we cannot decode a name string, the initializer should fail.
    guard let name = aDecoder.decodeObject(forKey: PropertyKey.name) as? String else {
        os_log("Unable to decode the name for a Meal object.", log: OSLog.default, type: .debug)
        return nil
    }
    
    // Because photo is an optional property of Meal, just use conditional cast.
    let photo = aDecoder.decodeObject(forKey: PropertyKey.photo) as? UIImage
    
    let rating = aDecoder.decodeInteger(forKey: PropertyKey.rating)
    
    // Must call designated initializer.
    self.init(name: name, photo: photo, rating: rating)
    
}
接下来，您需要在文件系统上保存和加载数据的持久路径，以便您知道在哪里查找它。

创建数据的文件路径

在Meal.swift该//MARK: Properties部分下方，添加以下代码：

//MARK: Archiving Paths
 
static let DocumentsDirectory = FileManager().urls(for: .documentDirectory, in: .userDomainMask).first!
static let ArchiveURL = DocumentsDirectory.appendingPathComponent("meals")
使用static关键字标记这些常量，这意味着它们属于类而不是类的实例。在Meal课程之外，您将使用语法访问路径Meal.ArchiveURL.path。无论Meal您创建的类的实例数是多少，都只会有这些属性的一个副本。

该DocumentsDirectory常数使用文件管理器的urls(for:in:)方法来查找网址为您的应用程序的文件目录。这是您的应用可以为用户保存数据的目录。此方法返回一个URL数组，第一个参数返回一个包含数组中第一个URL的可选项。但是，只要枚举正确，返回的数组应始终只包含一个匹配项。因此，强行打开可选项是安全的。

确定文档目录的URL后，您可以使用此URL为应用程序数据创建URL。在这里，您可以通过附加meals到文档URL的末尾来创建文件URL。

进一步探索

有关与iOS中的文件系统交互的更多信息，请参阅“ 文件系统编程指南”。

检查点：使用Command-B构建您的应用程序。它应该没有问题。

保存并加载膳食清单
现在您可以保存并加载单餐，只要用户添加，编辑或删除餐食，您就需要保存并加载餐饮清单。

实现保存膳食列表的方法

打开MealTableViewController.swift。

在//MARK: Private Methods最后一个花括号（}）之前的 部分中，添加以下方法：

private func saveMeals() {
}
在该saveMeals()方法中，添加以下代码行：

let isSuccessfulSave = NSKeyedArchiver.archiveRootObject(meals, toFile: Meal.ArchiveURL.path)
此方法尝试将meals数组存档到特定位置，true如果成功则返回。它使用Meal.ArchiveURL您在Meal类中定义的常量来标识保存信息的位置。

但是，如何快速测试数据是否成功保存？将消息记录到控制台以指示结果。

添加以下if语句：

if isSuccessfulSave {
    os_log("Meals successfully saved.", log: OSLog.default, type: .debug)
} else {
    os_log("Failed to save meals...", log: OSLog.default, type: .error)
}
如果保存成功，则会将调试消息记录到控制台，如果保存失败，则会向控制台记录错误消息。

您的saveMeals()方法应如下所示：

private func saveMeals() {
    let isSuccessfulSave = NSKeyedArchiver.archiveRootObject(meals, toFile: Meal.ArchiveURL.path)
    if isSuccessfulSave {
        os_log("Meals successfully saved.", log: OSLog.default, type: .debug)
    } else {
        os_log("Failed to save meals...", log: OSLog.default, type: .error)
    }
}
现在，实施一种加载膳食的方法。

实现加载膳食列表的方法

在MealTableViewController.swift最后一个花括号（}）之前，添加以下方法：

private func loadMeals() -> [Meal]? {
}
此方法具有Meal对象的可选数组的返回类型，这意味着它可能返回Meal对象的数组或者可能不返回任何内容（nil）。

在该loadMeals()方法中，添加以下代码行：

return NSKeyedUnarchiver.unarchiveObject(withFile: Meal.ArchiveURL.path) as? [Meal]
此方法尝试取消归档存储在路径Meal.ArchiveURL.path中的对象，并将该对象向下转换为对象数组Meal。此代码使用as?运算符，以便nil在向下转换失败时它可以返回。通常会发生此故障，因为尚未保存数组。在这种情况下，该unarchiveObject(withFile:)方法返回nil。nil转向[Meal] 的尝试也失败了，本身又回来了nil。

您的loadMeals()方法应如下所示：

private func loadMeals() -> [Meal]?  {
    return NSKeyedUnarchiver.unarchiveObject(withFile: Meal.ArchiveURL.path) as? [Meal]
}
实施这些方法后，您需要添加代码以在用户添加，删除或编辑餐时保存和加载餐饮列表。

在用户添加，删除或编辑餐时保存膳食列表

在MealTableViewController.swift，找到unwindToMealList(sender:)动作方法：

@IBAction func unwindToMealList(sender: UIStoryboardSegue) {
    if let sourceViewController = sender.source as? MealViewController, let meal = sourceViewController.meal {
        
        if let selectedIndexPath = tableView.indexPathForSelectedRow {
            // Update an existing meal.
            meals[selectedIndexPath.row] = meal
            tableView.reloadRows(at: [selectedIndexPath], with: .none)
        }
        else {
            // Add a new meal.
            let newIndexPath = IndexPath(row: meals.count, section: 0)
            
            meals.append(meal)
            tableView.insertRows(at: [newIndexPath], with: .automatic)
        }
    }
}
在该else子句之后，添加以下代码：

// Save the meals.
saveMeals()
meals只要添加新数组或更新现有数组， 此代码就会保存数组。确保这行代码在outer if语句中。

在MealTableViewController.swift，找到tableView(_:commit:forRowAt:)方法：

// Override to support editing the table view.
override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == .delete {
        // Delete the row from the data source
        meals.remove(at: indexPath.row)
        tableView.deleteRows(at: [indexPath], with: .fade)
    } else if editingStyle == .insert {
        // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view
    }
}
在该meals.removeAtIndex(indexPath.row)行之后，添加以下代码行：

saveMeals()
meals每当用餐被删除时， 此代码都会保存数组。

您的unwindToMealList(_:)操作方法应如下所示：

@IBAction func unwindToMealList(sender: UIStoryboardSegue) {
    if let sourceViewController = sender.source as? MealViewController, let meal = sourceViewController.meal {
        
        if let selectedIndexPath = tableView.indexPathForSelectedRow {
            // Update an existing meal.
            meals[selectedIndexPath.row] = meal
            tableView.reloadRows(at: [selectedIndexPath], with: .none)
        }
        else {
            // Add a new meal.
            let newIndexPath = IndexPath(row: meals.count, section: 0)
            
            meals.append(meal)
            tableView.insertRows(at: [newIndexPath], with: .automatic)
        }
        
        // Save the meals.
        saveMeals()
    }
}
你的tableView(_:commit:forRowAt:)方法应该是这样的：

// Override to support editing the table view.
override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == .delete {
        // Delete the row from the data source
        meals.remove(at: indexPath.row)
        saveMeals()
        tableView.deleteRows(at: [indexPath], with: .fade)
    } else if editingStyle == .insert {
        // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view
    }
}
现在，在适当的时间保存膳食，确保在适当的时间吃饭。加载存储数据的适当位置在表视图的viewDidLoad方法中。

在适当的时间加载膳食清单

在MealTableViewController.swift，找到viewDidLoad()方法：

override func viewDidLoad() {
    super.viewDidLoad()
    
    // Use the edit button item provided by the table view controller.
    navigationItem.leftBarButtonItem = editButtonItem
    
    // Load the sample data.
    loadSampleMeals()
}
设置编辑按钮（navigationItem.leftBarButtonItem = editButtonItem）后，添加以下if语句：

// Load any saved meals, otherwise load sample data.
if let savedMeals = loadMeals() {
    meals += savedMeals
}
如果loadMeals()成功返回数组Meal对象，这个条件true和if语句被执行。如果loadMeals()返回nil，则没有要加载的饭菜并且if语句不会被执行。此代码添加了成功加载到meals阵列的所有餐食。

在if语句之后，添加一个else子句并将调用移到loadSampleMeals()其中：

else {
    // Load the sample data.
    loadSampleMeals()
}
此代码添加了加载到meals阵列的任何膳食。

您的viewDidLoad()方法应如下所示：

override func viewDidLoad() {
    super.viewDidLoad()
    
    // Use the edit button item provided by the table view controller.
    navigationItem.leftBarButtonItem = editButtonItem
    
    // Load any saved meals, otherwise load sample data.
    if let savedMeals = loadMeals() {
        meals += savedMeals
    }
    else {
        // Load the sample data.
        loadSampleMeals()
    }
}
检查点：运行您的应用程序。如果您添加一些新餐并退出应用程序，您添加的食物将在下次打开应用程序时出现。

包起来
在本课程中，您添加了保存和加载应用程序数据的功能。这使数据可以在多次运行中保持不变。每当应用程序启动时，它都会加载现有数据。修改数据后，应用程序会保存数据。应用程序可以安全终止而不会丢失任何数据。

这也完成了应用程序。恭喜！您现在拥有功能齐全的应用程序。