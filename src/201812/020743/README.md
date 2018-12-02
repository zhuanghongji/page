# Java 注解的基本原理和运用

注解 (`Annotation`) 是在 Java 5.0 版本时引入的，是一种应用于类、方法、参数、变量、构造器及包声明中的特殊修饰符。通俗点，可以将其理解为源代码的元数据，比如：

```java
@Override
public String toString() {
  return "This is String Representation of current object."
}

// 在这里我们使用 @Override 注解描述了 toString() 方法覆盖了父类的方法。
// 这样编译器就知道你是要覆盖父类方法了，编译时如果父类中不存在该方法就会报误。
```

通过使用注解，开发者可以在不改变原有的逻辑下，在源文件中嵌入一些补充的信息：
* 可以在编译、类加载、运行时被读取，并执行相应的逻辑。
* 可以为类、方法、成员变量等设置元数据。

下面从以下几个方面进行阐述：
1. 基本注解
2. 元注解
3. 如何自定义注解
4. 如何提取注解信息
5. 如何使用注解的实例
6. 编译时处理注解

## 1. 基本注解

基本注解，直接修饰元素的注解。

* `@Override`: 强制一个子类必须覆盖父类的方法（编译时需保证父类有该方法可覆盖）。
* `@Deprecated`: 表示某个程序元素（类、方法等）已过时，当其他程序使用到该元素时，编译器会给出警告。
* `@SuppressWarnings`: 指示被该注解修饰的程序元素（以及该程序元素中的所有子元素）取消显示指定的编译器警告。
* `@SafeVarargs`: Java 7 专门为抑制“堆污染”警告提供的。


### 2. 元注解

元注注解，用来修饰注解的注解。

### 2.1 @Retention

> `@Retention`：描述被修饰的注解在什么时候使用。

只能用于修饰一个注解定义，用于指定被修饰的注解可以保留多长时间，包含一个 `RetentionPolicy` 类型的 `value` 成员变量，其值只能是以下三个：

* `RetentionPolicy.SOURCE`: 表明修饰的注解只保留在源码中，编译时会直接丢弃。
* `RetentionPolicy.CLASS`: 编译器将把修饰的注解记录在
class 文件中，但运行时 JVM 不再保留。默认值。
* `RetentionPolicy.RUNTIME`: 编译器将把修饰的注解记录在 class 文件中，运行时 JVM 会保留，程序可通过反射获取被修饰的注解的信息。

Retention.java:
```Java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    /**
     * Returns the retention policy.
     * @return the retention policy
     */
    RetentionPolicy value();
}
````

RetentionPolicy.java:
```Java
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     */
    SOURCE,

    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     */
    CLASS,

    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     *
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME
}
```

### 2.2 @Target

> `@Target`：描述被修饰的注解用在什么地方。

也是只能用于修饰一个注解定义，用于指定被修饰的注解能修饰哪些程序单元，包含一个 `ElementType` 类型的 `value` 成员变量：

* `ElementType.TYPE`: 只能修饰类、接口或枚举定义。
* `ElementType.FIELD`: 只能修饰成员变量。
* `ElementType.METHOD`: 只能修饰方法定义。
* `ElementType.PARAMETER`: 只能修饰参数。
* `ElementType.CONSTRUCTOR`: 只能修饰构造器。
* `ElementType.LOCAL_VARIABLE`: 只能修饰局部变量。
* `ElementType.ANNOTATION_TYPE`: 只能修饰注解类型。
* `ElementType.PACKAGE`: 只能修饰包定义。
* `ElementType.TYPE_PARAMETER`
* `ElementType.TYPE_USE`

Target.java:
```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    /**
     * Returns an array of the kinds of elements an annotation type
     * can be applied to.
     * @return an array of the kinds of elements an annotation type
     * can be applied to
     */
    ElementType[] value();
}
```

ElementType.java:
```java
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE,

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

### 2.3 @Documented

> `@Documented`：描述被修饰的注解是否包含在 JavaDoc 中。

用于指定被该元注解修饰的注解类将被 javadoc 工具提取成文档。

也就是说，如果注解类时使用了 `@Documented` 修饰，那么所有使用该注解修饰的程序元素的 API 文档中将会包含被修饰注解的实例。

Documented.java:
```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Documented {
}
```

### 2.4 `@Inherited`

> `@Documented`：描述被修饰的注解是否允许子类继承。

该元注解指定被它修饰的注解将具有继承性。

也就是说，如果某个类使用了 `@Inherited` 修饰的注解进行修饰，那么其子类将自动被该注解(被修饰的注解)修饰。

Inherited.java:
```Java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Inherited {
}
```

## 3. 如何自定义注解

可是使用 `@interface` 关键字定义新的注解类型。  

默认情况下，注解可用于修饰任何程序元素，包括类、接口和方法等。  

注解是可以带成员变量的，以无形参的方法形式来声明，其方法名和返回值定义了该成员变量的名称和类型。通过 `default` 关键字可以在定义时为成员变量指定默认值。

