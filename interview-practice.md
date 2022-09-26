# java基础

参考：[八股文面试-java 基础](https://blog.csdn.net/qq_35571432/article/details/121953322)

## 一、switch 支持的类型？

支持：**byte short char int Enum String**

不支持：**long**

原因：**因为switch 对应的jvm 字节码 lookupswitch ，tableSwitch指令只支持int类型，而long 无法转成int**

jdk 5 之前，switch 支持 **byte short  char int** （其实都是提升成  **int** 来实现的）

jdk 5 引入了** Enum**（也是**int**），jdk 7 引入了**String** （实际上调用了hashCode，所以本质还是int）。

<br/>

## 二、 java 的内部类

分类：

- 静态内部类（可访问外部类的静态变量）
- 局部内部类（可访问外部类的所有方法和变量）
- 成员内部类（定义在方法内部的类，与方法类变量的权限一致）
- 匿名内部类（没有名字的内部类）

注意：

### 匿名内部类不能访问 外部类未加final 修饰的变量？

**原因**：匿名内部类在编译的时候后单独生成一个类，把涉及到的参数通过**构造函数**还是 传入，如果参数不是final 的，则在其内部修改参数值的时候是无法将值传递给外部的，将会导致数据不一致的情况

在内部类中 使用  **未加final** 修饰的变量，会如下报错：

error: local variable xxx  is accessed from within inner class; needs to be declared final

<br/>

## 三、String 、StringBuffer 、StringBuilder

- **String 是常量，不可变**  String 底层是 final char value[]  是有final 修饰的 char 数组，所以 String 是常量，不可修改的。
- **StringBuffer 线程安全，StringBuilder线程不安全** ：StringBuffer 、 StringBuilder 都是继承的 AbstractStringBuilder ，底层使用的char value[] （没有final 修饰），是可变的，StringBuffer 通过synchronize 加锁来实现线程安全。
- String 每次修改，都会生成一个新的对象，把指针指向新的String （如果常量池中本来存在 相同字符串则不会新创建，直接赋值即可）

<br/>

## 四、元注解

- Target（修饰对象范围：TYPE，FIELD，METHOD，PARAMETER ... ）
- Retention（保留时间长度：SOURCE，CLASS，RUNTIME）
- Document（描述）
- Inherited（标注注解是可被子类继承的，注解中，如果该注解未加该注解，则，该注解子类不能继承）

## 五、java 容器快速失败（fail-fast）机制

**概念**：fail-fast 机制，即快速失败机制，是java集合(Collection)中的一种错误检测机制。当在迭代集合的过程中该集合在结构上发生改变的时候，就有可能会发生fail-fast，即抛出 ConcurrentModificationException异常。fail-fast机制并不保证在不同步的修改下一定会抛出异常，它只是尽最大努力去抛出，所以这种机制一般仅用于检测bug。

**实现**：这是通过modCount实现的。遍历器在遍历访问集合中的内容时，会维护modCount和expectedModCount。当modCount不等于expoectedModCount时，就会抛出异常。迭代器在调用next()、remove()方法时都是调用checkForComodification()方法来进行检测modCount是否等于expectedModCount。


**注意**：循环遍历删除，不一定会触发。如果删除的是**倒数第二个元素**，则不会触发该机制。

删除时，会调用 hasNext()，返回cursor  != size , 删除倒数第二个元素后，course  == size  会直接跳出循环，则不会在next（）触发该机制（但是会导致 丢失最后一个元素的遍历）