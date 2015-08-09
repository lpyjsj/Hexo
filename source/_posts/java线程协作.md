title: JAVA线程协作
date: 2015-07-18 19:24:38
updated: 2015-07-18 19:24:38
tags: 线程协作
categories: JAVA并发
---

###协作方式
*  基于对象的内置锁,在同步方法或同步块中使用wait(),notify(),notifyAll()
*  Lock配合Condition对象的await(),signal(),signalAll()
<!--more-->
下面的例子模拟多线程环境下的存款取款业务

####1.第一种方式实现

```java
public class JavaInnerLock {

	private int balance = 0;

	public synchronized void save(int x) {
		balance += x;
		System.out.println("存入" + x + "元,现有余额" + balance + "元");
		notify();

	}

	public synchronized void draw(int x) throws InterruptedException {
		while (balance < x) {
			System.out.println("欲取" + x + "元," + "余额不足");
			wait();
		}
		balance -= x;
		System.out.println("取出" + x + "元,现有余额" + balance + "元");
	}

	public void test() {
		for (int i = 0; i < 5; i++) {
			new Drawer(this).start();
			new Saver(this).start();

		}
	}

}

class Saver extends Thread {
	private JavaInnerLock account;

	public Saver(JavaInnerLock account) {
		this.account = account;
	}

	public void run() {
		int money = (int) (Math.random() * 100);
		account.save(money);
	}
}

class Drawer extends Thread {
	private JavaInnerLock account;

	public Drawer(JavaInnerLock account) {
		this.account = account;
	}

	public void run() {
		int money = (int) (Math.random() * 100);
		try {
			account.draw(money);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}

```
####2.第二种方式实现
```
package test;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class JavaCondition {

	private int balance = 0;
	private Lock lock = new ReentrantLock();
	private Condition draw = lock.newCondition();

	public void save(int x) {
		lock.lock();
		try {
			balance += x;
			System.out.println("存入" + x + "元,现有余额" + balance + "元");
			draw.signal();
		} finally {
			lock.unlock();
		}

	}

	public void draw(int x) throws InterruptedException {
		lock.lock();
		try {
			while (balance < x) {
				System.out.println("欲取" + x + "元," + "余额不足");
				draw.await();
			}
			balance -= x;
			System.out.println("取出" + x + "元,现有余额" + balance + "元");
		} finally {
			lock.unlock();
		}
	}

	public void test() {
		for (int i = 0; i < 5; i++) {
			new Drawer(this).start();
			new Saver(this).start();

		}
	}

}

class Saver extends Thread {
	private JavaCondition account;

	public Saver(JavaCondition account) {
		this.account = account;
	}

	public void run() {
		int money = (int) (Math.random() * 100);
		account.save(money);
	}
}

class Drawer extends Thread {
	private JavaCondition account;

	public Drawer(JavaCondition account) {
		this.account = account;
	}

	public void run() {
		int money = (int) (Math.random() * 100);
		try {
			account.draw(money);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}

```

####3测试结果

欲取94元,余额不足
欲取24元,余额不足
欲取78元,余额不足
欲取24元,余额不足
存入89元,现有余额89元
欲取94元,余额不足
存入65元,现有余额154元
存入18元,现有余额172元
取出78元,现有余额94元
存入73元,现有余额167元
存入42元,现有余额209元
取出24元,现有余额185元
取出94元,现有余额91元
取出24元,现有余额67元
取出23元,现有余额44元


