title: JAVA同步器CountDownLatch CyclicBarrier
date: 2015-08-08 10:41:16
updated: 2015-08-08 10:41:16
tags: [CountDownLatch,CyclicBarrier]
categories: JAVA并发
---

####CountDownLatch
可以实现类似计数器的功能，一般用于某个线程等待若干个其他线程执行完任务之后，它才执行
####CyclicBarrier
一组线程互相等待至某个状态，然后这一组线程再同时执行
<!--more-->

 - CountDownLatch例子

```
import java.util.concurrent.CountDownLatch;

public class JavaCountDownLatch {

	public static void main(String[] args) throws InterruptedException {
		CountDownLatch countDownLatch = new CountDownLatch(5);
		for (int i = 0; i < 5; i++)
			new JavaCountDownLatch.Student(countDownLatch, i).start();
		System.out.println("等待所有同学到达");
		countDownLatch.await();
		System.out.println("所有同学到达目的地");

	}

	static class Student extends Thread {
		private CountDownLatch countDownLatch;
		private int id;

		public Student(CountDownLatch countDownLatch, int id) {
			this.countDownLatch = countDownLatch;
			this.id = id;
		}

		public void run() {
			try {
				System.out.println("student " + id + "出发");
				Thread.sleep(3000);
				System.out.println("student " + id + "到达");
				countDownLatch.countDown();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}

```

> 执行结果：
> student 0出发
等待所有同学到达
student 2出发
student 4出发
student 1出发
student 3出发
student 0到达
student 2到达
student 4到达
student 1到达
student 3到达
所有同学到达目的地

 

 - CyclicBarrier例子
 

```
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class JavaCyclicBarrier {
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		CyclicBarrier cyclicBarrier = new CyclicBarrier(5);
		for (int i = 0; i < 5; i++)
			new JavaCyclicBarrier.Student(cyclicBarrier, i).start();
	}

	static class Student extends Thread {
		private CyclicBarrier cyclicBarrier;
		private int id;
		public Student(CyclicBarrier cyclicBarrier, int id) {
			this.cyclicBarrier = cyclicBarrier;
			this.id = id;
		}
		public void run() {
			try {
				System.out.println("student " + id + "出发");
				Thread.sleep(3000);
				System.out.println("student " + id + "到达,等待其他同学");
				cyclicBarrier.await();
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			} catch (BrokenBarrierException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			System.out.println("所有同学到达目的地");
		}
	}
}

```

> 执行结果：
> student 1出发
student 3出发
student 0出发
student 2出发
student 4出发
student 3到达,等待其他同学
student 1到达,等待其他同学
student 0到达,等待其他同学
student 2到达,等待其他同学
student 4到达,等待其他同学
所有同学到达目的地
所有同学到达目的地
所有同学到达目的地
所有同学到达目的地
所有同学到达目的地


参考资料

 - http://www.cnblogs.com/dolphin0520/p/3920397.html
 - http://blog.csdn.net/mazhimazh/article/details/19190241


