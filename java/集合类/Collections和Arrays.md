# 一：Collections:是集合框架的工具类。全是静态方法。

## 1）sort方法

不能给tree排序

### 定义说明：

```
public static <T extends Comparable<? super T>> void sort(List<T> list)
```

- 传入list，不知道list中的类型，所以用泛型List<T>
- T可以任意，但必须具备比较性，即为comparable的子类：T extends  Comparable
- 比较器一般都是传父类：? super T

## 2）binarysearch

只能对List排序，必须是有序的

返回下标，（-插入点-1），插入点就是我们二分查找时候的min，

## 3）fill,replaceAll方法

Collections.fill(list,"oo")  将集合中的元素全部替代为新的值"oo"

Collections.replaceAll(list,"pp","oo")  将集合中所有“pp”全部替代为新的值"oo"

## 4）reverseorder

```
TreeSet<String> ts = new TreeSet<String>(Collections.reverseOrder(new ComparatorByLength()));	
ComparatorByLength()是自己写的比较器，上面方法是将自己写的方法逆转
```

## 5）线程安全

所有集合对象都是线程不安全的，同时collections提供了线程安全的各种集合，这些集合内部就是把原有操作封装到一个同步代码块中。

# 二：Arrays:操作数组的工具类，里面都是静态方法

## 1）asList数组变集合

### - 为什么要将数组变集合：

将数组变成List集合，可以用集合的思想和方法来操作数组中的元素。

注意：不能使用集合的增删方法，因为数组长度是固定的。

### - 操作

```
int[] nums = {2,3,4};
List li = Arrays.asList(nums);
```

- 以上打印出来的是一个数组的哈希值，是把Nums作为集合中的一个元素存在。应该写成`List<int[]> li = Arrays.asList(nums);`
- 改为`Integer[] nums = {2,3,4};`即可

```
String[] nums = {"sss","ss"};
List li = Arrays.asList(nums);
```

- 以上打印出来的就是`{"sss","ss"}`

### 总结

- 如果数组中的元素都是对象，那变成集合时，数组中的元素就直接转成集合中的元素
- 如果数组中的元素都是基本数据类型，那么会将该数组作为集合中的元素存在

## 2）toArray集合变数组（collections中的方法）

使用的就是Collection接口中的toArray方法。

### - 为什么要将集合变成数组

		 * 集合转成数组：可以对集合中的元素操作的方法进行限定。不允许对其进行增删。
* toArray方法需要传入一个指定类型的数组。

### 长度该如何定义呢？

1. 如果长度小于集合的size，那么该方法会创建一个同类型并和集合相同size的数组。
2. 如果长度大于集合的size，那么该方法就会使用指定的数组，存储集合中的元素，其他位置默认为null。
3. 所以建议，最后长度就指定为，集合的size。

```
String[] arr = list.toArray(new String[list.size()]);
```

# Iterable接口和高级for循环

## foreach语句：

### - 格式：

```
for(类型  变量   ：Collection集合|数组)
{
}
```

### - 传统for和高级for的区别？

- 传统for可以完成对语句执行很多次，因为可以定义控制循环的增量和条件。
- 高级for是一种简化形式。有局限性：**它必须有被遍历的目标**。该目标要是数组，要么是**Collection单列集合**（支持iterator的）。（e.g. 打印100次‘a’就做不了，map也打印不了，因为map不支持iterator）

### - 局限性

- 对集合，数组的遍历只能获取集合元素，但是不能对集合进行操作
- 迭代器除了遍历，还可以进行remove集合，listIterator都可以操作。

### - 用法

- 如果仅仅是获取数组中的元素，可以使用高级for。
- 如果要对数组的角标进行操作建议使用传统for。 
- 如何打印map

```
//可以使用高级for遍历map集合吗？不能直接用，但是可以将map转成单列的set，就可以用了。
Map<Integer,String> map = new HashMap<Integer,String>();

for(Integer key : map.keySet()){
	String value = map.get(key);
	System.out.println(key+"::"+value);
}
		
for(Map.Entry<Integer,String> me : map.entrySet()){
	Integer key = me.getKey();
	String value = me.getValue();
	System.out.println(key+":"+value);
}
```

# 可变参数

## 思考过程

对n个数，n不确定进行相加，可以用函数重载，太麻烦了，可以传数组，但还要新建数组。思考历程是：函数重载---传数组

## 函数的可变参数

其实就是一个数组，但是接收的是数组的元素。自动将这些元素封装成数组。简化了调用者的书写。

## 注意

可变参数类型，必须定义在参数列表的结尾。其他固定的参数可以写在前面

```
public static int newAdd(int a,int...  arr){
	int sum = 0;
	for (int i = 0; i < arr.length; i++) {
		sum+=arr[i];
	}
	return sum;
}
```

# 静态导入

## 原来

Arrays里面的binarySearch,max方法，在调用的时候前面都要写全类名.方法名：

```
Arrays.sort(list);
```

## 现在

```
import static java.util.Collections.*;//静态导入，其实导入的是类中的静态成员。
//import static java.util.Collections.max;//静态导入，其实到入的是类中的静态成员。
// system里面的都是静态类
import static java.lang.System.*;

public class StaticImportDemo {
	public static void main(String[] args) {
		List<String> list = new ArrayList<String>();
		
		list.add("abc3");
		list.add("abc7");
		
		sort(list);
		out.println(list);		
		String max = max(list);
	}
}
```

## 注意：

1：当类名重名时，要指定具体的包名

2：当方法重名时，要指定所属的对象或类

如下，Arrays和object里面都有toString方法，所以要指定类名

```
class test extends Object
{ 
	Arrays.toString(arr);
}
```

