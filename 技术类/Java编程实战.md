## 泛型

Java泛型的设计经过了5年左右的时间才定型，功能强大，使用方便，能够最大限度地提供类型安全检测。在大多数情况下，泛型被设计用来处理集合。

### 类

1. JDK1.5 引入的元素，本质上是类型参数化，扩展了重复使用代码的能力，而且既安全又简单。
2. 单类型参数泛型示例： `public class Generic<T> {...}`, 类型参数T不能用在静态方法或者静态成员中，T必须是Java类类型，不能是int或者char之类的简单类型。
3. Java的泛型和C++的模板类的本质区别：Java编译器不会创建多个不同版本的Generic类，它会删除所有的泛型信息，并进行必要的强制类型转换，这一过程称为擦拭或者擦除。
4. 多类型参数泛型示例： `public class Generic<T, V> {...}`，多个类型参数之间用逗号分隔。
5. 有界类型参数（bounded types): `public class Generic<T extends superclass> {...}`, 为T指定一个类上界，传递的所有类型必须是这个超类的直接或间接子类。
    - 接口也可以作为上界： `public class Generic<T extends Comparable> `, 关键字依然是extends，不是implements
    - 可以有多个类型上界： `public class Generic<T extends Base & Comparable & Serializable> `, 限界类型用&分隔，可以有多个接口，但最多只能有一个类，类必须是第一个。
6. 通配符参数：`Generic<?> var` 或者 `Generic<? extends Integer> var`，通配符是用来声明一个泛型类的变量的，不能创建（声明）一个泛型类。

### 方法

定义形式：`[访问权限修饰符] [static] [final] <类型参数列表> 返回值类型 方法名([形式参数列表])`

1. 泛型方法可以写在一个泛型类中，也可以写在一个普通类中。实际上很少在泛型类中再定义泛型方法。
2. 泛型方法可以是实例方法或是静态方法。类型参数可以用在静态方法中，这是与泛型类的重要区别。

### 接口

声明形式： `interface 接口名<类型参数列表> `

示例： 

```java
interface MinMax<T extends Comparable<T>> {
    T min();
    T max();
}

//实现泛型接口: 类的声明形式必须和接口的完全一致
class MyClass<T extends Comparable<T>> implements MinMax<T> {
    //...
}
```

通常，如果一个类实现了一个泛型接口，则此类也是泛型类。除非实现的是泛型接口的特定类型，比如: `class MyClass implements MinMax<Integer>`.

### 继承

1. 任何一个泛型类都可以作为父类或子类，泛型类的子类必须将泛型父类所需要的类型参数，沿着继承链向上传递。
2. 一个泛型类也可以以非泛型类作为父类
3. 运行时类型识别（RTTI）：泛型类的对象总是一个特定的类型，由于类型擦除的原因。
    - 对象a是Generic<Integer>类型时： `a instanceof Generic<?>` 等价于 `a instanceof Generic`，均为真
    - getClass返回的也是原始类型
4. 规则：无论类型参数之间是否存在联系，对应的泛型类之间都是不存在联系的。

### 擦除

Java的泛型基本上都是在编译器这个层次来实现的，在生成的Java字节码中是不包含泛型中的类型信息的。这些信息被编译器擦除了，称之为类型擦除。

类型擦除可以简单理解为将泛型Java代码转换为普通Java代码，而由泛型附加的类型信息对JVM来说是不可见的。

类型擦除可能对带来冲突问题。

### 局限

1. 类型参数不能使用基本类型，因为基本类型无法用Object来替换
2. 不能抛出也不能捕获泛型类的异常，泛型类继承Throwable及其子类都是不合法的，也不能在catch子句中使用参数类型
3. 不能使用泛型类型的数组，比如下面是错误的：`Generic<Integer> arr[] = new Generic<Integer>[10];`
4. 不能实例化参数类型对象或数组，因为类型擦除类型参数被替换成了Object的原因，下面的代码是错误的：

```java
public class foo<T> {
    T ob = new T();// 错误，因为T被擦除成了Object
    T [] ob = new T [100]; // 错误
}
```

通常情况下，是由外部创建泛型对象时通过构造函数传递进来参数类型的实例对象进行使用。如果一定要在泛型类中创建，可以通过反射机制中的Class.newInstance()和Array.newInstance()方法实现。



