  其他知识点多谈思想还不如多写点代码体验下，不过反射这里不太一样，还需要理解清楚才能去写代码。
  
  学过了类与对象，就知道类是描述各种事物的，而对象是实例化。那么对于描述事物的各种类，按逻辑，也应该有个专门类描述他们自己吧。这就是反射类。
  
   再换一种角度，每个类都是被存放在方法区的，如果我们有相应的方法去扫描解析这个方法区中类的代码，那么是不是我们就不用必须把类实例化才能得到这个类的信息呢。
   Java为我们提供的方法就是反射机制。
   
   之前看到有个人总结反射的一句话是：反射就是把Java类中的各种成分映射成相应的Java类。这话总结的非常好，很精辟。但其实呢，Java中的反射能做到的不止这些，
   它还能知道这个类的父类、知道这个类中的注释等等。几乎只要这个类相关的，反射都有方法获取到。

   ![这里写图片描述](http://img.blog.csdn.net/20150629213741467)
  
   除此之外。我再补充三点：
   
 1. 数组的反射
 
       具有相同维数（不是长度，如一维数组、二维数组）和元素类型的数组属于同一个类型，即有相同的Class实例对象。
       
      元素为基本数据类型的一维数组可以被当做Object类型使用，但不能作为Object[]；元素为非基本数据类型的一维数组则可以作为Object[]用。
      
 2. main函数的调用
 
   JDK1.4和1.5中，关于将数组作为参数传递给invoke是不同的。1.4中是将数组中的每个元素分别对应被调用方法的一个参数，而1.5中是将数组视为一个参数。
   为了兼容性，系统会先按1.4的方法做。这样当我们用反射时想调用main函数并传递数组进去时，可能会出问题。为解决这个问题，需要按如下格式写：
   
   mainMethod.invoke(null,new Object[]{new String[]{字符串元素}})
   或mainMethod.invoke(null,(Object) new String[]{字符串元素})
   
   解释一下：第一种是把字符串数组作为一个元素放在Object[]中，这样系统就不会把字符串数组拆分掉；第二种是直接将字符串数组转为一个Object类型，系统会把其视为一个个体存在。
 3. 反射的应用
 
    反射这么大费周章又功能如此强大，必然是有很重要的应用。在不知道要被调用的类名，更无法直接new实例对象时，就会派上用场了。那是什么时候呢，是写框架的时候。
    
看下面示例
```
package paactises;
import java.lang.reflect.*;
public class ReflectTest {
	public static void main(String[] args) throws Exception {
		Class cla=Class.forName("paactises.Mode");    
		//三种获取Class的方式，此种最好，编译不行的时候试试加上包
		//Class cla=Mode.class;
		//Mode mode=new Mode();Class cla=mode.getClass();
//有空参构造函数时，可以这样创建实例对象
Constructor  intconstructor=
       cla.getDeclaredConstructor(int.class);
		 //获取有参构造函数
//Constructor[]constructors=cla.getDeclaredConstructors();
//获取所有构造函数
		 Object obj=intconstructor.newInstance(100);
		 //调用用有参构造函数创建对象
		 Method[] methods=cla.getDeclaredMethods(); 
		 //获取所有方法
		 sop1("全部成员方法:");
		 for(Method meth:methods){
			 sop1(meth.getName());
		 }
//获取有参方法，注意参数那里也是参数类型的.class.Method 

method2=cla.getDeclaredMethod("staticMethod",String.class);
//获取静态方法
         method1.invoke(obj, 200);   //调用有参方法
         method2.invoke(null, "static");//调用静态方法
		 sop1("公有成员变量：");    
		 Field[] fields=cla.getFields();   //只获取公有字段
		 for(Field fiel:fields){
			 sop1(fiel.getName());
		 }
		 
		 Field field1=cla.getField("field");//获取公有字段
		 field1.set(obj,11);
		 sop1(field1.get(obj));
		 Field field2=cla.getDeclaredField("staticField");
		 //获取静态字段
		 field2.set(null,22);
		 sop1(field2.get(obj));	 
		 Field field3=cla.getDeclaredField("privateField");//获取私有字段
field3.setAccessible(true);//开启权限，否则无法访问赋值
		 field3.set(obj,33);
		 sop1(field3.get(obj));	 
		 
	}
	public static void sop1(Object obj){
		System.out.println(obj);
	}
	
}
class Mode{
	public int field=1;         //定义公有变量
	static int staticField=2;  
	 //定义静态变量，不加public的话认为是私有的
	private int privateField=3;//定义私有变量
	public Mode(){              //定义空参构造函数
		sop("constuctor run::null");
	}
	Mode(int i){              //定义有参构造函数
		sop("constuctor run::"+i);
	}
	
	void method(){            //定义空参成员方法
		sop("method run::null");
	}
	void intMethod(int i){    //定义有参成员方法
		sop("method run::"+i);
	}
//定义有参静态成员方法
		sop("method run::"+str);
	}
	public static void sop(Object obj){      
	 //定义打印成员方法
		System.out.println(obj);
	}
}
运行结果为：
constuctor run::100
全部成员方法:
method
intMethod
staticMethod
sop
method run::200
method run::static
公有成员变量：
field
11
22
33
```

 