示例：

```java
public @interface NewAnnotation {

    int age() default 18;

    String name() default "Hello";
}

public class Test {
    public static void main(String[] args) {
        // ...
    }

    @NewAnnotation(age = 23, name = "zhuanghongji")
    public void info() {
        // ...
    }
}
```

根据注解是否包含成员变量，可将其分为以下两类：

* **标记注解**：没有成员变量，仅利用自身的存在与否来为我们提供信息，如前面介绍到的 `@Override`、`@Test` 等。
* **元数据注解**：包含成员变量，可以接受更多的元数据。
 

## 4. 如何提取注解信息

先来了解两个概念：
* `Annotation` 接口：代表程序元素前面的注解，是所有注解类型的父接口。
* `AnnotatedElement` 接口：代表程序中可以接受注解的程序元素。

Annotation.java:
```java
package java.lang.annotation;
public interface Annotation {
  boolean equals(Object obj);
  int hashCode();
  String toString();
  Class<? extends Annotation> annotationType();
}
```

AnnotatedElement.java:
```java
package java.lang.reflect;
public interface AnnotatedElement {
  // 判断该程序元素上是否存在指定类型的注解，如果存在则返回 true，否则返回 false
  default boolean isAnnotationPresent(Class<? extends Annotation> annotationClass) {
        return getAnnotation(annotationClass) != null;
  }

  // 返回该程序元素上存在的指定类型的注解，不存在则返回 null
  <T extends Annotation> T getAnnotation(Class<T> annotationClass);

  // 返回该程序元素上存在的所有注解
  Annotation[] getAnnotations();

  default <T extends Annotation> T[] getAnnotationsByType(Class<T> annotationClass) {
    // ...
  }

  default <T extends Annotation> T getDeclaredAnnotation(Class<T> annotationClass) {
    // ...
  }

  default <T extends Annotation> T[] getDeclaredAnnotationsByType(Class<T> annotationClass) {
    // ...
  }

  Annotation[] getDeclaredAnnotations();
}
```

扩展前面的自定义注解示例，演示如何获取 `info()` 方法的所有注解：

```Java
public class Test {
    public static void main(String[] args) {
        try {
            Annotation[] annotations = Class.forName("Test")
                    .getMethod("info").getAnnotations();
            for (Annotation an : annotations) {
                System.out.println(an);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @NewAnnotation(age = 23, name = "zhuanghongji")
    @Deprecated
    public void info() {
        // ...
    }
}

// 打印结果
@NewAnnotation(name=zhuanghongji, age=23)
@java.lang.Deprecated()
```

如果需要获取某个注解里的元数据，则可以将注解强制类型转换成所需的注解类型，然后通过注解对象的抽象方法来访问这些元数据：

```Java
// ...
for (Annotation an : annotations) {
    if (an instanceof NewAnnotation) {
        NewAnnotation newAnnotation = (NewAnnotation) an;
        int age = newAnnotation.age();
        String name = newAnnotation.name();
        System.out.printf("age = %s, name = %s", age, name);
    }
}

// 打印结果
age = 23, name = zhuanghongji
```

## 5. 如何使用注解的实例

开发者使用注解修饰了类、方法、Field 等成员之后，这些注解是不会自动生效的，必须由开发者提供相应的工具来提取并处理注解信息。

举个例子，自定义一个标记注解 `@Testable`，来标记哪些方法是需要进行测试的：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Testable {
}
```

测试工具代码：

```java
public static void processorTest(String clazz) throws ClassNotFoundException {
    int passed = 0;
    int failed = 0;
    // 遍历 clazz 对应的类里的所有方法
    for (Method m : Class.forName(clazz).getMethods()) {
        // 如果该方法使用了 @Testable 修饰
        if (m.isAnnotationPresent(Testable.class)) {\
            try {
                m.invoke(null);
                passed++;
            } catch (Exception e) {
                e.printStackTrace();
                failed++;
            }
        }
    }
    System.out.printf("共运行了：%s 个方法，成功 %s 个，失败 %s 个",
            passed + failed, passed, failed);
}
```

## 6. 编译时处理注解

`APT (Annotation Processing Tool)` 是一种处理注解的工具，它对源代码文件进行检测并找出其中的注解，然后对其进行额外的处理。

Java 提供的 javac.exe 工具有一个 `-processor` 选项，可用于指定一个注解处理器。每个注解处理器都需要实现 `javax.annotation.Processing` 包下的 `processor` 接口，未避免实现该接口的所有方法，通常我们会采用继承 `AbstractProcessor` 的方式来实现注解处理器。

> 一个注解处理器可以处理一种或多种注解。

```Java
package javax.annotation.processing;
public interface Processor {
  // ...
}

package javax.annotation.processing;
public abstract class AbstractProcessor implements Processor {
  // ...

  public abstract boolean process(Set<? extends TypeElement> annotations,
    RoundEnvironment roundEnv);
}
```

// TODO 完善编译时处理注解的知识，和运用场景