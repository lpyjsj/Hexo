title: java线程同步
date: 2015-07-18 19:45:34
updated: 2015-07-18 19:45:34
tags: 线程同步
categories: JAVA并发
---
###同步的两种方式
1. synchronized关键字同步
2. 可重入锁ReentrantLock同步

下面的例子分别使用两种方式实现同步

```
public class JavaSynchronize {
	public int count = 0;

	public void increment() {
		synchronized (this) {
			count++;
		}
	}

	public void test() {
		for (int i = 0; i < 20; i++)
			new Thread(new Runnable() {
				public void run() {
					increment();
					System.out.println("count=" + count);
				}
			}).start();
	}
}

```

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class JavaLock {
	private int count = 0;
	private Lock lock = new ReentrantLock();

	public void increment() {
		lock.lock();
		try {
			count++;
		}

		finally {
			lock.unlock();
		}

	}

	public void test() {
		for (int i = 0; i < 20; i++)
			new Thread(new Runnable() {
				public void run() {
					increment();
					System.out.println("count=" + count);
				}
			}).start();
	}
}

```

###两种方式的差异

*  synchronized是java关键字，是java语言的内置实现，Lock是接口
*  synchronized发生异常时，自动释放锁，Lock必须主动通过unlock()释放锁，为防止死锁，需在finally中释放锁
*  Lock可以响应中断，synchronized则不行
*  如果竞争激烈，Lock性能优于synchronized

参考资料：
http://www.cnblogs.com/dolphin0520/p/3923167.html