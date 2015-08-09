title: JAVA Fork/Join 框架
date: 2015-08-07 11:03:27
updated: 2015-08-07 11:03:27
tags: Fork/Join
categories: JAVA并发
---
JAVA Fork/Join 框架是一个任务并行计算框架，将大任务划分为小任务，分而治之

核心操作

 - fork：将大任务划分为小任务
 - join： 任务等待其创建的任务的结束
 
核心类

 - ForkJoinPool：ForkJoinTask需要通过ForkJoinPool来执行，实现ExecutorService接口和work-stealing算法，管理工作线程和提供关于任务的状态和它们执行的信息
 - ForkJoinTask：提供fork、join操作，它有两个子类，RecursiveAction没有返回结果的任务和RecursiveTask 有返回结果的任务
 <!--more-->
 
例子

```java
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.Future;
import java.util.concurrent.RecursiveTask;

public class JavaForkJoin extends RecursiveTask<Integer>{
	private int n;
	public JavaForkJoin(int n)
	{this.n=n;}
	@Override
	//模拟斐波那契数列
	protected Integer compute() {
		// TODO Auto-generated method stub
		int sum=0;
		if(n<=1)
			 sum=1;
		else
		{
			JavaForkJoin f1=new JavaForkJoin(n-1);
			f1.fork();
			JavaForkJoin f2=new JavaForkJoin(n-2);
			f2.fork();
			sum=f1.join()+f2.join();
		}
		return sum;
	}

	public static void main(String[] args) throws InterruptedException, ExecutionException {
		// TODO Auto-generated method stub
		ForkJoinPool pool=new ForkJoinPool();
		for(int n=0;n<8;n++)
		{	
		  Future<Integer> result=pool.submit(new JavaForkJoin(n));
		  System.out.println("Fibonacci("+n+")="+result.get());
		}
	}
}
```

执行结果

> 
Fibonacci(0)=1
Fibonacci(1)=1
Fibonacci(2)=2
Fibonacci(3)=3
Fibonacci(4)=5
Fibonacci(5)=8
Fibonacci(6)=13
Fibonacci(7)=21

 
参考链接

 - http://www.williamsang.com/archives/1724.html
 - http://ifeve.com/fork-join-1
 - http://www.infoq.com/cn/articles/fork-join-introduction
 

 
 
 

 

 

