# Android 知识体系

**基础知识**
* 四大组件
  * Activity
    * 生命周期管理
    * Intent、序列化
    * LanuchMode 和 Flag
  * Service
    * startService 和 stopService
    * bindService 和 unbindService
    * 跨进程、binder、aidl
    * IntentService
  * BrocastReceiver
    * LocalBrocastManager
    * 普通广播、有序广播
  * ContentProvider
    * 常见增删改查
    * ContentObserver
    * 实现 ContentProvider
* View
  * 常见 ViewGroup 使用
    * LinearLayout
    * ReleativeLayout
    * FrameLayout
    * ...
  * 基础控件使用
    * TextView
    * Button
    * ImageView
    * EditText
    * ...
  * 高级控件使用
    * ListView
    * RecyclerView
    * Toolbar
    * ViewPager
    * WebView
    * SurfaceView
  * 自定义 View 实现
    * onMeasure
    * onDraw
    * onLayout
    * 自定义属性
* UI 设计
  * Fragment
    * 声明周期
    * 基本使用
    * 状态恢复保存
  * 事件处理**
    * 事件分发机制
    * 多点触摸
    * GestureDetector
  * 绘图基础
    * Drawable
      * BitmapDrawable
      * NinePatchDrawable
      * LayerDrawable
      * ...
    * 自定义绘制
  * 动画
    * 帧动画
    * 属性动画
    * 补间动画
  * 通知栏
    * 系统 Style
    * 自定义 RemoteView
  * Material Design
* 数据存储
  * SharePreference
  * 私有存储目录和 SD 卡
  * Sqlite
  * ContentProvider 实现数据读写
* 线程/进程
  * 进程
    * 进程优先级
    * LowMemoryKiller
    * Service、Activity 等多进程实现
  * 线程
    * 线程和线程池的使用
    * Handler、HandlerThread
    * AsyncTask
    * Loader
* 网络
  * HttpClient、HttpUrlConnection
    * 发送请求、解析返回数据
    * 错误 HttpCode 处理
    * CookieStore、代理、超时、重试等设置
  * 文件上传下载

<br/>


**进阶内容**
* 资源打包管理
  * 打包流程
    * 编译、混淆、签名
    * Gradle
  * 资源管理
    * AssetManager
    * Resource
    * 主题样式
    * 多分辨率适配
* 视图架构
  * Activity 的视图层级
  * 布局生成过程
* 优化
  * 掉帧卡顿
  * CPU 或内存占用过高、内存泄漏
  * 过渡绘制
  * 电量优化
    * WakeLock
    * JobScheduler
  * ANR
  * 排查工具
    * Lint
    * StrictMode
    * MAT
    * HierarchyViewer
    * 开发者选项
* NDK 开发

<br/>


**编程语言**
* Java
  * 虚拟机基础知识
  * 语法结构
  * 多线程并发
  * 面向对象设计
* C / C++
* Shell
  * 常见命令使用

<br/>


**网络协议**
* HTTP / HTTPS
  * 数据报文结构
  * HttpCode、常见 Header
  * 文件上传
* 抓包工具使用
  * WireShark
  * HttpScoop
  * TcpDump

<br/>


**数据库**
* SQL 语法
* 索引优化
* 范式

<br/>


**开源框架**
* 图片加载
  * Fresco
  * Glide
  * Picasso
* 推送
  * 小米
  * 友盟
  * 极光
* 事件分发
  * EventBus
  * Otto
* 异步加载
  * RxJava
* 网络
  * Retrofit
  * OkHttp
  * Volley
* HyBrid
  * React Native
  * Flutter

<br/>


**测试基础**
* 开发自测
  * 冒烟测试
  * 边界测试
  * 回归测试
* MonkeyTest
* Android Instrumentation
* 单元测试

<br/>


**技术关注**
* 热修复
* 插件化
* 组件化

<br/>


**其它**
* Android 加固
* Crash、ANR 收集
* 软件内更新
* 多渠道打包
* 换肤
  * 自定义资源加载
  * 换色
* 自定义协议
* Android Studio 
* ADB 工具
  