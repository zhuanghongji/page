# Flutter 入门三部曲：看尽 Widget 繁华

## 概述

本篇文章的内容和截图主要来自 [alibaba/flutter-go](https://github.com/alibaba/flutter-go)，本人在这里仅仅起到一个资源整合的作用，以方便自己和读者进行更加高效的阅读和理解。


## 快捷方式

[Element](#element)
* [Form](#form)
  * [Input](#input)
    * [TextField](#textfield)
  * [CheckBox](#checkbox)
    * [CheckboxListTile](#checkboxlisttile)
    * [Checkbox](#checkbox)
  * [Button](#button)
    * [FlatButton](#flatbutton)
    * [RaisedButton](#raisedbutton)
    * [IconButton](#iconbutton)
    * [PopupMenuButton](#popupmenubutton)
    * [FloatingActionButton](#floatingactionbutton)
    * [RawMaterialButton](#rawmaterialbutton)
    * [DropdownButton](#dropdownbutton)
    * [OutlineButton](#outlinebutton)
  * [Text](#text)
    * [Text](#text)
    * [RichText](#richtext)
  * [Radio](#radio)
    * [Radio](#radio)
    * [RadioListTile](#radiolisttile)
  * [Slider](#slider)
    * [Slider](#slider)
    * [SliderTheme](#slidertheme)
    * [SliderThemeData](#sliderthemedata)
  * [Switch](#switch)
    * [Switch](#switch)
    * [SwitchListTile](#switchlisttile)
    * [AnimatedSwitcher](#animatedswitcher)
* [Frame](#frame)
  * [Align](#align)
    * [Align](#align)
  * [Stack](#stack)
    * [Stack](#stack)
    * [IndexedStack](#indexedstack)
  * [Layout](#layout)
    * [Row](#row)
    * [Column](#column)
    * [Container](#container)
    * [Center](#center)
  * [Box](#box)
    * [ConstrainedBox](#constrainedbox)
    * [OverflowBox](#overflowbox)
    * [DecoratedBox](#decoratedbox)
    * [FittedBox](#fittedbox)
    * [LimitedBox](#limitedbox)
    * [RotatedBox](#rotatedbox)
    * [SizedOverflowBox](#sizedoverflowbox)
    * [UnconstrainedBox](#unconstrainedbox)
  * [Expanded](#expanded)
    * [Expanded](#expanded)
  * [Spacing](#spacing)
    * [Padding](#padding)
    * [SliverPadding](#sliverpadding)
    * [AnimatedPadding](#animatedpadding)
  * [Table](#table)
    * [Table](#table)
* [Media](#media)
  * [Image](#image)
    * [AssetImage](#assetimage)
    * [DecorationImage](#decorationimage)
    * [ExactAssetImage](#exactassetimage)
    * [FadeInImage](#fadeinimage)
    * [FileImage](#fileimage)
    * [NetworkImage](#networkimage)
    * [RawImage](#rawimage)
    * [MemoryImage](#memoryimage)
    * [Image](#image)
  * [Icon](#icon)
    * [Icon](#icon)
    * [ImageIcon](#imageicon)
    * [IconTheme](#icontheme)
    * [IconData](#icondata)
    * [IconThemeData](#iconthemedata)
  * [Canvas](#canvas)
    * [Canvas](#canvas)
    * [PainterSketch](#paintersketch)
    * [PainterPath](#painterpath)
    * [CircleProgressBarPainter](#circleprogressbarpainter)

[Component](#Component)
  * [Navigation](#Navigation)
    * [BottomNavigationBarItem](#bottomnavigationbaritem)
    * [BottomNavigationBar](#bottomnavigationbar)
  * [List](#list)
    * [AnimatedList](#list)
    * [ListView](#listview)
    * [ListBody](#listbody)
  * [Card](#card)
    * [Card](#card)
  * [Bar](#bar)
    * [AppBar](#appbar)
    * [BottomAppBar](#bottomappbar)
    * [SnackBar](#snackbar)
    * [SliverAppBar](#sliverappbar)
    * [FlexibleSpaceBar](#flexiblespacebar)
    * [ButtonBar](#buttonbar)
    * [SnackBarAction](#snackbaraction)
    * [TabBar](#tabbar)
  * [Dialog](#dialog)
    * [AlertDialog](#alertdialog)
    * [Dialog](#dialog)
    * [AboutDialog](#aboutdialog)
    * [SimpleDialog](#simpledialog)
  * [Scaffold](#scaffold)
    * [Scaffold](#scaffold)
    * [ScaffoldState](#scaffoldstate)
  * [Grid](#grid)
    * [GridTile](#gridtile)
    * [GridView](#gridview)
    * [GridPaper](#gridpaper)
    * [SliverGrid](#slivergrid)
    * [GridTileBar](#gridtilebar)
  * [Scroll](#scroll)
    * [ScrollView](#scrollview)
    * [Scrollable](#scrollable)
    * [ScrollbarPainter](#scrollbarpainter)
    * [ScrollMetrics](#scrollmetrics)
    * [ScrollPhysics](#scrollphysics)
    * [BoxScrollView](#boxscrollview)
    * [CustomScrollView](#customscrollview)
    * [NestedScrollView](#nestedscrollview)
  * [Tab](#tab)
    * [Tab](#tab)
  * [Menu](#menu)
    * [CheckedPopupMenuItem](#checkedpopupmenuitem)
    * [DropdownMenuItem](#dropdownmenuitem)
    * [PopupMenuButton](#popupmenubutton)
    * [PopupMenuDivider](#popupmenudivider)
    * [PopupMenuEntry](#popupmenuentry)
    * [PopupMenuItem](#popupmenuitem)
  * [Pick](#pick)
    * [DayPicker](#daypicker)
    * [MonthPicker](#monthpicker)
    * [YearPicker](#yearpicker)
    * [ShowdatePicker](#showdatepicker)
    * [CityPicker](#citypicker)
  * [Chip](#chip)
    * [Chip](#chip)
    * [ChipTheme](#chiptheme)
    * [ChipThemeData](#chipthemedata)
    * [ChoiceChip](#choicechip)
    * [FilterChip](#filterchip)
    * [InputChip](#inputchip)
    * [RawChip](#rawchip)
  * [Panel](#panel)
    * [ExpansionPanel](#expansionpanel)
    * [ExpansionPanelList](#expansionpanellist)
    * [ExpansionPanelRadio](#expansionpanelradio)
  * [Progress](#progress)
    * [LinearProgressIndicator](#linearprogressindicator)
    * [CircularProgressIndicator](#circularprogressindicator)
    * [RefreshProgressIndicator](#refreshprogressindicator)
  
[Theme](#theme)
* [Material](#material)
  * [MaterialApp](#materialapp)
  * [MaterialColor](#materialcolor)
  * [MaterialButton](#materialbutton)
  * [MaterialPageRoute](#materialpageroute)
  * [MaterialAccentColor](#materialaccentcolor)
  * [MergeableMaterialItem](#mergeablematerialitem)
* [Cupertino](#cupertino)
  * [CupertinoApp](#cupertinoapp)
  * [CupertinoButton](#cupertinobutton)
  * [CupertinoColors](#cupertinocolors)
  * [CupertinoIcons](#cupertinoicons)
  * [CupertinoNavigationBar](#cupertinonavigationbar)
  * [CupertinoPageRoute](#cupertinopageroute)
  * [CupertinoPageScaffold](#cupertinopagescaffold)
  * [CupertinoPicker](#cupertinopicker)
  * [CupertinoPopupSurface](#cupertinopopupsurface)
  * [CupertinoScrollbar](#cupertinoscrollbar)
  * [CupertinoSegmentedControl](#cupertinosegmentedcontrol)
  * [CupertinoSlider](#cupertinoslider)
  * [CupertinoSliverNavigationBar](#cupertinoslivernavigationbar)
  * [CupertinoSwitch](#cupertinoswitch)
  * [CupertinoTabBar](#cupertinotabbar)
  * [CupertinoTabScaffold](#cupertinotabscaffold)
  * [CupertinoTabView](#cupertinotabview)
  * [CupertinoTimerPicker](#cupertinotimerpicker)



## Element
### Form
#### Input
##### TextField

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

#### CheckBox
##### CheckboxListTile

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### Checkbox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

#### Button
##### FlatButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### RaisedButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### IconButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### PopupMenuButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### FloatingActionButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### RawMaterialButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### DropdownButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### OutlineButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

#### Text
##### Text

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

##### RichText

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

#### Radio
##### Radio

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### RadioListTile

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Slider
##### Slider

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### SliderTheme

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### SliderThemeData

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Switch
##### Switch

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### SwitchListTile

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### AnimatedSwitcher

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Frame
#### Align
##### Align

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Stack
##### Stack

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### IndexedStack

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Layout
##### Row

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### Column

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### Container

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### Center

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Box
##### ConstrainedBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### OverflowBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### DecoratedBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### FittedBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### LimitedBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### RotatedBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### SizedOverflowBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### UnconstrainedBox

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Expanded
##### Expanded

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Spacing
##### Padding

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### SliverPadding

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### AnimatedPadding

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Table
##### Table

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Media
#### Image
##### AssetImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### DecorationImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### ExactAssetImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### FadeInImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### FileImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### NetworkImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### RawImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### MemoryImage

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### Image

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Icon
##### Icon

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### ImageIcon

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### IconTheme

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### IconData

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### IconThemeData

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Canvas
##### Canvas

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### PainterSketch

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### PainterPath

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


##### CircleProgressBarPainter

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


* Component
### Navigation
#### BottomNavigationBarItem

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### BottomNavigationBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### List
#### AnimatedList

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ListView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ListBody

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Card
#### Card

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Bar
#### AppBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### BottomAppBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### SnackBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### SliverAppBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### FlexibleSpaceBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ButtonBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### SnackBarAction

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### TabBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Dialog
#### AlertDialog

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Dialog

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### AboutDialog

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### SimpleDialog

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Scaffold
#### Scaffold

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ScaffoldState

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Grid
#### GridTile

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### GridView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### GridPaper

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### SliverGrid

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### GridTileBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Scroll
#### ScrollView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### Scrollable

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ScrollbarPainter

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ScrollMetrics

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ScrollPhysics

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### BoxScrollView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CustomScrollView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### NestedScrollView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Tab
#### Tab

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Menu
#### CheckedPopupMenuItem

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### DropdownMenuItem

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### PopupMenuButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### PopupMenuDivider

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### PopupMenuEntry

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### PopupMenuItem

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Pick
#### DayPicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### MonthPicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### YearPicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ShowdatePicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CityPicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Chip
#### Chip

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ChipTheme

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ChipThemeData

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ChoiceChip

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### FilterChip

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### InputChip

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### RawChip

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Panel
#### ExpansionPanel

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ExpansionPanelList

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### ExpansionPanelRadio

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>



**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Progress
#### LinearProgressIndicator

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CircularProgressIndicator

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### RefreshProgressIndicator

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


* Theme
### Material
#### MaterialApp

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### MaterialColor

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### MaterialButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### MaterialPageRoute

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### MaterialAccentColor

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### MergeableMaterialItem

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


### Cupertino
#### CupertinoApp

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoButton

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoColors

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoIcons

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoNavigationBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoPageRoute

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoPageScaffold

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoPicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoPopupSurface

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoScrollbar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoSegmentedControl

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoSlider

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoSliverNavigationBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoSwitch

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoTabBar

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoTabScaffold

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoTabView

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


#### CupertinoTimerPicker

**简介**

[官方文档]() [示例代码]()

**基本用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>

**进阶用法**

<img src="./res/.png" width="480"/>


## 总结 
