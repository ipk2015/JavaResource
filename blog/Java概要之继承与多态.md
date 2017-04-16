  一般说Java有三大特性:封装，继承，多态。封装基本是所有面向对象语言的特性，不再多说。本篇主要探讨继承和多态。
  
  ### 继承
  
 1.格式：class 子类名 extends 父类名{代码}
 
 2.当子类继承了父类后，就具有了父类的功能以及属性。同时由于自己特有的功能和属性，子类会比父类功能更强大。因此，继承的重点是子类如何传承这些功能的。
 
  当成员函数和成员变量不同名的时候是很简单的，也就是这些成员有明确的归属，要么是继承过来的父类的，要么是子类自己的。但当子父类中的成员出现同名的情况时呢，如何确保调用不会混淆出错，有下列规则。
 
 ⑴.private修饰的成员就不用多想了，还是只能由父类本身使用，子类访问不了。下面的同名成员都是非私有的。
 
 ⑵同名成员变量或函数，其中同名成员函数应做到函数名、参数、返回值都要相同：在子类中调用自己的时，可以用 this.成员名 或直接写成员名，要用到父类的时，则要用 super.成员名 来调用。
 
 这里注意一点：当创建对象后，用对象调用成员时时，只能用 对象名.成员名 来使用子类自己定义的成员，无法再使用super。也就是说：super只能在定义子类时使用，在外界使用子类对象时无法使用。不过可以借助建立方法来间接使用。
 简单点就是说：super只能用在子类在定义类的过程中，可以用来使用父类的同名成员。而当子类创建了对象后，用对象调用同名成员时就是自己定义的成员，即所谓的覆盖。
   看下面的示例：

```
public class Test1 {
	public static void main(String[] args) {
		Zi z1=new Zi();    //创建子类对象
		sop(z1.sameH);	   //调用同名成员变量，不可写z1.super.sameH
		z1.fuRun();     //调用同名函数
		z1.fuRun2();    //调用父类的特有函数
	}
	public static void sop(Object obj){
		System.out.println(obj);
	}
}
class Fu{              //父类
	int sameH=1;       //定义一个同名成员变量
	void fuRun(){      //定义一个同名成员函数
		System.out.println("Fu run");
	}
	void fuRun2(){     //父类特有函数
		System.out.println("Fu2 run");
	}	
}
class Zi extends Fu{         //定义子类继承父类
	int sameH=3;             //同名成员变量
	void fuRun(){            //同名成员函数
		super.fuRun();       //调用父类同名函数
		System.out.println(super.sameH+"   Zi run");
	}                         //调用父类同名成员变量
}
运行结果为
3
Fu run
1   Zi run
Fu2 run
```

 ⑶构造函数：子类对象进行初始化时，在所有的构造函数的第一行都有隐式语句super（），即先执行父类的构造函数。
 
  这里有个细节是：隐式语句是super（），是空参构造函数。当父类中没有空参构造函数且子类也没有主动调用父类的有参构造函数时，编译会失败。至于原因，
  可以这样认为：构造函数是对对象进行初始化的，可能对成员变量或函数有处理动作。子类想继承父类使用成员，就必然要知道这些父类成员经过了哪些初始化处理。
  否则，便无法继承完整。
  
  示例：将上面代码里的Fu类定义代码里加入
  `Fu(int i){
    sameH=i;
  }`    
  
  将代码再编译，会发现编译无法通过
  
### 多态
  可以理解为事物存在的多种体现形态，多态的出现大大的提高程序的扩展性，当然有利必有弊，虽然提高了扩展性，但是只能使用父类的引用访问父类中的成员。
多态体现在程序里就是父类的引用指向了自己的子类对象，即父类的引用也可以接收自己的子类对象。

       格式：父类名 对象名=new 子类名（）。

这里有个前提是必须是类与类之间有关系。要么继承，要么实现。通常还有一个前提是存在成员方法覆盖（并非必须）。

既然出现了父子类，多态势必和继承是有关联的，在使用时关键点自然也是两个类中的变量方法是如何调用的。总结有以下规则：

1.用对象调用的变量和函数，必须是父类自己具有的，否则编译不能通过。

2.静态成员和非静态的成员变量，（或者说成员变量和静态函数）对象调用的都是父类的。

3.非静态的成员函数，对象调用的是子类的。如果父类有而子类没有，那么就等同于调用父类自己的。

看了上面的规则后会明白上面为什么说存在成员方法覆盖并非必须的，但是如果没有非静态成员方法覆盖的话，用多态也就没什么意义了：对象可以调用的全是父类的东西。

下面看一个代码示例：
```
public class Test1 {
	public static void main(String[] args) {
		Fu fu=new Zi();                                 //多态,父类引用指向子类对象
		sop(fu.sameH);                                //对象调用非静态同名成员变量
        sop(fu.st);                                        //对象调用静态同名成员变量
       fu.fuRun();                                       //对象调用非静态同名函数
		fu.fuRun2();                                   //对象调用非静态父类特有函数
		fu.staticRun();                                //对象调用静态同名函数
	}
	public static void sop(Object obj){
		System.out.println(obj);
	}
}
class Fu{
	int sameH=1;
	static int st=10;
	void fuRun(){
		System.out.println("Fu run");
	}
	void fuRun2(){
		System.out.println("Fu2 run");
	}	
	static void staticRun(){
	System.out.println("Fu staitc run");
   }
}

class Zi extends Fu{
	int sameH=3,noSame=0;           //对象不可调用父类没有的noSame
	static int st=11;
	void fuRun(){
		System.out.println("Zi run");
	}
	void ziRun(){                            //对象不可调用父类没有的ziRun
		System.out.println("Zi2 run");
	}
	static void staticRun(){
	System.out.println("Zi staitc run");
   }
}
```
运行结果为
```
1
10
Zi run
Fu2 run
Fu staitc run
```
