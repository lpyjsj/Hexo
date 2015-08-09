title: JAVA动态代理
date: 2015-07-17 19:31:40
updated: 2015-07-17 19:31:40
tags: [JDK动态代理,CGLib]
categories: JAVA基础
---
java有两种动态代理，JDK动态代理和CGLib动态代理，二者的区别如下
*  代理范围：JDK代理只能代理实现了接口的类，CGLib可以代理没有实现接口的类（spring AOP 中如果类实现了接口，默认使用JDK代理）
*  实现原理：CGLib使用底层的字节码技术，为被代理类生成子类，在子类中使用方法拦截技术拦截父类方法的调用，因为是继承，所以被代理类和方法最好不要声明为final（注意：CGlib代理依赖CGLib.jar和asm.jar)
*  性能：CGGlib创建的代理对象要比JDK的性能高很多，但是创建时所花费的时间却比JDK动态代理要多，CGLib适合单例等生命周期比较长的对象。
<!--more-->
##例子
>接口和被代理类

```java
public interface subjectInterface {
	public void doTask();
}

public class Subject implements subjectInterface {
	@Override
	public void doTask() {
		// TODO Auto-generated method stub
		System.out.println("执行任务");
	}
}

```

>JDK代理

```
import java.lang.reflect.*;

public class JDKDynamicProxy implements InvocationHandler {
	private Object target;// 被代理对象

	// 返回代理对象
	public Object bindTarget(Object target) {
		this.target = target;
		return Proxy.newProxyInstance(target.getClass().getClassLoader(),
				target.getClass().getInterfaces(), this);
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {
		// TODO Auto-generated method stub
		Object result = null;

		result = method.invoke(target, args);// 实现代理

		return result;
	}

}

```

>CGLib 代理

```
import java.lang.reflect.Method;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

public class CGlibProxy implements MethodInterceptor {

	private Object target;

	// 创建代理对象
	public Object bindTarger(Object target) {
		this.target = target;// 设置被代理对象
		Enhancer enhancer = new Enhancer();
		enhancer.setSuperclass(this.target.getClass());// 设置要创建的子类的父类
		enhancer.setCallback(this);// 回调方法
		return enhancer.create();// 通过字节码技术动态创建子类
	}

	@Override
	// 拦截父类方法的调用，可在这里织入横切逻辑
	public Object intercept(Object obj, Method method, Object[] args,
			MethodProxy proxy) throws Throwable {
		// TODO Auto-generated method stub
		Object result = proxy.invokeSuper(obj, args);// 调用父类方法
		return result;
	}

}

```

>测试

```
public class ProxyTest {
	public static void main(String[] args) {
		Subject subject = new Subject();
		JDKDynamicProxy proxy = new JDKDynamicProxy();
		subjectInterface s1 = (subjectInterface) proxy.bindTarget(subject);
		System.out.println("使用JDK动态代理");
		s1.doTask();

		CGlibProxy cglibproxy = new CGlibProxy();
		Subject s2 = (Subject) cglibproxy.bindTarger(subject);
		System.out.println("使用CGLib代理");
		s2.doTask();

	}

}

```

>结果

使用JDK动态代理
执行任务
使用CGLib代理
执行任务
