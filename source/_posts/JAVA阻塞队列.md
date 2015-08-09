title: JAVA阻塞队列
date: 2015-08-08 15:07:35
updated: 2015-08-08 15:07:35
tags: BlockingQueue
categories: JAVA并发
---
阻塞队列，顾名思义，当队列为空取元素或者队列满了放元素时对线程进行阻塞
阻塞队列有以下几种：

 - ArrayBlockingQueue 基于数组实现，定义时需指定大小
 - LinkedBlockingQueue 基于链表实现
 - PriorityBlockingQueue 基于堆实现
 - DelayQueue 延时阻塞队列

 <!--more-->
用ArrayBlockingQueue实现生产者消费者模型

```
import java.util.concurrent.ArrayBlockingQueue;

public class JavaBlockQueue {
	public ArrayBlockingQueue<Integer> arrayBlockingQueue = new ArrayBlockingQueue<Integer>(5);
	public int count = 0;

	public static void main(String[] args) {
		JavaBlockQueue javaBlockQueue = new JavaBlockQueue();
		new Thread(javaBlockQueue.new Consumer()).start();
		new Thread(javaBlockQueue.new Producer()).start();

	}

	class Consumer implements Runnable {
		@Override
		public void run() {
			try {
				while (true) {
					System.out.println("取出" + arrayBlockingQueue.take());
				}
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

	class Producer implements Runnable {
		@Override
		public void run() {
			try {
				while (true) {
					arrayBlockingQueue.put(++count);
				    System.out.println("存入 " + count);
				}
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
}

```

> 测试结果
> 存入 25449
存入 25450
存入 25451
存入 25452
存入 25453
取出25449
取出25450
取出25451
取出25452
取出25453