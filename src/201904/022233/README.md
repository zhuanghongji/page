# Java 设计模式：工厂模式和抽象工厂模式

## 工厂模式

在工厂模式中，创建对象时是不需要向外暴露具体创建逻辑的。我们只需定义出一个专门用于创建对象的接口 (或方法)，然后使用方就可以通过定义出来的接口 (或方法) 来创建对象。

> 注：我们通常所说的「工厂模式」可以理解为「普通工厂模式」，以便与后面提到的「抽象工厂模式」区别开。

<img src="./res/001.jpg" width="120">

小明家有个汽车厂，有一条生产线是专门生产 SUV 的，可生产不同颜色的 SUV (多功能车) 汽车。

模拟代码如下：

```java
// 首先，抽象出车的接口
public interface Car {
    void run();
}

// 然后，设计 SUV 的具体类
public class SuvCar implements Car {
    // 这款车的颜色
    private String mColor;

    public SuvCar(String color) {
        // ... 隐藏了轮胎、发动机等具体创建逻辑
        mColor = color;
    }

    @Override
    public void run() {
        System.out.println("Run suv car, color: " + mColor);
    }
}

// 最后，设计一个专门生产 SUV 的生产线
public class SuvCarFactory {
    public static final String RED = "red";
    public static final String GREEN = "green";
    public static final String BLUE = "blue";

    // 根据颜色来生产出对应的 SUV 
    public SuvCar create(String color) {
        if (RED.equals(color)) {
            return new SuvCar(RED);
        } else if (GREEN.equals(color)) {
            return new SuvCar(GREEN);
        } else if (BLUE.equals(color)) {
            return new SuvCar(BLUE);
        } else {
            throw new RuntimeException("No " + color + " suv car.");
        }
    }
}
```

到此，我们已经设计好了一个能根据 “指令” 来生产不同颜色 SUV 汽车的生产线了。接着，我在写一个 "生产三辆颜色不一样的 SUV" 的小示例验证下逻辑是否正确：

```java
public static void main(String[] args) {
    SuvCarFactory factory = new SuvCarFactory();

    SuvCar redSuv = factory.create(SuvCarFactory.RED);
    SuvCar greenSuv = factory.create(SuvCarFactory.GREEN);
    SuvCar blueSuv = factory.create(SuvCarFactory.BLUE);
    
    redSuv.run();
    greenSuv.run();
    blueSuv.run();    
}
```

日志输出如下：

```java
Run suv car, color: red
Run suv car, color: green
Run suv car, color: blue
```

嗯，逻辑是正确的。

该「工厂模式」示例的示意图如下：

![](./res/003.png)


**回头仔细想想好像有哪里不对？**

一个汽车厂不可能只有一条生产线呀，它可能还有一条专门生产 MPV (多用途车) 的生产线，或者生成其它款型汽车的生产线呢。

**由此也引出了另外一个疑问，既然一个汽车厂有这么多条生产线，那有木有一种模式是专门用来生产「生产线」的呢？**

有，请滚动鼠标进入下一部分内容：抽象工厂模式。


## 抽象工厂模式

在抽象工厂模式中，定义出来的接口用于创建一个「相关对象的工厂」，并非用于创建「具体对象」。

重要的事情说三遍：
1. 创建出来的是工厂！  
2. 创建出来的是工厂！  
3. 创建出来的是工厂！  

创建出来的每个工厂也都能按照「工厂模式」创建具体对象。

> 普通工厂与抽象工厂这两者的主要区别在于：
> * 普通工厂产出的是一个产品 (实例)
> * 抽象工厂产出的是一个抽象 (接口)
>
> 有点懵？那可以这样去区分：
> * 普通工厂生产的是「汽车」
> * 抽象工厂生产的是「能生产汽车的生产线」

再通过之前的栗子进行讲解？嗯！但讲解之前得改造下之前的代码。

在普通工厂模式中的 `SuvFactory` 上抽象出「生产线」的逻辑：

```java
// ... Car 接口和 SuvCar 类不变

// 新增一个抽象出来的生产线接口
public interface CarFactory<T extends Car> {
    String RED = "red";
    String GREEN = "green";
    String BLUE = "blue";

    T create(String color);
}

// 让原有的 SuvCarFactory 生产线实现新增的 CarFactory 接口
public class SuvCarFactory implements CarFactory<SuvCar> {
    @Override
    public SuvCar create(String color) {
        if (RED.equals(color)) {
            return new SuvCar(RED);
        } else if (GREEN.equals(color)) {
            return new SuvCar(GREEN);
        } else if (BLUE.equals(color)) {
            return new SuvCar(BLUE);
        } else {
            throw new RuntimeException("No " + color + " suv car.");
        }
    }
}
```

然后，新增一条 MPV 生产线 (姿势与新增 SUV 生产线类似)：

```java
public class MpvCar implements Car {
    private String mColor;

    public MpvCar(String color) {
        mColor = color;
    }

    @Override
    public void run() {
        System.out.println("Run mpv car, color: " + mColor);
    }
}

// 新增的 MPV 生产线也要实现 CarFactory 接口
public class MpvCarFactory implements CarFactory<MpvCar>{
    @Override
    public MpvCar create(String color) {
        if (RED.equals(color)) {
            return new MpvCar(RED);
        } else if (GREEN.equals(color)) {
            return new MpvCar(GREEN);
        } else if (BLUE.equals(color)) {
            return new MpvCar(BLUE);
        } else {
            throw new RuntimeException("No " + color + " mpv car.");
        }
    }
}
```

到目前为止，两条生产线我们都准备好了。最后，我们需要定义一个专门生产「生产线」的工厂：

```java
public class Factory {
    public static final String SUV = "suv";
    public static final String MPV = "mvp";

    // 注：返回值 CarFactory 是接口噢
    public CarFactory create(String type) {
        if (SUV.equals(type)) {
            return new SuvCarFactory();
        } else if (MPV.equals(type)) {
            return new MpvCarFactory();
        } else {
            throw new RuntimeException("No " + type + " car factory.");
        }
    }
}
```

直接看代码有点抽象？那我上张图：

![](./res/005.png)

代码写好了，我们来试着生产出两条「生产线」，然后再通过这两条「生产线」生产出不同款型、不同颜色的「汽车」：

```java
public static void main(String[] args) {
    Factory factory = new Factory();

    // suv
    CarFactory suvFactory = factory.create(Factory.SUV);
    Car redSuvCar = suvFactory.create(CarFactory.RED);
    Car greenSuvCar = suvFactory.create(CarFactory.GREEN);
    redSuvCar.run();
    greenSuvCar.run();

    // mpv
    CarFactory mpvFactory = factory.create(Factory.MPV);
    Car greenMpvCar = mpvFactory.create(CarFactory.GREEN);
    Car blueMpvCar = mpvFactory.create(CarFactory.BLUE);
    greenMpvCar.run();
    blueMpvCar.run();
}
```

日志输出如下：

```java
Run suv car, color: red
Run suv car, color: green
Run mpv car, color: green
Run mpv car, color: blue
```

通过上面的输出日志可以看出，两条汽车生产线最终生产出来的汽车是符合预期的。

该「抽象工厂模式」示例的示意图如下：

![](./res/004.png)


如果 SUV 卖得好，工厂想增加一条新的 SUV 生产线来提高生产效率，我们只需通过 `factory` 定义的 `create()` 方法再创建一个即可：

```java 
CarFactory suvFactory2 = factory.create(Factory.SUV);
``` 


## 总结

在这篇文章中，我们了解了「工厂模式」和「抽象工厂模式」的基本思想，也知道了这两者的区别。