title: java 多线程
date: 2015-07-17 18:57:20
updated: 2015-07-17 18:57:20
tags: Java Thread 
categories: JAVA基础
---
####Thread创建的两种方式
1. 实现runnable接口，实现run()方法
2. 继承Thread类
####ExecutorService类，Callable接口和Future对象
上面两种方式执行完任务是没有返回值的，如果想要任务返回值，需要实现callable接口中的call()方法，利用线程池执行时返回Future对象
```
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;


public class ThreadTest {

	/**
	 * @param args
	 * @throws ExecutionException 
	 * @throws InterruptedException 
	 */
	@SuppressWarnings("unchecked")
	public static void main(String[] args) throws InterruptedException, ExecutionException {
		// TODO Auto-generated method stub
      ExecutorService pool=Executors.newFixedThreadPool(2);
      
      ThreadTest test=new ThreadTest();
      
      pool.execute(test.new runnable());

      pool.execute(test.new thread());//execute()参数只能是Runnable
      
      Future result=pool.submit(test.new callable());//submit()参数可以是Runnable，Callable
      System.out.println(result.get().toString());
      
      pool.shutdown();  
           
		
	}
	
 class runnable implements Runnable
 {

	@Override
	public void run() {
		// TODO Auto-generated method stub
		System.out.println("thread created by implements runnnable");
	}
	 
 }

 class callable implements Callable
 {

	@Override
	public Object call() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("task created by implements callable");
		return "return value from callable";
	}
	 
 }
 
 class thread extends Thread
 {
	 public void run()
	 {
		System.out.println("thread created by extending Thread"); 
	 }
	 
 }
 
}

```
