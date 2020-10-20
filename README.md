# 2.1类型信息 
## 2.1.1类型信息
定义：类型信息记录（描述）了类的名称，拥有字段，实现方法，继承的父类和接口的信息。  
经过.java文件经过编译形成的.class文件。  
## 2.1.2类型信息的装载
Java提供类的装载方式：一是预先装载，二是按需装载。不需要把所有的类都装载到JVM中，大部分的类要被延迟到使用时才动态加载，这就是Java的运行时动态加载机制。
+ Java基础类的加载：  
Java基础类是程序运行的基础，因此采用预先加载机制。当启动一个程序时，会先导JDK下面找基础类，并载入jvm.dll。然后启动虚拟机。虚拟机会做一些初始化操作。如设置系统参数。然后会创建一个BoostrapLoader对象，称为启动类装载器，由C++编写，作用是负责在虚拟机启动时一次性加载基础类。
+ 含main()函数类的加载： 
Bootstrap Loader另一项重要的工作是装载定义在sun.misc命名空间下面的Launcher类。Launcher有两个内部类 ExtClassLoader 和AppClassLoader。AppClassLoader的父加载器设置为ExtClassLoader,ExtClassLoader设置为null表示其父加载器为Bootstrap Loader。含有main()函数的入口类有APPClassLoader在程序启动时加载。
```java 
public class Main {
	static {
		System.out.println("Bootstrap prepare!");
	}
	public static void main(String[] args) {
		ClassLoader loader=Main.class.getClassLoader();
		System.out.println(loader);//预期答案是AppClassLoader
		System.out.println(loader.getParent());//预期答案是ExtClassLoader
		System.out.println(loader.getParent().getParent());//预期答案是null
	}
	
}

```
结果如下：  
![](image-kghs2mk7.png)

在装载时会执行静态域。相当于是调用了构造函数。  
+ 按需装载
动态加载机制提到，当需要某一个类时JVM才会到动态加载它。  
1）装载条件：
在程序在运行过程中，党一个类的静态成员被第一次被引用时，JVM就会加载它，这些静态成员包括：  
	静态方法
    静态属性（包括常量，变量）
    构造方法
需要说明的是，一当访问静态常量时，JVM加载类时不会进行类的初始化工作，二尽管构造方法没有显示的声明是静态方法，但是当做是类的静态成员特例，因此使用new关键字是，会被当做是类静态成员的引用，同样会触发JVM加载这个类。
```java 
package zhangbin;
class Person{
    public static  final int ID=1;
    public static String name="zhansan";
    static {
        System.out.println("Person prepare!");
    }
    public static void method(){
        System.out.println("method is excute");
    }
}
/***
 * 需要说明的是，当访问一个类的静态属性时，类的初始化工作并不会进行，真正的初始化会被延迟到对静态方法或者非常量静态属性的首次访问时
 * 初始化有效地实现了尽可能的惰性以减少不必要的内存使用。初始化只会进行一次。
 */
public class Main {
    public static  void main(String[] args){
        System.out.println(Person.ID);//访问静态常量时，还未初始化
        System.out.println(Person.name);//访问静态变量，会初始化
      //  Person.method();//访问静态方法，会初始化
        //new Person();构造方法也算是特殊的静态方法，会初始化。
    }
}

```  
2)按需装载流程
[![Bpy7Ks.jpg](https://s1.ax1x.com/2020/10/20/Bpy7Ks.jpg)](https://imgchr.com/i/Bpy7Ks)























