# Collections接口（单列集合）：子类是list和set

## 1，添加。

​	boolean add(Object obj):
	boolean addAll(Collection coll):	

## 2，删除。

​	boolean remove(object obj):
	boolean removeAll(Collection coll);
	void clear();

## 3，判断：

​	boolean contains(object obj):
	boolean containsAll(Colllection coll);
	boolean isEmpty():判断集合中是否有元素。根据size()方法判断

## 4，获取：

​	int size():
	Iterator iterator():取出元素的方式：迭代器。

- 该对象必须依赖于具体容器，因为每一个容器的数据结构都不同，所以存取方法都有不同的实现方式。所以每个集合都将取出操作封装成一个对象，并且这个类为集合的**私有内部类**。这些内部类都符合一个规则，抽出Iterator接口。
- 所以该迭代器对象是在容器中进行内部实现的。对于使用容器者而言，具体的实现不重要，只要通过容器获取到该实现的迭代器的对象即可，也就是iterator方法。

```
//使用了Collection中的iterator()方法。 调用集合中的迭代器方法，是为了获取集合中的迭代器对象。
Iterator it = coll.iterator();		
while(it.hasNext()){
	System.out.println(it.next());
}
```

- 写迭代用下面这个方法，因为iterator迭代完就没用了，所以声明为临时变量较好。iter.next指针一开始在所有元素最前面

```
for(Iterator it = coll.iterator(); it.hasNext(); ){
	System.out.println(it.next());
}
```

```
Iterator接口就是对所有的Collection容器进行元素取出的公共接口。
其实就是抓娃娃游戏机中的夹子！	大罐子就是容器，夹子的形状就是迭代器的具体实现（夹子形状不同）
```

- 每一次访问it.next，就要判断一次it.hasNext()。不能判断一次，取两次

