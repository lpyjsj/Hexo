title: JAVA反射
date: 2015-08-09 08:37:20
updated: 2015-08-09 08:37:20
tags: JAVA反射
categories: JAVA基础
---
Java反射可以获取构造函数，成员变量，方法等反射对象，通过程序化的方式间接对Class对象实例进行操作，同时可以绕过在类外部无法访问私有成员(private,protected)的限制

有两种方式获取Class实例：

* Class静态方法forName(全路径类名)
* ClassLoader实例方法loadClass(全路径类名)
<!--more-->
获取具体类实例的方法(假设Class实例为clazz)：

* clazz.newInstance()方法,只能调用无参构造函数
* clazz.getDeclaredConstructor(Class... arg0).newInstance(Object... arg0)

###测试代码

```
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Student {
	private String name;
	private int num;
	
	public Student()
	{}
	
	public Student(String name,Integer num)
	{
		this.name=name;
		this.num=num.intValue();
	}	
	
	public void setName(String name) {
		this.name = name;
	}


	public void setNum(Integer num) {
		this.num = num.intValue();
	}
	
	public void printInfo()
	{
		System.out.println("name: "+name+"num: "+num);
	}


	public static void main(String[] args) throws ClassNotFoundException, InstantiationException,IllegalAccessException, 
	NoSuchMethodException, 	SecurityException, IllegalArgumentException, InvocationTargetException {
		// TODO Auto-generated method stub
		//通过Class获取Class实例
		Class clazz=Class.forName("Student");
		Student student1=(Student)clazz.newInstance();
		
		student1.setName("Robin");
		student1.setNum(1);
		student1.printInfo();
		
        //通过ClassLoader获取Class实例
		clazz=Thread.currentThread().getContextClassLoader().loadClass("Student");
       //无参构造器
		Constructor cons=clazz.getDeclaredConstructor((Class[])null);
		Student student2=(Student)cons.newInstance();
		//通过反射设置属性
		Method setName=clazz.getDeclaredMethod("setName", String.class);
		setName.invoke(student2, "Pony");
		Method setNum=clazz.getDeclaredMethod("setNum", Integer.class);
		setNum.invoke(student2, 2);		
		student2.printInfo(); 	
		//有参构造器
		Constructor cons2=clazz.getDeclaredConstructor(String.class,Integer.class);
		Student student3=(Student) cons2.newInstance("Jack",3);
		student3.printInfo();
	}

}


```

实验结果
> name: Robin num: 1
name: Pony num: 2
name: Jack num: 3
