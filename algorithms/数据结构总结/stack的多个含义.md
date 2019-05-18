# 含义说明

## **含义一：数据结构**

stack的第一种含义是一组数据的[存放方式](http://en.wikipedia.org/wiki/Stack_(abstract_data_type))，特点为LIFO，即后进先出（Last in, first out）。

![数据结构stack](http://www.ruanyifeng.com/blogimg/asset/201311/bg2013112901.png)

在这种数据结构中，数据像积木那样一层层堆起来，后面加入的数据就放在最上层。使用的时候，最上层的数据第一个被用掉，这就叫做"后进先出"。

与这种结构配套的，是一些特定的方法，主要为下面这些。

> - push：在最顶层加入数据。
> - pop：返回并移除最顶层的数据。
> - top：返回最顶层数据的值，但不移除它。
> - isempty：返回一个布尔值，表示当前stack是否为空栈。

## **含义二：代码运行方式**

stack的第二种含义是["调用栈"](http://en.wikipedia.org/wiki/Call_stack)（call stack），表示函数或子例程像堆积木一样存放，以实现层层调用。

下面以一段Java代码为例（[来源](http://www.itcsolutions.eu/2011/02/06/tutorial-java-8-understand-stack-and-heap/)）。

> ```
> class Student{
>     int age;              
>     String name;      
> 
>     public Student(int Age, String Name)
>     {
>         this.age = Age;
>         setName(Name);
>     }
>     public void setName(String Name)
>     {
>         this.name = Name;
>     }
> }
> 
> public class Main{
>     public static void main(String[] args) {
>             Student s;           
>             s = new Student(23,"Jonh");
>     }
> }
> ```

上面这段代码运行的时候，首先调用main方法，里面需要生成一个Student的实例，于是又调用Student构造函数。在构造函数中，又调用到setName方法。

![调用栈](http://www.ruanyifeng.com/blogimg/asset/201311/bg2013112902.gif)

这三次调用像积木一样堆起来，就叫做"调用栈"。程序运行的时候，总是先完成最上层的调用，然后将它的值返回到下一层调用，直至完成整个调用栈，返回最后的结果。

## **含义三：内存区域**

stack的第三种含义是[存放数据的一种内存区域](http://en.wikipedia.org/wiki/Stack-based_memory_allocation)。程序运行的时候，需要内存空间存放数据。一般来说，系统会划分出两种不同的内存空间：一种叫做stack（栈），另一种叫做heap（堆）。

1. 栈是为执行线程留出的内存空间。

   - 当线程创建的时候，操作系统（OS）为每一个系统级（system-level）的线程分配栈，并设置栈的大小 
   - 当函数被调用的时候，栈顶为局部变量和一些 bookkeeping 数据预留块。当函数执行完毕，块就没有用了，可能在下次的函数调用的时候再被使用。

   栈通常用后进先出（LIFO）的方式预留空间；因此最近的保留块（reserved block）通常最先被释放。这么做可以使跟踪堆栈变的简单；从栈中释放块（free block）只不过是指针的偏移而已。 

2. 堆（heap）是为动态分配预留的内存空间。操作系统通过调用语言的运行时（runtime）去为应用程序分配堆。 在应用程序启动的时候，设置堆的大小，但是可以在需要的时候扩展

   和栈不一样，从堆上分配和重新分配块没有固定模式；你可以在任何时候分配和释放它。这样使得跟踪哪部分堆已经被分配和被释放变的异常复杂；有许多定制的堆分配策略用来为不同的使用模式下调整堆的性能。 

### 主要区别：

1:  stack是有结构的，每个区块按照一定次序存放，可以明确知道每个区块的大小；

​     heap是没有结构的，数据可以任意存放。因此，stack的寻址速度要快于heap。

2:  每个线程分配一个stack，每个进程分配一个heap，也就是说，stack是线程独占的，heap是线程共用的。

3:  此外，stack创建的时候，大小是确定的，数据超过这个大小，就发生stack overflow错误，而heap的大小是不确定的，需要的话可以不断增加。

4: 堆的增加通常从低地址向高地址扩展 

​     栈的地址从高向低扩展

根据上面这些区别，数据存放的规则是：只要是局部的、占用空间确定的数据，一般都存放在stack里面，否则就放在heap里面。请看下面这段代码（[来源](http://www.codeproject.com/Articles/76153/Six-important-NET-concepts-Stack-heap-value-types)）。

> ```
> public void Method1()
> {
>     int i=4;
> 
>     int y=2;
> 
>     class1 cls1 = new class1();
> }
> ```

上面代码的Method1方法，共包含了三个变量：i, y 和 cls1。其中，i和y的值是整数，内存占用空间是确定的，而且是局部变量，只用在Method1区块之内，不会用于区块之外。cls1也是局部变量，但是类型为指针变量，指向一个对象的实例。指针变量占用的大小是确定的，但是对象实例以目前的信息无法确知所占用的内存空间大小。

这三个变量和一个对象实例在内存中的存放方式如下。

![内存空间stack实例](http://www.ruanyifeng.com/blogimg/asset/201311/bg2013112905.jpg)

从上图可以看到，i、y和cls1都存放在stack，因为它们占用内存空间都是确定的，而且本身也属于局部变量。但是，cls1指向的对象实例存放在heap，因为它的大小不确定。作为一条规则可以记住，所有的对象都存放在heap。

接下来的问题是，当Method1方法运行结束，会发生什么事？

回答是整个stack被清空，i、y和cls1这三个变量消失，因为它们是局部变量，区块一旦运行结束，就没必要再存在了。而heap之中的那个对象实例继续存在，直到系统的垃圾清理机制（garbage collector）将这块内存回收。因此，一般来说，内存泄漏都发生在heap，即某些内存空间不再被使用了，却因为种种原因，没有被系统回收。

# JAVA中的堆栈

## **Java Heap Memory**

堆内存（heap memory）是被用来在**runtime**的时候给

1. 对象
2. jre的那些class

分配内存的。

不管你何时创建对象，创建任何一个对象，这些对象都是被创建在了heap空间里的。那个我们熟悉的gc（垃圾回收站）负责把那些不再被引用（reference）的对象从heap memory中清理掉，这也是gc的职责所在。在heap空间里创建的任何对象都是全局访问的。可以被应用程序的任何地方引用。

## **Java Stack Memory**

​	java里的stack内存（stack memory）是被用来线程的执行的。也就是stack是线程级别的。而heap是对象级别的。

​	stack里边包含了

1. 方法里边那些定义的值，这些值随着一次方法执行完毕后就消失了；
2. 还包含了引用地址。这个引用地址就是对存放在heap memory中的一个链接。你可以理解为关系数据库里边的外键，nosql中的外链。

​       stack memory由于她是个stack结构。所以遵循LIFO，就是后进先出的顺序。一个方法不论什么时候被调用，一个针对该方法的全新的block就会在stack memory里被创建，用来存储这个方法里的本地基本类型的值以及这个方法对其他对象的引用地址。一旦方法执行结束，这个block的状态就变为unused了，就是变为了空闲状态，就变为available了。宣布单身了，等着下一个method来用她。stack memory的size相比heap memory的size要小得多。

## 举例

> Memory.java
>
>  
>
> public class Memory {
>
> ​    public static void main(String[] args) { // Line1
>
> ​        int i=1; // Line 2
>
> ​        Object obj = new Object(); // Line 3
>
> ​        Memory mem = new Memory(); // Line 4
>
> ​        mem.foo(obj); // Line 5
>
> ​    } // Line 9
>
>  
>
> ​    private void foo(Object param) { // Line 6
>
> ​        String str = param.toString(); //// Line 7
>
> ​        System.out.println(str);
>
> ​    } // Line 8
>
> }

下面这个图就展示了在上面这个程序中的stack和heap memory的存储和引用关系。堆栈怎么被用来存储基本类型值（primitive value）以及对象以及对象的引用。

![img](http://mmbiz.qpic.cn/mmbiz/LsNc01I3kx58GCSVftONAwF5vZFYKFzZUJ79c9NWgv5zFpJQpk30hZ6zD8R1AZCiaZPia8zQKFvBfVvskHsEKsaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

###  一步步看上面program的执行情况。

​	•	一旦我们运行了这个程序，它就会把所有的runtime class  load 到 heap空间。当main()方法在line1那个地方被发现后，Java Runtime就会创建stack memory给main()方法这个线程来用。

​	•	在line2那个地方，我们创建了一个primitive（基本类型）的局部变量，这个变量自然是被存储到了main()的stack memory里的。

​	•	在line3那个位置，我们创建了一个对象，按照前面说的，这个对象自然是存储在heap memory里边的，并且在stack memory里边也有个这个对象的引用地址被存储了进去。line4的对象创建过程和line3是一样的。

​	•	 现在我们来到了line5这个地方，这一行我们调用了foo()方法，这时候一个block在stack的顶部被创建，这个block现在专门为foo()方法服务。由于java是按值传递，所以在line6那个位置一个新的对象引用就会在foo() 方法的stack block中被创建。

​	•	在line7那个位置，一个字符串被创建，这个串是在heap空间的string池（String Pool）中。并且对这个string对象的引用自然也在foo()方法的stack空间里被创建了。

​	•	在line8那个地方foo()方法就被终止了，在方法结束的时候，在stack中为foo()分配的那个block重新变回空窗期，宣布available了。

​	•	在line9那个地方，main()方法也要结束了。自然为main()创建的stack memory就会被destory掉了。自此，Java Runtime 释放所有的memory然后结束程序的执行！

### **heap memory与stack memory的区别**

基于上面的那一堆解释，我们现在可以很轻松的总结出以下不同之处：

​	1	heap用在一个application的很多地方，但stack memory只用于一个线程的执行。前者是对象级别，后者是线程级别。

​	2	只要是对象的创建，都是被存储到heap space中，同时stack中有这个对象的引用地址。stack memory中只包含基本类型变量和存储在heap space中的对象的引用变量。

​	3	存储在heap中的对象是全局都可以访问的，然而stack memory不能被其他线程访问。

​	4	stack 的内存管理是使用LIFO的，然而heap的内存管理要更复杂，因为heap是被全局使用的。因此heap memory被分为Young-Generation，Old-Generation等等，有关这个你可以去了解下java的gc（垃圾回收）制。

​	5	stack memory是短命的，然而heap memory则是application运行的整个生命周期都有他，直到application结束掉。

​	6    我们使用**-Xms**和**-Xmx** jam 参数来定义heap memory的启动size和最大size。stack memory的size则是使用**-Xss。**

​	7    当stack memory满了，那么Java runtime就会抛出一个java.lang.StackOverFlowError的异常。当heap memory 满了，则会抛出java.lang.OutOfMemoryError: Java Heap Space error.

​	8	 stack memory相比heap memory来说是非常小的。另外由于内存分配比较简单（LIFO），stack memory相比heap memory来说是非常快的。

# C语言的堆栈

c语言的堆栈，是指一个程序运行起来系统给分配的区域，总共5个，堆栈区，简称栈区，系统自动释放的区域。堆区，需要程序员自己手动开辟和释放（malloc，free）的区域。静态区（全局区）static。常量区（字符串常量等）。代码区。（这里指的是用户栈了） 