![](https://note.youdao.com/yws/public/resource/f2168540caf26c25109a3234bac42594/xmlnote/011C6FC9478948DB92BE306BB1B8142B/50936)

## 5，其他：

​	boolean retainAll(Collection coll);取交集。
	Object[] toArray():将集合转成数组。 

## 6. Collection子接口是List和Set

Collection：

​	|--List：有序(存入和取出的顺序一致),元素都有索引(角标)，元素可以重复。
	|--Set：元素不能重复,无序。

# List

## List 特有方法：

### 1.有一个共性特点就是都可以操作角标。

1. e.g.remove(index) index就是角标

### 2.遍历方式：有Iterator, 循环变量，listIterator三种

### 3.保证元素唯一性的依据：equals方法（contains,remove判断元素相同）

#### 1，添加

​	void add(index,element);
	void addAll(index,collection);

#### 2，删除；

​	Object remove(index):

#### 3，修改：

​	Object set(index,element);

#### 4，获取：

​	Object get(index);
	int indexOf(object);
	int lastIndexOf(object);
	List subList(from,to);  // 包含头不包含尾

#### 5. listIterator（list集合特有的迭代器）

##### Iterator的局限性：只能对元素进行判断、取出、删除

```
Iterator it = list.Iterator();
	while(it.hasNext()){		
		Object obj = it.next();	
		if(obj.equals("abc2")){
			// add方法是集合操作，此时既用集合操作，又用迭代器操作，会引发并发修改异常
			// it.add("abc9");
			it.remove(); // 这里只是将ab2的引用移除了
		}
	}
```

##### ListIterator迭代：可以在迭代过程中完成对元素的增删改查。

```
ListIterator it = list.listIterator();//获取列表迭代器对象
		//它可以实现在迭代过程中完成对元素的增删改查。
		//注意：只有list集合具备该迭代功能.
	while(it.hasNext()){
		Object obj = it.next();	
		if(obj.equals("abc2")){
			it.set("abc9");
		}
	}
```

##### 判断前面是否有元素，逆向遍历。

```
while(it.hasPrevious()){
	System.out.println("previous:"+it.previous());
}
```

## List的三个子类对象:

​	|--Vector:内部是数组数据结构，**是线程同步的**。可变长度（100%延长）**增删，查询都很慢**！现在已经不用了
	|--ArrayList:内部是数组数据结构，**是线程不同步的**。可变长度（50%延长）替代了Vector。**查询的速度快。** 
	|--LinkedList:内部是链表数据结构，是不同步的。**增删元素的速度很快。查询很慢**

### 1.Vector特有方法：elements()相关

Enumeration枚举：vetcor特有的取出方式，和迭代器是一样的，因为枚举的名称过长所以被取代了。

```
public static void main(String[] args) {
		Vector v = new Vector();
		
		v.addElement("abc1");
		v.addElement("abc2");
		
		Enumeration en = v.elements();
		while(en.hasMoreElements()){
		System.out.println("nextelment:"+en.nextElement());
		}
}
```

### 2.ArrayList

内部是数组数据结构，是不同步的。替代了Vector。查询的速度快。

### 3.LinkedList的特有方法

**如果集合中没有元素，以下方法会抛出异常：**

addFirst(),addLast()  // 在首尾添加

getFirst(),getLast()    // 获取但不删除。

removeFirst(),removeLast()        // 获取且删除

**JDK1.6出现替代方法,集合为null，返回Null**

offerFirst(),offerLast()

peekFirst(),peekLast()

pollFirst(),pollLast()

## list练习

### 1.使用LinkedList来模拟一个堆栈或者队列数据结构。

```
public class DuiLie {
	private LinkedList link;

	public DuiLie() {
		link = new LinkedList();
	}

	/**
	 * 队列的添加元素的功能。
	 */
	public void myAdd(Object obj) {
		link.addLast(obj);
	}

	public Object myGet() {
		return link.removeFirst();
	}

	public boolean isNull() {
		return link.isEmpty();
	}
}
```

### 2.定义功能去除ArrayList中的重复元素

```
public static ArrayList getSingleElement(ArrayList al) {
		//1,定义一个临时容器。
		ArrayList temp = new ArrayList();
		//2,迭代al集合。
		Iterator it = al.iterator();
		
		while(it.hasNext()){
			Object obj = it.next();
			//3,判断被迭代到的元素是否在临时容器存在。
			if(!temp.contains(obj)){
				temp.add(obj);
			}
		}
		return temp;
	}
```

### 3. 将自定义对象作为元素保存到arraylist集合中，去除重复元素

1：对人描述，将数据封装进入容器

2：定义容器，将人存入

3：取出

```
public static void main(String[] args) {
		Person p1 = new Person("lisi1",21);
		
		ArrayList al = new ArrayList();
		al.add(p1);
		// 这里进行元素添加的时候进行了向下转型，插入的都是object变量
		al.add(new Person("lisi2",22));
		al.add(new Person("lisi3",23));
		al.add(new Person("lisi4",24));
		
		Iterator it = al.iterator();
		while(it.hasNext()){
			// 下面这样取编译会报错，因为it是object类型，需要向下转型
//			System.out.println((it.next()).getName()+"::"+(it.next()).getAge());
//			System.out.println(((Person) it.next()).getName()+"::"+((Person) it.next()).getAge());
			Person p = (Person) it.next();
			System.out.println(p.getName()+"--"+p.getAge());
		}
//		al.add(5);//al.add(new Integer(5));
	}
```

### 4. 将重复的person对象删除

- arraylist使用的是对象的equals方法进行对象是否相同的判断，object的equals判断地址，每个对象都是new出来的，所以不一样。

```
public boolean equals(Object obj) {
	if(this == obj)
		return true;
	if(!(obj instanceof Person))
		throw new ClassCastException("类型错误");
	// 从这个打印可以看出，复用题目2中的判断重复，调用了contaiuns方法，contains方法又通过对每个元素调用equals方法判断重复
//	System.out.println(this+"....equals....."+obj);
	Person p = (Person)obj;		
	return this.name.equals(p.name) && this.age == p.age;
}
```

- 复用题目2中的判断重复，调用了contains方法，contains方法又通过对每个元素调用equals方法判断重复。测试发现，remove底层也是调用equals方法进行判断

# Set：无序

## 1.HashSet

### 1）底层数据结构是hash表，线程非同步。

### 2）保证元素唯一性的依据：hashCode->equals

​	HashSet集合数据结构是哈希表，所以存储元素的时候，使用的元素的**hashCode方法来确定位置**；如果位置相同，**再通过元素的equals来确**定是否是同一个对象。所以自定义对象都要覆写hashcode,equals方法。添加成功返回true,否则返回false

### 3）遍历方式：hashset只有iterator一种遍历方式

## 练习：

### 1. 将person类数据添加到set中--直接写，产生重复

```
public class Person{
	private String name;
	private int age;
	
	public Person(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}
}	

hs.add(new Person("lisi7",27));
hs.add(new Person("lisi7",27));
```

- 如果直接这么写，会添加两个相同的person，因为都是new 对象，两个person对象的hashCode是不一样的，所以都会加进去。

### 2. 手工给定hash值，定义equals方法

注意覆写：equals(Object obj)

```
	@Override
	public int hashCode() {
		return name.hashCode()+age*27;
// 		手动给定hash具体值，不太好
//		return 100;
	}
	@Override
	public boolean equals(Object obj) {	
		if(this == obj)
			return true;
		if(!(obj instanceof Person))
			throw new ClassCastException("类型错误");
		
		Person p = (Person)obj;
		return this.name.equals(p.name) && this.age == p.age;
	}
```

### 保证元素唯一性的依据：（contains，remove调用）先hashcode,再equals

## 2.TreeSet：可以对set集合中的元素排序

### 1）底层数据结构：二叉树,线程不同步

### 2）保证元素唯一性的依据：compareTo返回0

### 3）两种排序方式:当两种方式都存在时，以比较器为准

## 练习：

### 1. 存储自定义对象，自定义类型需实现comparable接口

```
public class Person /*extends Object*/ implements Comparable {
	@Override
	public int compareTo(Object o) {
		if(!(obj instanceof Person))
			throw new RuntimeException("不是人")
		Person p = (Person)o;
		if(this.age>p.age)
			return 1;
		if(this.age<p.age)
			return -1;	
		else
			return 0;
	}
}
```

- 上面问题：只判断了年龄，ts.add(new Person("z",2));ts.add(new Person("w",2)); 会返回0，就代表是相同对象。所以w加不进去。
- 所以应该**主要条件相同时，再判断次要条件**：年龄，名字都不同。

```
public int compareTo(Object o) {
	Person p = (Person)o;
	int  temp = this.age-p.age;
	return temp==0?this.name.compareTo(p.name):temp;		
}
```

### 2. 按加入顺序排序

```
public int compareTo(Object o) {
	return 1;	
}
```

#### 1）对象类实现Comparable接口，覆盖compareTo方法，该接口定义的就是元素的自然排序，即默认排序

#### 2）当元素自身不具备比较性时，或者具备的比较性不是所需要的，就需要让集合自身具备比较性。

即：集合在初始化时，就具有比较性。定义比较器，实现Comparator接口，实现compare方法。将比较器对象作为参数传递给TreeSet集合的构造函数

```
TreeSet ts = new TreeSet(new ComparatorByLength())
public class ComparatorByLength implements Comparator {
	@Override
	public int compare(Object o1, Object o2) {
		String s1 = (String)o1;
		String s2 = (String)o2;
		
		int temp = s1.length()-s2.length();
		// 字符串的compareTo方法
		return temp==0? s1.compareTo(s2): temp;
	}
}
```

匿名内部类

```
TreeSet ts = new TreeSet(new ComparatorByLength()
{
	public int compare(Object o1, Object o2) {
		String s1 = (String)o1;
		String s2 = (String)o2;
		
		int temp = s1.length()-s2.length();
		// 字符串的compareTo方法
		return temp==0? s1.compareTo(s2): temp;
	}
});
```

