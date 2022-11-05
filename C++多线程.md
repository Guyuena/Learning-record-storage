# RALL

Resource Acquisition Is Initialization）,也称为“资源获取就是初始化”，是C++语言的一种管理资源、避免泄漏的惯用法。C++标准保证任何情况下，已构造的对象最终会销毁，即它的析构函数最终会被调用。简单的说，RAII 的做法是使用一个对象，在其构造时获取资源，在对象生命期控制对资源的访问使之始终保持有效，最后在对象析构的时候释放资源。





# 进程-线程状态分析

![image-20221006100848583](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221006100848583.png)













下面是Java的线程例子



![img](https://img-blog.csdn.net/20150309140927553)



1、新建状态（New）：新创建了一个线程对象。

2、就绪状态（Runnable）：线程对象创建后，其他线程调用了该对象的start()方法。该状态的线程位于可运行线程池中，变得可运行，等待获取CPU的使用权。

3、运行状态（Running）：就绪状态的线程获取了CPU，执行程序代码。

4、阻塞状态（Blocked）：阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。阻塞的情况分三种：

（一）、等待阻塞：运行的线程执行wait()方法，JVM会把该线程放入等待池中。(wait会释放持有的锁)

（二）、同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池中。

（三）、其他阻塞：运行的线程执行sleep()或join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。（注意,sleep是不会释放持有的锁）

5、死亡状态（Dead）：线程执行完了或者因异常退出了run()方法，该线程结束生命周期。



**系统调度**

调度的本质就是cpu从运行队列中选择一个线程(控制块)去执行。

如果线程由于一个条件没有满足无法运行时，就会从运行队列中移除加入到一个等待队列中，当条件满足时就会产生一个事件使线程从等待队列中移除加入到运行队列中重新开始调度执行。

![img](https://upload-images.jianshu.io/upload_images/6274961-895259988122ed2c.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

进程控制块（PCB： Process Control Block）





**线程阻塞**

是操作系统层面挂起在内存，释放CPU，上下文切换

线程阻塞： 线程阻塞，指的是当一个线程执行到某一个状态时，这时候它需要获得其他资源才能继续执行（比方说IO资源），但是此时有其他线程占着IO资源不释放，那么这个线程就必须等到其他的线程将IO资源释放之后才能继续执行了，这个便是线程阻塞，此时线程在线程阻塞队列而非就绪队列中。



**就是汽车在路上没有油了(没有CPU资源)，只能在车道上等着(等着系统资源)，但不会放弃自己的位置(不释放内存资源)**



**线程休眠和阻塞的区别**

睡眠   和   挂起   是两种行为，阻塞则是一种状态。



进程一般分五个状态：创建，就绪，运行，阻塞，结束
线程一般分四个状态：就绪，运行，阻塞，死亡

针对进程或线程各个状态的区别，从名字大概就可以看出来了。针对阻塞、休眠，挂起，又怎么考虑区别呢？实际使用时，经常称呼阻塞后进入挂起，因为可以认为挂起就是一个动作，进入阻塞态或休眠态。休眠和挂起并没有写入线程、进程生命周期的状态。

**1、主动，被动角度**
阻塞pend是被动，在访问临界资源（锁等）时，被阻塞了
休眠和挂起，一般是主动（或由父进程发起挂起），休眠在休眠时，就知道了计划休眠时长sleep（10），挂起suspend需要等待resume。
时间片到了，也会挂起线程。

**2、CPU,内存角度**

- 阻塞会释放CPU，一般**不释放内存**。
- 挂起一般会继续占用CPU，一般会释放内存，被转移到外存。
- 休眠一般会释放CPU，低优先级或其它优先级可以得到执行。也有说sleep（）指线程被调用时，占着CPU不工作，形象的说明为“占着CPU”睡觉

**3、线程锁等角度**

- 阻塞不会释放锁
- 挂起会释放锁
- 休眠也不会释放锁

**4、调度角度**
任务调度是操作系统来实现的，任务调度时，直接忽略挂起状态的任务，
但是会顾及处于pend下的任务，当pend下的任务等待的资源就绪后，就可以转为ready了。ready只需要等待CPU时间



1--[挂起](https://so.csdn.net/so/search?q=挂起&spm=1001.2101.3001.7020)（Suspend）  有U无存

挂起是一种**主动行为**（首先是一种行为，而不是一种状态，只有成功挂起后才会进入新的状态），因此恢复也应该要主动完成。

进程挂起的结果是从内存移到外存，所以挂起不占内存。

因为挂起后还要受到CPU的监督（等待着激活），所以挂起不释放CPU。
如果被挂起的线程任务优先级巨高，就永远轮不到其他线程任务运行。

挂起一般用于程序调试中的条件中断，当出现某个条件的情况下挂起，然后进行单步调试。

比如sleep()，不释放锁，占着CPU睡觉。


2--**阻塞（Pend）** 有存无U

而阻塞是一种被动行为（阻塞后进入阻塞态），是在请求IO资源时，发生的等待。 IO资源拿到后，自动加入就绪任务队列，等待分配CPU。

因为拿不到IO资源，所以阻塞时会放弃 CPU的占用。

比如wait()，释放锁，释放CPU，等待；

**wait()：让出CPU资源和锁资源**



3--**休眠 （sleep）** 有锁无U

让出CPU资源，但是不会释放锁资源

虽然让出了CPU，但是不会让出锁，其他线程可以利用CPU时间片了，但如果其他线程要获取sleep(long mills)拥有的锁才能执行，则会因为无法获取锁而不能执行，继续等待。但是那些没有和sleep(long mills)竞争锁的线程，一旦得到CPU时间片即可运行了。





时间片轮转法的操作系统进程的状态和它们之间的转换

![img](https://pica.zhimg.com/9faf605ebd46e68d125f5f5ed76495cc_r.jpg?source=1940ef5c)

挂起和睡眠是主动的，挂起恢复需要主动完成，睡眠恢复则是自动完成的，因为睡眠有一个睡眠时间，睡眠时间到则恢复到就绪态



而阻塞是被动的，是在等待某种事件或者资源的表现，一旦获得所需资源或者事件信息就自动回到就绪态



**比喻：**

首先这些术语都是对于线程来说的。对线程的控制就好比你控制了一个雇工为你干活。你对雇工的控制是通过编程来实现的。

挂起线程的意思就是你对主动对雇工说：“你睡觉去吧，用着你的时候我主动去叫你，然后接着干活”。

使线程睡眠的意思就是你主动对雇工说：“你睡觉去吧，某时某刻过来报到，然后接着干活”。

线程阻塞的意思就是，你突然发现，你的雇工不知道在什么时候没经过你允许，自己睡觉呢，但是你不能怪雇工，肯定你这个雇主没注意，本来你让雇工扫地，结果扫帚被偷了或被邻居家借去了，你又没让雇工继续干别的活，他就只好睡觉了。至于扫帚回来后，雇工会不会知道，会不会继续干活，你不用担心，雇工一旦发现扫帚回来了，他就会自己去干活的。因为雇工受过良好的培训。这个培训机构就是操作系统。





## 同步

![image-20221017110438132](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221017110438132.png)



**因为线程任务需要同步，所以就有了互斥量**



**为什么要线程同步**？

 由于现在操作系统支持多个线程运行，可能多个线程之间会共享同一资源。当多个线程去访问同一资源时，如果不加以干预，可能会引起冲突。例如，多个线程同时访问同一个全局变量，如果都是读取操作，则不会出现问题。如果一个线程负责改变此变量的值，而其他线程负责同时读取变量内容，则不能保证读取到的数据是经过写线程修改后的。为了确保读线程读取到的是经过修改的变量，就必须在向变量写入数据时禁止其他线程对其的任何访问，直至赋值过程结束后再解除对其他线程的访问限制。这种保证线程能了解其他线程任务处理结束后的处理结果而采取的保护措施即为线程同步






## 异步



![image-20221017104716263](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221017104716263.png)









## 阻塞

![image-20221017145012186](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221017145012186.png)



## 互斥

线程互斥：线程互斥是指对于共享的操作系统资源，在各线程访问时具有排它性。当有若干个线程都要使用某一共享资源时，任何时刻最多只允许有限的线程去使用，其它要使用该资源的线程必须等待，直到占用资源者释放该资源。例如：两个线程A和B在运行过程中共享同一变量，但为了保持变量的一致性，如果A占有了该资源则B需要等待A释放才行，如果B占有了该资源需要等待B释放才行。




# 一、并发、进程、线程的基本概念和综述

并发，线程，进程要求必须掌握

### 1.1 并发

两个或者更多的任务（独立的活动）同时发生（进行）：一个程序同时执行多个独立的任务；
以往计算机，单核cpu（中央处理器）：某一个时刻只能执行一个任务，由操作系统调度，每秒钟进行多次所谓的“任务切换”。并发的假象（不是真正的并发），切换（上下文切换）时要保存变量的状态、执行进度等，存在时间开销；

随着硬件发展，出现了多处理器计算机：用于服务器和高性能计算领域。台式机：在一块芯片上有多核（一个CPU内有多个运算核心，对于操作系统来说，每个核心都是作为单独的CPU对待的）：双核，4核，8核，10核（自己的笔记本是4核8线程的）。能够实现真正的并行执行多个任务（硬件并发）
使用并发的原因：主要就是同时可以干多个事，提高性能



不同任务之间不断切换执行，由操作系统负责调度

上下文切换是需要内存开销的

![image-20221003152014437](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221003152014437.png)





资料来源： [(68条消息) C++11并发与多线程笔记（1） 并发基本概念及实现，进程、线程基本概念_胡胡浩特的博客-CSDN博客](https://blog.csdn.net/qq_38231713/article/details/106091041)

1.2 可执行程序

磁盘上的一个文件，windows下，扩展名为.exe；linux下，ls -la，rwx（可读可写可执行）
1.3 进程

运行一个可执行程序，在windows下，可双击；在linux下，./文件名
进程，一个可执行程序运行起来了，就叫创建了一个进程。进程就是运行起来的可执行程序。

1.4 线程
①

a)每个进程（执行起来的可执行程序），都有唯一的一个主线程
b)当执行可执行程序时，产生一个进程后，这个主线程就随着这个进程默默启动起来了
ctrl+F5运行这个程序的时候，实际上是进程的主线程来执行（调用）这个main函数中的代码
线程：用来执行代码的。线程这个东西，可以理解为一条代码的执行通路
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513094504846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)

②

除了主线程之外，可以通过写代码来创建其他线程，其他线程走的是别的道路，甚至去不同的地方
每创建一个新线程，就可以在同一时刻，多干一个不同的事（多走一条不同的代码执行路径）
③

多线程（并发）
线程并不是越多越好，每个线程，都需要一个独立的堆栈空间（大约1M），线程之间的切换要保存很多中间状态，切换也会耗费本该属于程序运行的时间



小结：

1-线程是用来执行代码的；

2-把线程理解为一条代码的执行通路，一个新线程代表一条新道路

3-一个进程自动包含一个主线程，主线程随着进程的启动并运行，可以通过代码创建多个子线程



*1.5 学习心得*

- 开发多线程程序：一个是实力的体现，一个是商用的必须需求
- 线程开发有一定难度
- C++线程会设计很多新概念
- 网络方向：网络通讯、网络服务器，多线程是绝对绕不开的



**二、并发的实现方法**

实现并发的手段：
a）通过多个进程实现并发
b）在单独的进程中，写代码创建除了主线程之外的其他线程来实现并发



进程之间并发，就是同时运行多个程序   （由系统调度来实现，一台设备同时运行不停的软件）

线程间并发，就是一个进程内进行多个线程实现(代码编程实现)



2.1 多进程并发

比如账号服务器一个进程，游戏服务器一个进程。
服务器进程之间存在通信（同一个电脑上：管道，文件，消息队列，共享内存）；（不同电脑上：socket通信技术）
2.2 多线程并发

线程：感觉像是轻量级的进程。每个进程有自己独立的运行路径，**但一个进程中的所有线程共享地址空间（共享内存）**，全局变量、全局内存、全局引用都可以在线程之间传递，所以多线程开销远远小于多进程
多进程并发和多线程并发可以混合使用，但建议优先考虑多线程技术



**多线程会存在数据一致性问题**     临界、互斥









![image-20221003154831542](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221003154831542.png)

三、C++11新标准线程库
以往

- windows：CreateThread(), _beginthread(),_beginthreadexe()创建线程；
- linux：pthread_create()创建线程；不能跨平台
- 临界区，互斥量
- POSIX thread(pthread):跨平台，但要做一番配置，也不方便



**C++11**

从C++11新标准，C++语言本身增加对多线程的支持，意味着可移植性（跨平台），这大大减少开发人员的工作量




# 二、线程启动、结束，创建线程





![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513095221587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)



![image-20221005215958677](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005215958677.png)

join()就是主线程会等待子线程完成工作后进行汇合







一、范例演示线程运行的开始

- 程序运行起来，生成一个进程，该进程所属的主线程开始自动运行；当主线程从main（）函数返回，则整个进程执行完毕
- 主线程从main（）开始执行，那么我们自己创建的线程，也需要从一个函数开始运行（初始函数），一旦这个函数运行完毕，线程也结束运行
- 整个**进程**是否执行完毕的标志是：主线程是否执行完，如果主线程执行完毕了，就代表整个进程执行完毕了，此时如果其他子线程还没有执行完，也会被强行终止【此条有例外，以后会解释】
  



创建一个线程：

1. 包含头文件thread
2. 写初始函数
3. 在main中创建thread

必须要明白：有两个线程在跑，相当于整个程序中有两条线在同时走，即使一条被阻塞，另一条也能运行



*//(1)创建了线程，线程执行起点（入口）是myPrint；*

thread myThread(myPrint);

补充：

**线程类参数是一个可调用对象。**
一组可执行的语句称为可调用对象，c++中的可调用对象可以是**函数、函数指针、lambda表达式、bind创建的对象或者重载了函数调用运算符的类对象**

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221008091230178.png" alt="image-20221008091230178" style="zoom:67%;" />





**二、其他创建线程的方法**
①创建一个类，并编写圆括号重载函数，初始化一个该类的对象，把该对象作为线程入口地址

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221003171926428.png" alt="image-20221003171926428" style="zoom: 67%;" />

![image-20221003201920377](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221003201920377.png)







<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221007212511956.png" alt="image-20221007212511956" style="zoom:67%;" />











# 三、 线程传参详解，detach()大坑，成员函数做线程函数



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513100115719.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)





**首先要明确的是，创建线程的入口函数，或者说线程例化时的功能函数，这个函数必须是一个可调用对象**









**使用detach()存在的问题**







**一、传递临时对象作为线程参数**
*1.1要避免的陷阱1：*



就是说在给线程的入口函数传参时，函数定义的参数最好时对实参的复制

而不是和main()函数中的定义的待传参数变量共用一个地址，

因为在使用detach()函数时，可能造成主函数完成工作了

但是子线程还没完成工作，而且子线程中使用的一些变量是和主线程是一个，

按照主线程完成工作就会对变量内存空间进行释放的原因，

会导致子线程中要使用的变量的被清空了，导致空读取报错。



	#include <iostream>
	#include <thread>
	using namespace std;
	
	void myPrint(const int &i, char* pmybuf)
	{
		//如果线程从主线程detach了
		//i不是mvar真正的引用，实际上值传递，即使主线程运行完毕了，子线程用i仍然是安全的，但仍不推荐传递引用
		//推荐改为const int i
		cout << i << endl;
		//pmybuf还是指向原来的字符串，所以这么写是不安全的， 不能要指针
		cout << pmybuf << endl;
		
		// 安全的方法
		//void myPrint( const int i,const string &pmybuf)
	}
	
	int main()
	{
		int mvar = 1;
		int& mvary = mvar;
		char mybuf[] = "this is a test";
		thread myThread(myPrint, mvar, mybuf);//第一个参数是函数名，后两个参数是函数的参数
		myThread.join();
		//myThread.detach();
	cout << "Hello World!" << endl;


// 上面的程序还有bug



*1.2要避免的陷阱2：*



	void myPrint(const int i, const string& pmybuf)
	{
		cout << i << endl;
		cout << pmybuf << endl;
	}
	
	int main()
	{
		int mvar = 1;
		int& mvary = mvar;
		char mybuf[] = "this is a test";
		//如果detach了，这样仍然是不安全的
		//因为存在主线程运行完了，mybuf被回收了，系统采用mybuf隐式类型转换成string
		// 就是说这种隐式转换可能会是在主线程完成后才进行，就导致子函数中是没有得到该参数的转化结果
		//推荐先创建一个临时对象thread myThread(myPrint, mvar, string(mybuf));就绝对安全了。。。。
		thread myThread(myPrint, mvar, mybuf);
		myThread.join();
		//myThread.detach();
	cout << "Hello World!" << endl;
	}















***1.3总结***

- 如果传递int这种简单类型，推荐使用值传递，不要用引用
- 如果传递类对象，避免使用隐式类型转换，全部都是创建线程这一行就创建出临时对象，然后在函数参数里，函数的形参定义时要用引用来接，否则还会创建出一个对象
- 终极结论：建议不使用detach   (非必要情况不要用，常用join()  )



[(68条消息) C++11并发与多线程笔记（3） 线程传参详解，detach()大坑，成员函数做线程函数_胡胡浩特的博客-CSDN博客](https://blog.csdn.net/qq_38231713/article/details/106091597)

[(68条消息) C++ 并发与多线程学习笔记（三）线程传参隐患 成员函数指针做线程函数_Rache_Bartmoss的博客-CSDN博客](https://blog.csdn.net/qq_39731058/article/details/104355062)





![image-20221004095518839](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221004095518839.png)

![image-20221004095541295](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221004095541295.png)



**二、临时对象作为线程参数继续讲**
*2.1线程id概念*

- id是个数字，每个线程（不管是主线程还是子线程）实际上都对应着一个数字，而且每个线程对应的这个数字都不一样
- 线程id可以用C++标准库里的函数来获取。std::this_thread::get_id()来获取



![image-20221004191214752](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221004191214752.png)





**三、传递类对象、智能指针作为线程参数**
*3.1*



**std::ref函数**

 **C++11 的std::ref函数就是为了解决在线程的创建中等过程的值拷贝问题**，可以在模板传参的时候传入引用。



**想要实现真正引用的作用，那么就需要借助std::ref的作用**

[std::ref函数 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1583874)





**智能指针传递**



	void myPrint(unique_ptr<int> ptn)
	{
		cout << "thread = " << std::this_thread::get_id() << endl;
	}
	
	int main()
	{
		unique_ptr<int> up(new int(10));
		//独占式指针只能通过std::move()才可以传递给另一个指针
		//传递后up就指向空，新的ptn指向原来的内存
		//所以这时就不能用detach了，因为如果主线程先执行完，ptn指向的对象就被释放了
		thread myThread(myPrint, std::move(up));
		myThread.join();
		//myThread.detach();
	return 0;
	}
**std::move()**





**四、用成员函数指针做线程函数**

主要注意用法的不同：
依次为成员函数、类对象、需要传入函数的参数。

例如：

A a(10); 	

thread mytobj(&A::thread_work,    a,   10); // 传入三类参数

![image-20221005105832260](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005105832260.png)

	class Test
	{
	    public:
	        Test() {};
	        ~Test() {};
	    void Thread(const int& num)
	    {
	        cout << "子线程执行了" << endl;
	    }
	};
	
	int main()
	{
		Test test;
		thread obj(&Test::Thread, test, 10);	//std::ref()传递真正的引用
		obj.join();
	    return 0;
	}













# 四、创建多个线程、数据共享问题分析

![img](https://img-blog.csdnimg.cn/20200513100626104.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)





```
void myprint(int inum)
{
    cout << "myprint start" << inum<< endl;
    return;
}
int main()
{
    //创建和等待多个线程
    vector<thread> mythreads;
    for (int i = 0; i < 10; i++)
    {
        mythreads.push_back(thread(myprint,i));
    }
    for (auto iter = mythreads.begin(); iter != mythreads.end(); iter++)
    {
        iter->join();
    }
    return 0;
    cout << "主线程执行结束" << endl;
}
```





**线程间的数据共享分析：**

```
vector<int> g_v = {1,2,3};//共享数据，只读
void myprint(int inum)
{
	// 子线程对全局共享数据只有读操作，所以线程对数据是安全的
    cout << "id为: " << this_thread::get_id()<< "打印的g_v值" << g_v[0] << g_v[1] << g_v[2] << endl;
    return;
}
int main()
{
    //创建和等待多个线程
    vector<thread> mythreads;
    for (int i = 0; i < 10; i++)
    {
        mythreads.push_back(thread(myprint,i));
    }
    for (auto iter = mythreads.begin(); iter != mythreads.end(); iter++)
    {
        iter->join();
    }
    return 0;
}
```



**多线程间对共享数据有读有写**

当线程1在写共享数据，线程2在读共享数据，就有可能造成程序崩溃；

写的时候不能读，读的时候不要写；

所以为了线程数据安全，就要对线程、共享数据进行特殊保护处理；

所以在访问共享数据的时候，会共享数据的访问进行锁 保护起来
**c++解决多线程保护共享数据问题的一个概念 “互斥量”**



范例：

```
class A {
public:
//把收到的消息(玩家命令)入到一个队列的线程
void inMsgRecvQueue()
{
	for (int i = 0; i < 10000; i++) {
    	cout << "inMsgRecvQueue执行，插入一个元素" << i << endl;
   		msgRecvQueue.push_back(i); // 假设这个数字就是收到的命令
	}
}

void outMsgRecvQueue()
{
	for (int i = 0; i < 10000; i++)
	{
		if (!msgRecvQueue.empty()) {
		int command = msgRecvQueue.front(); // 读头部元素
		msgRecvQueue.pop_front(); // 移除头部元素
		// 处理数据
		cout << "接收到命令，处理命令" << command << endl;

		}
	else
	{
		cout << "outMsgRecvQueue执行，但目前消息队列为空" << i << endl;
	}
	}
}

private:
    list < int > msgRecvQueue; //
    容器，专门用于代表玩家给咱们发送过来的命令
};
int main()
{
// 数据共享问题分析
// 只读的数据: 安全稳定，不需要特别说明处理手段，直接读就可以
// 有读有写：2个写线程、8个读线程，如果代码没有特别的处理，那程序肯定崩溃                 
// 最简单的不崩溃处理，读的时候不能写，写的时候不能读，2个线程不能同时写，8个线程不能同时读

// 共享数据的保护案例代码
// 网络游戏服务器: 两个自己创建的线程, 一个线程收集玩家命令，并把命令数据写到一个队列中
// 另一个线程从队列中取出玩家发送过来的命令，解析，然后执行
// list频繁的按顺序插入数据和删除数据效率高
// vector容器随机的插入和删除数据效率高
// 所以队列使用list
A mya;
thread Rthread( & A::outMsgRecvQueue, & mya); // 第二个参数，带引用，就不会被拷贝一分了，这个就不能用detach了
thread Wthread( & A::inMsgRecvQueue, & mya);
Rthread.join();
Wthread.join();
return 0;
}
```





# 五、互斥量概念、用法、死锁演示及解决详解

## 线程数据保护

mutex:  互斥量;  互斥体;  互斥; 互斥锁;   互斥对象

### 互斥量   mutex   

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513101113721.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)

**一、互斥量（mutex）的基本概念**

- 互斥量就是个类对象，可以理解为一把锁，多个线程尝试用lock()成员函数来加锁，只有一个线程能锁定成功，如果没有锁成功，那么流程将卡在lock()这里不断尝试去锁定。
- 互斥量使用要小心，保护数据不多也不少，少了达不到效果，多了影响效率。



**声明一个互斥量实例化对象，是表明在某个线程过程中会发生线程互斥现象或者说为了避免互斥现象的提前准备，利用对互斥量的规避操作避免线程运行时出现与其他线程互斥，避免线程崩溃。**

mutex就是类似于一个标志flag，通过这个标志的辅助操作函数来避免进入死锁、线程崩溃等问题；





**一个互斥量就是一把锁**

锁的粒度

- 锁头锁住的代码的多少称为粒度

> - 锁住的代码少，粒度就比较细，程序的运行效率就高
> - 锁住的代码多，粒度就比较粗，程序的运行效率就低

通俗来理解就是锁lock到unlock之间的代码量





保护共享数据，某个线程用代码把共享数据锁住，其他线程想要操作共享数据就要等待解锁



**互斥量就是一个类对象**



互斥量用法： lock() 、  unlock()

先lock(),操作共享数据，unlock()解锁共享数据 

要成对使用， lock()--unlock(),  一对,一次lock()只能对应一次unlock()

![image-20221005121206786](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005121206786.png)





只要是要在不同的子线程中要对共享数据进行操作，那么每个线程中都进行加锁-解锁操作，避免线程对数据的错误操作。



```

int g_num = 0;  // 为 g_num_mutex 所保护
std::mutex g_num_mutex;
void slow_increment(int id) 
{
    for (int i = 0; i < 3; ++i) {
        g_num_mutex.lock();// 上锁保护数据 g_num
        ++g_num;
        std::cout << id << " => " << g_num << '\n';
        g_num_mutex.unlock(); // 操作完共享数据，释放锁
 
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
}
int main()
{
    std::thread t1(slow_increment, 0);
    std::thread t2(slow_increment, 1);
    t1.join();
    t2.join();
}
```





**注意**  大坑

总结一句话就是，函数可以中途返回退出，但是在退出前必须把mutex锁给释放了；·	!~

```
//情况1
mutex.lock(); // 加锁

if(判断条件){
.....;
mutex.unlock();  
return X; // 在条件判断前进行加锁，而在条件判断内部就完成输出返回，必须要在return前进行解锁，不然崩溃
}

else{
....;
mutex.unlock();
return Y;
}

// 情况2   单级判断，无else
mutex.lock();
if(判断条件){
.....;
mutex.unlock();  
return X; // 在条件判断前进行加锁，而在条件判断内部就完成输出返回，必须要在return前进行解锁，不然崩溃
}
....;
mutex.unlock();
return Y;

```




二、互斥量的用法
包含#include <mutex>头文件
2.1 lock()，unlock()

- 步骤：1.lock()，2.操作共享数据，3.unlock()。
- lock()和unlock()要成对使用



2.2 lock_guard类模板     

- lock_guard<mutex> sbguard(myMutex);取代lock()和unlock()
- lock_guard构造函数执行了mutex::lock();在作用域结束时，调用析构函数，执行mutex::unlock()
  

### std::lock_guard模板  

   **就是说把互斥量mutex交给一个管理者lock_guard来管理，更智能化**   

```cpp
 std::lock_guard<std::mutex> guard(some_mutex);
```

![image-20221005145200495](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005145200495.png)





告诉模板要实现的具体类型，前面告诉要实现的类型后，传入对应的参数

**用来lock_guard()就不要再使用lock-unlock,混用会报错**



好处就是在例化std::lock_guard的对象时，其构造函数就是执行mutex::lock()函数，对数据加锁保护

而这个std::lock_guard的对象完成工作就会执行其析构函数，析构函数中就执行 mutex::unlock()函数解锁



说白了就是利用局部变量在定义时，完成工作退出时，内存的释放过程中，把mutex::lock mutex::unlock嵌入到构造函数与析构函数找中，从而一次定义使用就可以完成上锁和解锁。

局部变量的作用域内有效，出了作用域就释放



lock_guard()虽好但是灵活性没mutex::lock  unlock好



```cpp
{
    std::lock_guard<std::mutex> my_guard(some_mutex);
    return std::find(some_list.begin(), some_list.end(), value_to_find) != some_list.end();
}
//my_guard这个类实例对象是在函数内部，属于局部变量，实例化时执行构造函数，其内部执行mutex::lock()进行加锁；
// 在这个函数的return完成前，lock_guard的析构函数执行，释放内存，析构函数内部就会执行 mutex::unlock()函数进行解锁


```

![image-20221005151159884](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005151159884.png)



### 死锁

**deal  locks**

两个或两个以上的进程（线程）在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程（线程）称为死锁进程（线程）。

3.1 死锁演示
死锁至少有两个互斥量mutex1，mutex2。

- a.线程A执行时，这个线程先锁mutex1，并且锁成功了，然后去锁mutex2的时候，出现了上下文切换。
- b.线程B执行，这个线程先锁mutex2，因为mutex2没有被锁，即mutex2可以被锁成功，然后线程B要去锁mutex1.
- c.此时，死锁产生了，A锁着mutex1，需要锁mutex2，B锁着mutex2，需要锁mutex1，两个线程没办法继续运行下去。。。

3.2 死锁的一般解决方案：

​		只要保证多个互斥量上锁的顺序一样就不会造成死锁。

3.3 std::lock()函数模板

- std::lock(mutex1,mutex2……); 一次锁定多个互斥量（一般这种情况很少），用于处理多个互斥量。
- 如果互斥量中一个没锁住，它就等着，等所有互斥量都锁住，才能继续执行。如果有一个没锁住，就会把已经锁住的释放掉（要么互斥量都锁住，要么都没锁住，防止死锁）

3.4 std::lock_guard的std::adopt_lock参数

adop：采用、采纳

- std::lock_guard < std::mutex >  my_guard(my_mutex,std::adopt_lock);
- 加入adopt_lock后，在调用lock_guard的构造函数时，不再进行lock();
- adopt_guard为结构体对象，起一个标记作用，表示这个互斥量已经lock()，不需要在lock()。
  



死锁是存在两个以上互斥量的问题



例子来描述，如果此时有一个线程A，按照先锁a，再锁b,而在此同时又有另外一个线程B，按照先锁b再锁a的顺序获得锁。如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210608202310416.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZhaXJMaWtlU25vdw==,size_16,color_FFFFFF,t_70)



就如图这种情况下，线程A在等待锁b，可是锁b被锁住了，所以此时不能往下进行，需要等待锁b释放，而线程B先是锁住了锁b,在等待锁a的释放，这样就造成了线程A等线程B,线程B等待线程A,从而出现了死锁。





产生死锁的原因？

1）系统资源不足（内存资源、CPU执行资源）；

2）进程（线程）推进的顺序不恰当；

3）资源分配不当。

如果系统资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁；其次，进程运行推进顺序与速度不同，也可能产生死锁。

死锁的形成场景：

1）忘记释放锁：在申请锁和释放锁之间直接return

2）单线程重复申请锁：一个线程，刚出临界区，又去申请资源。

3）多线程多锁申请：两个线程，两个锁，他们都已经申请了一个锁了，都想申请对方的锁

4）环形锁的申请：多个线程申请锁的顺序形成相互依赖的环形

死锁产生的4个必要条件？
	产生死锁的必要条件：

1. 互斥条件：进程要求对所分配的资源进行排它性控制，即在一段时间内某资源仅为一进程所占用。
2. 请求和保持条件：当进程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件：进程已获得的资源在未使用完之前，不能剥夺，只能在使用完时由自己释放。
4. 环路等待条件：在发生死锁时，必然存在一个进程–资源的环形链。

**解决死锁的基本方法**
既然已经知道了形成死锁的条件，那么我们就从这几个条件入手就行，比如：

- a.一次性分配完所有资源，这样就不会再有请求了：（破坏请求条件）
- b.当进程阻塞时，释放所持有的资源（破坏请保持条件）
- c.资源有序分配法：系统给每类资源赋予一个编号，每一个进程按编号递增的顺序请求资源，释放则相反（破坏环路等待条件），也就是上锁的顺序要一致
- 用std::lock()模板来对多个锁上锁，避免死锁

```
 mutex mt1;
mutex mt2;
void thread1()
{
// cout << "thread1 begin" << endl;
// lock_guard < mutex > guard1(mt1);
// this_thread::sleep_for(chrono::seconds(1));
// lock_guard < mutex > guard2(mt2);
// cout << "hello thread1" << endl;
// 或
    cout << "thread1 begin" << endl;
    mt1.lock();
    this_thread::sleep_for(chrono::seconds(1));
    mt2.lock();
    mt1.unlock();
    mt2.unlock();
    cout << "hello thread1" << endl;

}
void thread2()
{
// cout << "thread2 begin" << endl;
// lock_guard < mutex > guard1(mt2);
// this_thread::sleep_for(chrono::seconds(1));
// lock_guard < mutex > guard2(mt1);
// cout << "hello thread2" << endl;
// 或
    cout << "thread2 begin" << endl;
    mt2.lock();
    this_thread::sleep_for(chrono::seconds(1));
    mt1.lock();
    mt1.unlock();
    mt2.unlock();
    cout << "hello thread2" << endl;
}

int main()
{
    thread t1(thread1);
    thread t2(thread2);
    t1.join();
    t2.join();
    cout << "thread end" << endl;
    return 0;
}
```



### std::lock()函数模板

**更大的“锁”管理员**

负责给它们mutex上锁，但不负责给解锁，每个锁要自己解

- std::lock(mutex1,mutex2……); 一次锁定多个互斥量（一般这种情况很少），用于处理多个互斥量。
- 如果互斥量中一个没锁住，它就等着，等所有互斥量都锁住，才能继续执行。如果有一个没锁住，就会把已经锁住的释放掉（要么互斥量都锁住，要么都没锁住，防止死锁）

[std::lock - C++中文 - API参考文档 (apiref.com)](https://www.apiref.com/cpp-zh/cpp/thread/lock.html)

**要么这几个互斥量都锁住，要么都没锁住，一旦有一个没锁住，就是把已经锁住的都放开**





```
std::lock(mutex1,mutex2);

......;

mutex1.unlock();

mutex.unlock();
```



**虽然std::lock()可以同时对多个互斥量进行上锁，但是还是需要对每个互斥量手动unlock()，很容易造成忘记对某个互斥量unlock；**



**解决上述存在的问题**



利用 std::lock_guard模板的配置参数std::adopt_lock

- 该参数表示这个当前这个互斥量`mutex`已经被lock了，那么就不要再调用`lock()`函数了，只调用析构函数中的`unlock()`就好。



**方法一**：先同时lock掉两个锁，再构建std::lock_guard

构建lock_guard时，Tag 参数为 std::adopt_lock，表明当前线程已经获得了锁，**不需要再锁了，**此后mt对象的解锁操作交由 lock_guard 对象 guard 来管理，在 guard 的生命周期结束之后，mt对象会自动解锁。



```cpp
	std::lock(mtA, mtB);
    std::lock_guard<std::mutex>lock1(mtA, std::adopt_lock);
    std::lock_guard<std::mutex>lock2(mtB, std::adopt_lock);
//上面的操作就是先用std::lock()同时锁住两个互斥量，再把互斥量交给std::lock_guard来负责最后的解锁，避免忘记使用unlock
//不使用std::lock_guard就要对每一个互斥量手动添加unlock()

```





![image-20221005163841135](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005163841135.png)



**std::lock() 谨慎使用**， 还是一个个上锁更为稳妥

有时候程序代码内，并不需要这对某个互斥量在这里就上锁，可能是在后面才上锁，std::lock(.....)集体上锁，会导致其他的一些麻烦



**方法二**：先构建std::unique_lock，再同时lock掉两个锁

构建unique_lock时，Tag 参数为 std::defer_lock，表明不lock锁，在执行功能代码之前再统一lock掉两个unique_lock，在 unique_lock 的生命周期结束之后，mt对象自动解锁。

```cpp
std::mutex mt1;
std::mutex mt2;
void deadLockProcess2(std::mutex& mtA, std::mutex& mtB)
{
    std::unique_lock<std::mutex>lock1(mtA, std::defer_lock);
    std::cout << "get the first mutex" << " in thread " << std::this_thread::get_id() << std::endl;
    std::this_thread::sleep_for(std::chrono::milliseconds(1));

    std::unique_lock<std::mutex>lock2(mtB, std::defer_lock);
    std::cout << "get the second mutex" << " in thread " << std::this_thread::get_id() << std::endl;
    
    std::lock(lock1, lock2);
    
    assert(lock1.owns_lock() == true);
    std::cout << "do something in thread " << std::this_thread::get_id() << std::endl;
}
```











C++11中引入了std::unique_lock与std::lock_guard两种数据结构。通过对lock和unlock进行一次薄的**封装**，实现自动unlock的功能。

### unique_lock（类模板）

**用unique_lock()来管理mutex互斥量**

unique：独一的，也就是管理一个互斥量对象，一个管理员管理一个mutex，一对一辅导；

独占，独占一个互斥量

和unique_ptr独占指针那样，只有一个操作对象

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513101831493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)



unique_lock<mutex> myUniLock(myMutex);

把 myMutex  和  myUniLock   绑定在了一起，也就是  myUniLock  拥有  myMutex  的所有权



![image-20221005165923097](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005165923097.png)



1.unique_lock取代lock_guard

- unique_lock比lock_guard灵活很多（多出来很多用法），效率差一点。（但是更灵活的代价是占用空间相对更大一点且相对更慢一点。）（unique_lock比lock_guard占用空间和速度慢一些，因为其要维护mutex的状态）
- unique_lock内部持有mutex的状态：locked（已锁）,unlocked（未锁）
- unique_lock<mutex> myUniLock(myMutex);

2.unique_lock的第二个参数
	**2.1 std::adopt_lock：**

- ​	表示这个互斥量已经被lock()，即不需要在构造函数中lock这个互斥量了。
- ​	前提：必须提前lock
- ​	lock_guard中也可以用这个参数

![image-20221005171122271](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005171122271.png)

使用  std::adopt_lock这个参数就要提前对互斥量加锁



**2.2 std::try_to_lock：**

- ​	尝试用mutex的lock()去锁定这个mutex，但如果没有锁定成功，会立即返回，不会阻塞在那里；

就是说尝试加锁，不成功也不会停留在此处；

- ​	使用try_to_lock的原因是防止其他的线程锁定mutex太长时间，导致本线程一直阻塞在lock这个地方
- ​	前提：不能提前lock();
- ​	owns_lock()    方法判断是否拿到锁，如拿到返回true

if( xxx.try_to_lock()==true){

}



**2.3 std::defer_lock：**

- ​	如果没有第二个参数就对mutex进行加锁，加上defer_lock是始化了一个没有加锁的mutex
- ​	不给它加锁的目的是以后可以调用unique_lock的一些方法
- ​	前提：不能提前lock



defer，英语单词，主要用作不及物动词、及物动词、名词，作不及物动词时译为“推迟；延期；服从”，作及物动词时译为“使推迟；使延期”



没有加锁的mutex就能灵活调用unique_lock的一些成员函数



![image-20221005172634090](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005172634090.png)



```cpp
std::mutex mt1;
std::mutex mt2;
void deadLockProcess2(std::mutex& mtA, std::mutex& mtB)
{
    std::unique_lock<std::mutex>  lock1(mtA, std::defer_lock);// 初始化了一个没有加锁的mutex对象lock1
	...;

    std::unique_lock<std::mutex>  lock2(mtB, std::defer_lock);// 初始化了一个没有加锁的mutex对象lock2
	...;

    std::lock(lock1, lock2);// 手动加锁  lock是给unique_lock绑定的mutex加锁
    

    assert(lock1.owns_lock() == true);

    std::cout << "do something in thread " << std::this_thread::get_id() << std::endl;
    
    
    //在 unique_lock 的生命周期结束之后，mutex对象(lock1 lock)自动解锁 
}
int main() {
    std::thread t1([&] {deadLockProcess2(mt1, mt2); });
    std::thread t2([&] {deadLockProcess2(mt2, mt1); });
    t1.join();
    t2.join();
}
```





![image-20221005193344616](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005193344616.png)



![image-20221005193423963](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005193423963.png)

```
std::unique_lock<std::mutex>lock1(mtA, std::defer_lock);

lock1.lock();
    # 处理共享数据

lock1.unlock();
    # 处理非共享数据
lock1.lock();
    #     处理共享数据
```

```

std::unique_lock<std::mutex>lock1(mtA, std::defer_lock);

if(lock1.try_lock() ==true){
	#     处理共享数据
}
else{
	# 处理非共享数据
}
```

[并发编程6——unique_lock - 简书 (jianshu.com)](https://www.jianshu.com/p/bca5c22bf28f)

**解释release()成员函数**

```
std::mutex myMutex1;
std::unique_lock<std::mutex> lock1(myMutex1);
// release返回的是原始的mutex指针，代码中的是*ptx，对应的就是mutex对象。
std::mutex *ptx = lock1.release(); 
// release()后 lock1和myMutex1关系解除  管理员lock1不再管理互斥量myMutex1
// 现在就要自己手动对互斥量myMutex1进行解锁

// 需要处理的代码
...;
 ptx->unlock();// 自己进行解锁
 
 void inMsgRecvQueue()
    {
        for (int i = 0; i < 10000; i++)
        {
            cout << "inMsgRecvQueue()执行，插入一个元素" << i << endl;
            std::unique_lock<std::mutex> go_guard1(my_mutex1);
            std::mutex* ptx = go_guard1.release();//关系解除
           
            msgRecvQueue.push_back(i);
            ptx->unlock();// 自己进行解锁
        }
        return;
    }
 
```

**unique_lock的所有权的传递**       **move（）**     权力的交接

- ```cpp
   std::unique_lock<std::mutex> go_guard1(my_mutex1);
  ```

可以说`go_guard1`拥有`my_mutex1`的所有权
`go_guard1`可以把自己对`mutex`的所有权转移给其他的`unique_lock`对象，比如`go_guard2`。

unique_lock对象对于mutex的所有权是属于`可以转移`，但是不能`复制`。转移的话使用`move`

- ```cpp
  std::unique_lock<std::mutex> go_guard2(std::move(go_guard1));
  ```

转移的话也可以使用`函数的临时对象`——

```kotlin
 return rtn_unique_lock()
```

![image-20221005212733038](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221005212733038.png) 

```cpp
class A
{
public:

    // 从函数返回一个局部的unique_lock对象是可以的，返回局部对象会让系统生成一个临时unique_lock对象
    // 并调用unique_lock的移动构造函数
    std::unique_lock<std::mutex> rtn_unique_lock()
    {
        std::unique_lock<std::mutex> tmpguard(my_mutex1);
        return tmpguard; // 临时对象
    }
    void inMsgRecvQueue()
    {
        for (int i = 0; i < 10000; i++)
        {
            cout << "inMsgRecvQueue()执行，插入一个元素" << i << endl;

            // 把tmpguard的所有权转移到了 go_guard1 了	
            std::unique_lock<std::mutex> go_guard1 = rtn_unique_lock();
            
            msgRecvQueue.push_back(i);
        }
        return;
    }
...
}




```



# 六、 单例设计模式共享数据分析、解决，call_once





![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051310305989.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjMxNzEz,size_16,color_FFFFFF,t_70#pic_center)



1.设计模式



- 程序灵活，维护起来可能方便，用设计模式理念写出来的代码很晦涩，但是别人接管、阅读代码都会很痛苦
- 老外应付特别大的项目时，把项目的开发经验、模块划分经验，总结整理成设计模式
- 中国零几年设计模式刚开始火时，总喜欢拿一个设计模式往上套，导致一个小小的项目总要加几个设计模式，本末倒置
- 设计模式有其独特的优点，要活学活用，不要深陷其中，生搬硬套
  



**2.单例设计模式：**Singleton 
整个项目中，有某个或者某些特殊的类，只能创建一个属于该类的对象。只能实例化一个类的对象，有且只有一个。

单例类：只能生成一个对象。

其实就是类class定义的一种方法；



- 全局只有一个实例：static 特性，同时禁止用户自己声明并定义实例（把构造函数设为 private）
- 线程安全
- 禁止赋值和拷贝
- 用户通过接口获取实例：使用 static 类成员函数



```
class Danli{
private:
    Danli()
	{ // 私有化构造函数
	}
// 静态成员变量
	static Danli * m_instance;
public:

    static Danli * GetInstance()
    {
        if (m_instance == NULL)
    {
        m_instance = new Danli();
    }
        return m_instance;
    }

    void test_fun()
    {
    	cout << "单例测试" << endl;
    }
};

// 静态成员变量初始化
Danli * Danli::m_instance = NULL;
int main()
{
    // 创建对象，返回该类对象的指针
    Danli * p_a = Danli::GetInstance();
    if (p_a != NULL)
    {
        cout << "实例化完成对象" << endl;
        cout << "p_a指针= " << p_a << endl;
    }

    Danli * p_b = Danli::GetInstance();
    if (p_b != NULL)
    {
        cout << "实例化完成对象" << endl;
        cout << "p_b指针= " << p_a << endl;
    }
    if (p_a == p_b)
    {
        cout << "实例化对象是同一个" << endl;
        cout << "p_a == p_b 而且指针=" << p_a << endl;
    }
	return 0;
}
```

[(68条消息) C++11并发与多线程笔记（7） 单例设计模式共享数据分析、解决，call_once_胡胡浩特的博客-CSDN博客](https://blog.csdn.net/qq_38231713/article/details/106092538)



**3.单例设计模式共享数据分析、解决**

面临问题：需要在自己创建的线程中来创建单例类的对象，这种线程可能不止一个。我们可能面临GetInstance()这种成员函数需要互斥。
可以在加锁前判断m_instance是否为空，否则每次调用Singleton::getInstance()都要加锁，十分影响效率





**4.std::call_once()：**

函数模板，该函数的第一个参数为标记，第二个参数是一个函数名（如a()）。
功能：能够保证函数a()只被调用一次。具备互斥量的能力，而且比互斥量消耗的资源更少，更高效。
call_once()需要与一个标记结合使用，这个标记为std::once_flag；

其实once_flag是一个结构，call_once()就是通过标记来决定函数是否执行，调用成功后，就把标记设置为一种已调用状态。


**多个线程同时执行时，一个线程会等待另一个线程先执行。**

只要std::once_flag 标志被置位，那么别的线程就不会再执行相同的线程服务函数。

```
std::once_flag g_flag;
class Singleton
{
public:
    static void CreateInstance()//call_once保证其只被调用一次
    {
        instance = new Singleton;
    }
    //两个线程同时执行到这里，其中一个线程要等另外一个线程执行完毕
   static Singleton * getInstance() {
         call_once(g_flag, CreateInstance);
         return instance;
   }
private:
   Singleton() {}
   static Singleton *instance;
};
Singleton * Singleton::instance = NULL;
```



# 七、条件变量condition_variable

wait、notify_one、notify_all

**条件变量**

线程A：等待一个条件满足

线程B：专门往消息队列中放入消息(数据)



**一、条件变量condition_variable、wait、notify_one、notify_all**
std::condition_variable 实际上是一个类，是一个和条件相关的类，说白了就是等待一个条件达成。

 **condition_variable是一个类，搭配互斥量mutex来用**

这个类有它自己的一些函数，这里就主要讲wait函数和notify_\*函数，故名思意，wait就是有一个等待的作用，notify就是有一个通知的作用。

简而言之就是程序运行到wait函数的时候会先在此阻塞，然后自动unlock，那么其他线程在拿到锁以后就会往下运行，当运行到notify_one()函数的时候，就会唤醒wait函数，然后自动lock并继续下运行。



```
std::mutex mymutex1;
std::unique_lock < std::mutex > sbguard1(mymutex1);
std::condition_variable condition;
condition.wait(sbguard1, [this]{ 
if (!msgRecvQueue.empty())
	return true;
return false;
});

condition.wait(sbguard1);
```

wait()用来等一个东西

如果第二个参数的lambda表达式返回值是false，那么wait()将解锁互斥量，并阻塞到本行
如果第二个参数的lambda表达式返回值是true，那么wait()直接返回并继续执行。

阻塞到什么时候为止呢？阻塞到其他某个线程调用notify_one()成员函数为止；

如果没有第二个参数，那么效果跟第二个参数lambda表达式返回false效果一样

wait()将解锁互斥量，并阻塞到本行，阻塞到其他某个线程调用notify_one()成员函数为止。

当其他线程用notify_one()将本线程wait()唤醒后，这个wait恢复后

1、wait()不断尝试获取互斥量锁，如果获取不到那么流程就卡在wait()这里等待获取，如果获取到了，那么wait()就继续执行，获取到了锁

2.1、如果wait有第二个参数就判断这个lambda表达式。

​	a)如果表达式为false，那wait又对互斥量解锁，然后又休眠，等待再次被notify_one()唤醒
​	b)如果lambda表达式为true，则wait返回，流程可以继续执行（此时互斥量已被锁住）。
2.2、如果wait没有第二个参数，则wait返回，流程走下去。

**注**

仍然存在一些问题，当另外的线程执行.notify_one()或者.notify_all()的时候，需要唤醒的wait()并不是正好阻塞在那，可能是后面一点的代码执行时间有些耗时，就可能那边给出了唤醒信号，但没有在那个时刻接到，又被后来的信号给冲了，这种可能性是存在的



```
class A {
public:
    void inMsgRecvQueue()
    {
    for (int i = 0; i < 100000; ++i)
    {
        cout << "inMsgRecvQueue插入一个元素" << i << endl;
   		std::unique_lock < std::mutex > sbguard1(mymutex1);
    	msgRecvQueue.push_back(i);
        // 尝试把wait() 线程唤醒, 执行完这行，
        // 那么outMsgRecvQueue() 里的wait就会被唤醒
        // 只有当另外一个线程正在执行wait()时notify_one()才会起效，否则没有作用
   		 my_condition.notify_one();
    }
    }

    void outMsgRecvQueue()
	{
    	int command = 0;
   	 	while (true) {
    	std::unique_lock < std::mutex > sbguard2(mymutex1);
        // wait() 用来等一个东西
        // 如果第二个参数的lambda表达式返回值是false，那么wait()将解锁互斥量，并阻塞到本行
         // 阻塞到什么时候为止呢？阻塞到其他某个线程调用notify_one()成员函数为止；
        // 当wait()被notify_one()激活时，会先执行它的条件判断表达式是否为true，
        // 如果为true才会继续往下执行
    	my_condition.wait(sbguard2, [this]
        {
            if (!msgRecvQueue.empty())
            return true;
            return false;
         });
            
    command = msgRecvQueue.front();
   	msgRecvQueue.pop_front();
	// 因为unique_lock的灵活性，我们可以随时unlock，以免锁住太长时间
    sbguard2.unlock();
	cout << "outMsgRecvQueue()执行，取出第一个元素" << endl;
	}
	}
private:
    std::list < int > msgRecvQueue;
    std::mutex mymutex1;
    std::condition_variable my_condition;
};
int main()
{
    A myobja;
    std::thread myoutobj( & A::outMsgRecvQueue, & myobja);
    std::thread myinobj( & A::inMsgRecvQueue, & myobja);
    myinobj.join();
    myoutobj.join();
}
```

![image-20221007091811458](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221007091811458.png)







**锁之间是会竞争的**，所以在某个线程中进行了notify_one(),另外一个线程需要这个notify_one()给的唤醒，但是两个线程本身就是竞争关系的话，notif_one()所在的线程可能再次获得了锁，导致wait()所在的线程没法得到该锁，所以wait()所在线程还是没法工作。



上面的代码可能导致出现一种情况：
因为outMsgRecvQueue()与inMsgRecvQueue()并不是一对一执行的，所以当程序循环执行很多次以后，可能在msgRecvQueue 中已经有了很多消息，但是，outMsgRecvQueue还是被唤醒一次只处理一条数据。这时可以考虑把outMsgRecvQueue多执行几次，或者对inMsgRecvQueue进行限流。



![image-20221007093741669](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221007093741669.png)





# 八、async、future、packaged_task、promise

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513104145145.png#pic_center)



**本节内容需要包含头文件#include <future>**

**一、std::async、std::future创建后台任务并返回值**
**std::async**是一个函数模板，用来启动一个异步任务，启动起来一个异步任务之后，它返回一个**std::future**对象，这个对象是个[类模板](https://so.csdn.net/so/search?q=类模板&spm=1001.2101.3001.7020)。



（1）直接用函数作为线程入口函数

std::future<int> result1 = std::async(mythread);



（2）用类成员函数作为线程的入口函数

```
class A{
   ...;
public:
   void mythread(int tmp){
   ...;
   }
   ...;
}；

A a；

std::future<int> result2 = std::async(&A::mythread, &a, tmp);
```







```c++
int mythread() {
	cout << "mythread() start " << "threadid = " << std::this_thread::get_id() << endl;
	std::chrono::milliseconds dura(5000); // dura时间长度
	std::this_thread::sleep_for(dura);
	cout << "mythread() end " << "threadid = " << std::this_thread::get_id() << endl;
	return 5;
}
//  什么叫“启动一个异步任务”？就是自动创建一个线程，并开始 执行对应的线程入口函数，它返回一个std::future对象，
//  这个std::future对象中就含有线程入口函数所返回的结果，我们可以通过调用future对象的成员函数get()来获取结果。

int main() {
	A a;
	int tmp = 12;
	cout << "main" << "threadid = " << std::this_thread::get_id() << endl; // 主线程
	std::future<int> result1 = std::async(mythread); // 自动创建一个线程
	// std::async()就会创建一个线程，而且线程的服务函数是mythread()这个自定义的函数，这个函数
	// 就是异步线程函数
    // 将std::future的类对象 result1和std::async创建的线程对象绑定在一起
// 就是说，当前工作线程想要获得所开辟的异步线程的操作结果，但是异步线程往往不能马上就完成任务操作，
// 操作结果没法立马给到工作线程，但是工作线程就是要，那么future就是提供了工作线程去“访问”这个异步线程结果的一种
//方法，就是工作线程可以获得异步线程的操作结果
cout << "continue........" << endl;
cout << result1.get() << endl; //卡在这里等待mythread()执行完毕，拿到结果
// std::future对象的get()成员函数会等待线程执行结束并返回结果，拿不到结果它就会一直等待
// 不拿到返回值，誓不罢休

//类成员函数
std::future<int> result2 = std::async(&A::mythread, &a, tmp); //第二个参数是对象引用才能保证线程里执行的是同一个对象
cout << result2.get() << endl; 
// 不能多次get()
// 执行到get(),要是std::async的线程还没执行结束，没得到返回值，那就会卡在这里等待返回值
//或者result2.wait();
//wait()成员函数，用于等待线程返回，本身并不返回结果，这个效果和 std::thread 的join()更像
cout << "good luck" << endl;
return 0;
}
```





### std::move()

移动语义

一句话概括std::move ———— std::move是**将对象的状态或者所有权从一个对象转移到另一个对象**，只是转移，没有内存的搬迁或者内存拷贝。

![img](https://img-blog.csdnimg.cn/2021030222151279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3MxMXNob3dfMTYz,size_16,color_FFFFFF,t_70)



2. C++move的特点
std::move函数可以以非常简单的方式将左值引用转换为右值引用。（左值、左值引用、右值、右值引用 参见：http://www.cnblogs.com/SZxiaochun/p/8017475.html）
 通过std::move，可以避免不必要的拷贝操作。
std::move是为性能而生。
std::move是将对象的状态或者所有权从一个对象转移到另一个对象，只是转移，没有内存的搬迁或者内存拷贝。
如string类在赋值或者拷贝构造函数中会声明char数组来存放数据，然后把原string中的 char 数组被析构函数释放，如果a是一个临时变量，则上面的拷贝，析构就是多余的，完全可以把临时变量a中的数据直接 “转移” 到新的变量下面即可



C++11中，标准库在中提供了一个有用的函数std::move，std::move并不能移动任何东西，**它唯一的功能是将一个左值引用强制转化为右值引用，继而可以通过右值引用使用该值，以用于移动语义**。









![image-20221017180542658](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221017180542658.png)























# 线程thread类实例化方法

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221008091903908.png" alt="image-20221008091903908" style="zoom: 50%;" />

**1、通过函数指针创建**

void func(int param1,int param2,int param3);//全局函数

thread t(func,param1,param2,param3);//创建线程



    void counter( int id, int numIter )
    {
    	for( int i = 0; i < numIter; ++i )
    	{
       	 	cout << "counter id:" << id << endl;
        	cout << "iteraion:" << i << endl;
    	}
    }
    
    int main(){
    
        thread t1( counter, 1, 6 );
     
        thread t2( counter, 2, 4 );
     }











存在的问题：







**2、通过函数对象创建**

    class CCount
    {
    public:
        CCount( int id, int numIter ): m_ID( id ), mNum( numIter )
        {
    	}
    void operator()()const
    {
        for( int i = 0; i < mNum; ++i )
        {
            cout << "counter id:" << m_ID << endl;
            cout << "iteraion:" << i << endl;
        }
    }
    private:
        int m_ID;
        int mNum;
    };
    
    //通过函数对象创建线程
    int main()
    {
        //方法1
        thread t1{ CCount{1, 20} };
        t1.join;
    	//方法2
    	CCount c( 2, 12 );
    	thread t2( c );
    	t2.join();
     
    	//方法3
    	thread t3( CCount( 3, 10 ) );
    	t3.join();
     
    return 0;
    }





存在的问题：





**3、通过lamda表达式创建**




​    
​    int main()
​    {
​        std::thread thread1([](){
​        std::cout<<"lambda thread called." <<std::endl;
​        });
​    	thread1.join(); // 等待线程结束
​    return 0;
​    }




存在的问题：



**4、通过成员函数创建**

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221007214903704.png" alt="image-20221007214903704" style="zoom:67%;" />



注意看两者的区别，一个用的是普通传递，一个是用的是引用传递

用普通的传递，那么就是会对类对象的重新复制

而用引用那么不会复制，用的是同一个类对象







存在的问题：





**5、重载了函数调用符的类对象来创建   或   通过类对象创建线程**

```
class  TEST{
public:
    void operator() ()// 重载()运算符
    {
        // 处理代码
    }
};

int main(){
    TEST test; // test是重载了()运算符的类的对象
    thread myThread(test);
    myThread.join();

    return 1;
}
```



存在的问题：

1. 用detach() 主线程结束对象即被销毁，那么子线程的成员函数还能调用吗？

   - ​	这里的的对象会被复制到子线程中，当主线程结束，复制的子线程对象并不会被销毁

   -    只要是没有引用、指针就不会出现问题







