title: JAVA同步器Semaphore
date: 2015-08-07 21:15:39
updated: 2015-08-07 21:15:39
tags: Semaphore
categories: JAVA并发
---
Semaphore可以控同时访问资源的线程个数，通过 acquire() 获取一个访问资源许可，如果没有就等待，而 release() 释放一个资源许可

<!--more-->

模拟酒店入住，退房

```java
import java.util.concurrent.Semaphore;
public class JavaSemaphore {
    static Semaphore room=new Semaphore(3);
	public static void main(String[] args) {
		// TODO Auto-generated method stub
	    JavaSemaphore javasemaphore=new JavaSemaphore();
		for(int i=0;i<7;i++)
			javasemaphore.new costomer(room, i).start();
	}
		
	class costomer extends Thread
	{
		private Semaphore s;
		private int id;
		public costomer(Semaphore s,int id)
		{
			this.s=s;
			this.id=id;
		}
		public void run()
		{
			
			try {
				s.acquire();
				System.out.println("costomer "+id +"入住");
				Thread.sleep(3000);
				s.release();
				System.out.println("costomer "+id +"退房");
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

}

```

结果

>costomer 0入住
costomer 2入住
costomer 4入住
costomer 6入住
costomer 0退房
costomer 1入住
costomer 2退房
costomer 3入住
costomer 4退房
costomer 6退房
costomer 5入住
costomer 1退房
costomer 3退房
costomer 5退房
