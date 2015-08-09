title: JAVA序列化与反序列化
date: 2015-08-07 20:19:52
updated: 2015-08-07 20:19:52
tags: 序列化
categories: JAVA基础
---
Java提供一种机制叫做序列化，通过有序的格式或者字节序列持久化java对象，该字节序列包括该对象的数据、有关对象的类型的信息和存储在对象中数据的类型。序列化通常在需要通过网络传输数据，或者保存对象到文件的场合使用。
<!--more-->
注意

 - 序列化的对象要实现标记接口Serializable
 - 若对象持有引用型实例变量，该引用也要实现Serializable接口
 - 若父类实现了Serializabel接口，子类也可序列化
 - 被static，transient修饰的变量不能被序列化
 - serialVersionUID作用：即使对象被序列化之后，它所对应的类被修改了，该对象也依然可以被正确地反序列化
 

例子
```java

public class Student implements Serializable { 
	private static final long serialVersionUID = 6359397991233342078L;
	private int ID;
	private String name;
	private transient int age; //transient 关键字修饰的成员不再被序列化
	private static int total;  //静态成员不能序列化

	
	public Student(int ID,String name,int age)
	{
		this.ID=ID;
		this.name=name;
		this.age=age;
		total++;
	}
	//...setter/getter方法省略
	public void save() throws FileNotFoundException, IOException
	{
		System.out.println("序列化前:");
		System.out.println("ID:"+getID()+"\n name:"+getName()+
				"\n age:"+getAge()+"\n total:"+getTotal());
		ObjectOutputStream os=new ObjectOutputStream(new FileOutputStream("student.txt"));
		os.writeObject(this);
		os.flush();
		os.close();
	}
	
	public void read() throws FileNotFoundException, IOException, ClassNotFoundException
	{
		ObjectInputStream is=new ObjectInputStream(new FileInputStream("student.txt"));
		Student student=(Student) is.readObject();
		System.out.println("反序列化：");
		System.out.println("ID:"+student.getID()+"\n name:"+student.getName()+
				"\n age:"+student.getAge()+"\n total:"+student.getTotal());
		           
	}

	public static void main(String[] args) throws FileNotFoundException, IOException, ClassNotFoundException {
		// TODO Auto-generated method stub
	     Student s=new Student(1, "John", 24);
	     s.save();
	     s.read();

	}
}
```

结果

> 序列化前:
ID:1
 name:John
 age:24
 total:1
反序列化：
ID:1
 name:John
 age:0
 total:1//由于在同一JVM上故可以读出其值



