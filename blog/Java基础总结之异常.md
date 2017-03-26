  是程序就会有bug，是人就会犯错误，那么在Java里是如何对待这些错误或异常呢？基于Java面向对象的思想，Java程序中的错误也是一种事，也有相应类来描述。异常体系及处理的框架总结如下图：
![这里写图片描述](http://img.blog.csdn.net/20150630112838394)    
  学过一遍的同学应该对上面的异常体系应该很清楚了，还算简单，编程时涉及到的时候也不多。在此也就不多谈了。
  和我们关系更紧密的是下面的异常处理。由于所处是基础阶段，对异常转译和异常处理框架这两个高级一点的部分就不说了，我也是从网页上搜索资源时看到的，自己编程时还没遇到过需要用的，就算写，也会比较假。
  下面着重谈try-catch-finally和throw(s)。
 1. throw和throws
    当不马上捕获处理异常时，要用到throw（s）。
    throw是在函数中抛出异常，后跟异常对象；throws则是在函数上声明异常，后跟异常类，可以跟多个。一般抛出或声明时，建议异常越具体越好。
    除了运行异常（RuntimeException），其他异常被在函数中throw，就必须要在函数上用throws声明。运行异常可以两个只有一个。这是因为运行异常一旦发生，程序也就无法运算下去了。
    这里有个注意的地方就是：如上图所示，在子父类中，子类是不能抛出或声明比父类更多的异常，能抛出的异常也仅限于父类抛出的异常的子集。父类异常以外的，就只能子类自己处理掉，否则编译无法通过。
 2. try-catch-finally
   当要异常一发生就马上处理时，就要用到这个部分了。也是我最想谈的部分，因为这是我们用的最多的，也是较为复杂的知识点。
   首先看格式：
```
try{
   可能会发生异常的代码
}
catch（异常类 异常对象）{
  捕捉到对应异常后的处理
}
finally{
 方法返回前一定会执行的代码
} 
```
允许有三种格式：try...catch...finally;try....catch...；try...finally...

(1)try

  try中代码受异常监控，其中代码发生异常时，会抛出异常对象。可能抛出的异常类型有多个。
  
（2）catch

 catch会捕获try代码中发生的异常并在其代码块中做异常处理。要注意的一点是：由于try可能抛出的异常有多个，允许有多个catch。每个catch的（）里的异常种类是不同的（也可以相同，但那样写没意义），try中抛出的异常匹配到哪个catch里的异常种类就执行哪个catch代码。
 
这里有个问题是：当不同catch中异常种类有继承关系（异常种类众多，很可能会发生这种现象）时，try中抛出的异常按catch写的顺序来匹配，先匹配到的就执行，后面的就算同样可以符合匹配但不会被匹配到了。也因此，当我们想处理更为具体的异常时，最好把父类异常放在子类下面。

（3）finally

 一定会在方法返回前执行的代码，不管try中有没有抛出异常和catch有没有执行。只有一种情况finally是不会执行的：当在try或catch中调用了System.exit(0)时（有时候就算是写了但却有可能不会被执行）。
 
 看下面的代码示例，来更好了解下finally
```
 public class ExceptionTest {
	public static void main(String[] args) {
		int b=1,c=2 ;
		int a=0;
		try{
		    sop("try run");
			b=c/a;  //会发生除零异常
		}
		catch(Exception e){
		    sop("catch run");
			sop(e.getMessage());	
		}
		finally{
			sop("finally run");
		}
	}
	public static void sop(Object obj){
		System.out.println(obj);
	}
} 
运行结果为
try run
catch run
/ by zero
finally run
```
接下来，我们做下改变：保持其余代码不变，改写try中代码 
```
try{
   sop("try run");
   b=a/c;  //不会发生异常
}
运行结果为
try run
finally run
```
改写try
```
try{
	sop("try run");
	b=a/c;
	return ;
}
显示结果为
try run
finally run
```
再改写try
```
try{
	sop("try run");
	b=a/c;
	System.exit(0);
	}
显示结果为
try run	
```

  


  

 

