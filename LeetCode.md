

0701

# 数据复杂度-简单排序

![preview](https://pic2.zhimg.com/v2-971c7e2a3e28574abe7c68583b29bff9_r.jpg)

非常糟糕的复杂度有`O(n!)`，`O(2^n)`，`O(n^2)`；比较糟糕的有`O(nlog n)`，可以接受的有`O(n)`，还不错的有`O(n)`，非常好的有`O(logn)`和`O(1)`。



**O(1)  > O(logn) >O(n) > O(n*logn) >O(n^2) > O(2^n)  > O(n!)**



算法的复杂度通常用大O符号表述，定义为T(n) = O(f(n))。称函数T(n)以f(n)为界或者称T(n)受限于f(n)。 如果一个问题的规模是n，解这一问题的某一算法所需要的时间为T(n)。T(n)称为这一算法的“时间复杂度”。当输入量n逐渐加大时，时间复杂度的极限情形称为算法的“渐近时间复杂度”。空间复杂度同理。举个例子，令![[公式]](https://www.zhihu.com/equation?tex=f%28n%29+%3D+2n%5E2+%2B+3n+%2B+5)，![[公式]](https://www.zhihu.com/equation?tex=O%28f%28n%29%29+%3D+O%282+n%5E2+%2B+3n+%2B+5%29+%3D+O%28n%5E2%29)。







![image-20220703200040283](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200040283.png)

**O：表示正比例关系**

**f(n): 表示执行的次数**



**假设n趋于无限大**

![image-20220703200329667](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200329667.png)

![image-20220703200440660](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200440660.png)

![image-20220703200451511](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200451511.png)

![image-20220703200533359](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200533359.png)

![image-20220703200549544](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200549544.png)

**横轴：输入问题的量级**

**纵轴：时间量级**





- O(1)—常数阶：最低的时空复杂度，也就是耗时与输入数据大小无关，无论输入数据增大多少倍，耗时/耗空间都不变。哈希算法就是典型的O(1)时间复杂度，无论数据规模多大，都可以在一次计算后找到目标。



```text
a = 1
b = 2
c = a + b
b += a
```

![image-20220703200848904](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200848904.png)



**「一个循环」**算法需要执行的运算次数用输入大小n的函数表示，即 T(n) 。下面这个函数，语句频度T(n) = 2 + 2*n + 1，那么时间复杂度为`O(2*n + 3) = O(n)`，因为时间复杂度只关注最高数量级，且与之系数也没有关系。

```text
def fun(n):
    count1 = 0
    count2 = 0
    for i in range(n):
        count1 += 1
        count2 += 2
    return count
```





- 时间复杂度为O(n)—线性阶，就代表数据量增大几倍，耗时也增大几倍。比如常见的遍历算法。

![image-20220703200900852](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200900852.png)

![image-20220703200909743](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703200909743.png)

![image-20220703201008271](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201008271.png)

《国王赏麦》来直观的解释![[公式]](https://www.zhihu.com/equation?tex=O%28log%28N%29%29)。

> 传说西塔发明了国际象棋而使国王十分高兴，他决定要重赏西塔。西塔说：“我不要你的重赏，陛下，只要你在我的棋盘上赏一些麦子就行了。在棋盘的第1个格子里放1粒，在第2个格子里放2粒，在第3个格子里放4粒，在第4个格子里放8粒，依此类推，以 后每一个格子里放的麦粒数都是前一个格子里放的麦粒数的2倍，直到放满第64个格子就行了”。区区小数，几粒麦子，这有何难，“来人”，国王令人如数付给西塔。计数麦粒的工作开始了，第一格内放1粒，第二格内放2粒，第三格内放4粒。还没有到第二十格，一袋麦子已经空了。一袋又一袋的麦子被扛到国王面前来。但是，麦粒数一格接一格飞快增长着，国王很快就看出，即便拿出全国的粮食，也兑现不了他对西塔的诺言。

从西塔的角度看，每多一个格子他就可以多获得一倍的麦粒，这是个幂运算。而对数计算是幂运算的逆运算，想要直观的理解它，我们可以从西塔对面的视角，也就是国王的角度来看这次赏麦是一个什么样的游戏——我每多提供一倍的麦子，他只多消耗一个格子。***而这其实就是![[公式]](https://www.zhihu.com/equation?tex=O%28log%28N%29%29)的本质：输入规模翻倍，操作次数只增加一\***。所以这真的是一个非常非常低的时间复杂度。比起![[公式]](https://www.zhihu.com/equation?tex=O%28N%29)它更接近![[公式]](https://www.zhihu.com/equation?tex=O%281%29).

如果要设计一个算法，让其具有![[公式]](https://www.zhihu.com/equation?tex=O%28log%28N%29%29)的时间复杂度，从正面思考是困难的。我们不妨想一想有没有什么操作是***每操作一次，需要处理的规模就小一半的。\***对，特别经典的例子就是二分搜索。每次取中位数，在其左或其右继续搜索目标值。其本质就是每搜索一次，就把待搜索的数据量减小了一半。在这之上还有二分搜索树，![[公式]](https://www.zhihu.com/equation?tex=O%28log%28N%29%29)其实就是二分搜索树的高度。

总结：

1. 时间复杂度指的是***随着输入大小的增长，运行时间会以怎样的速度扩张。***
2. ![[公式]](https://www.zhihu.com/equation?tex=O%28log%28N%29%29)指的是 ***该算法随着输入规模翻倍，操作次数只增加一。\***













- 时间复杂度O(nlogn)—线性对数阶，就是n乘以logn，当数据增大256倍时，耗时增大256*8=2048倍。这个复杂度高于线性低于平方。归并排序就是O(nlogn)的时间复杂度。

![image-20220703201026746](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201026746.png)





对于**「多个循环」**，假设循环体的时间复杂度为O(n)，各个循环的循环次数分别是a, b, c...，则这个循环的时间复杂度为 O(n×a×b×c...)。分析的时候应该由里向外分析这些循环。比如下面这个函数，复杂度为`O(n*n*1) = O(n^2)`

```text
def fun(n):
    count = 0
    for i in range(n):
        for j in range(n):
            count += 1
    return count
```





时间复杂度O(n^2)—平方阶, 就代表数据量增大n倍时，耗时增大n的平方倍，这是比线性更高的时间复杂度。比如冒泡排序，就是典型的O(n x n)的算法，对n个数排序，需要扫描n x n次

![image-20220703201035637](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201035637.png)











对于**「顺序执行的语句」**或者算法，总的时间复杂度等于其中最大的时间复杂度。比如下面这个函数，第1部分复杂度为O(n^2)，第2部分复杂度为O(n)，总复杂度为`max(O(n^2), O(n)) = O(n^2)`

```text
def fun(n):
    # 第1部分复杂度为O(n^2)
    count = 0
    for i in range(n):
        for j in range(n):
            count += 1
    # 第2部分复杂度为O(n)
    for i in range(n):
        count += 2
    return count
```



对于**「条件判断语句」**，总的时间复杂度等于其中 时间复杂度最大的路径 的时间复杂度。当n >= 0分支的复杂度最大，即总复杂度为`O(n^2)`





![image-20220703201103058](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201103058.png)



![image-20220703201136054](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201136054.png)

![image-20220703201152371](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201152371.png)

![image-20220703201159915](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201159915.png)



![image-20220703201222636](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201222636.png)

![image-20220703201351025](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703201351025.png)



空间复杂度也是关于数据量n的函数，比较常见的空间复杂度有O(1)、O(n)、O(n^2)，更复杂度对数阶、常数对数阶平时是用不到的。



### **如何计算空间复杂度**

我们在写代码时，完全可以用空间来换取时间，比如字典树，哈希等都是这个原理。算法在运行过程中临时占用的存储空间随算法的不同而异，有的算法只需要占用少量的临时工作单元，而且**「不随问题规模的大小而改变」**，我们称这种算法是“就地"进行的，是节省存储的算法，空间复杂度为O(1)，注意这并不是说仅仅定义一个临时变量；有的算法需要占用的临时工作单元数与解决问题的规模n有关，它随着n的增大而增大，当n较大时，将占用较多的存储单元，例如将快速排序和归并排序算法就属于这种情况。

如果算法执行所需要的临时空间不随着某个变量n的大小而变化，即此算法空间复杂度为一个常量，可表示为 O(1)。如下代码中的 i、j、t 所分配的空间都不随着处理数据量变化，因此它的空间复杂度为O(1)。

```python3
i = 0
j = 1
t = i + j
```

这段代码中，第一行定义了一个列表，这个列表的长度随着n的规模不同，会不一样，这里空间复杂度为O(n)。

```text
def fun(n):
    temp = []
    for i in range(n):
        temp.append(i)
```

对于一个算法，其时间复杂度和空间复杂度往往是相互影响的。当追求一个较好的时间复杂度时，可能会使空间复杂度的性能变差，即可能导致占用较多的存储空间；反之，追求一个较好的空间复杂度时，可能会使时间复杂度的性能变差，即可能导致占用较长的运行时间。另外，算法的所有性能之间都存在着或多或少的相互影响。



首先，程序自身所占用的存储空间取决于其包含的代码量，如果要压缩这部分存储空间，就要求我们在实现功能的同时，尽可能编写足够短的代码。

程序运行过程中输入输出的数据，往往由要解决的问题而定，即便所用算法不同，程序输入输出所占用的存储空间也是相近的。

事实上，对算法的空间复杂度影响最大的，往往是程序运行过程中所申请的临时存储空间。不同的算法所编写出的程序，其运行时申请的临时存储空间通常会有较大不同。

如果程序所占用的存储空间和输入值无关，则该程序的空间复杂度就为 O(1)；反之，如果有关，则需要进一步判断它们之间的关系：

- 如果随着输入值 n 的增大，程序申请的临时空间成线性增长，则程序的空间复杂度用 O(n) 表示;
- 如果随着输入值 n 的增大，程序申请的临时空间成 n2 关系增长，则程序的空间复杂度用 O(n2) 表示；
- 如果随着输入值 n 的增大，程序申请的临时空间成 n3 关系增长，则程序的空间复杂度用 O(n3) 表示；
- 等等



**空间复杂度-额外空间复杂度**，一个东西

如果程序运行过程中，不需要额外的[数据结构](https://so.csdn.net/so/search?q=数据结构&spm=1001.2101.3001.7020)，只是使用了额外的几个变量。那么额外空间复杂度为O(1)；
如果要申请一个和原数组大小一样的数组，那额外空间复杂度为O(n)；
如果申请一个是原数组大小一半的数组，那额外空间复杂度为O(n)（因为系数是可以忽略的）







 常数操作、非常数操作

![image-20220701102913171](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701102913171.png)

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701103029241.png" alt="image-20220701103029241" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701103240651.png" alt="image-20220701103240651" style="zoom:50%;" />



![image-20220701104411034](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701104411034.png)











**基本操作执行次数**

关于代码的基本操作执行次数，我们用四个生活中的场景，来做一下比喻：

**场景1：**给小灰一条长10寸的面包，小灰每3天吃掉1寸，那么吃掉整个面包需要几天？

<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy9OdE81c2lhbEpaR3BENmxyb1pYUWVVUzJNVUlXRk5MNVZ5bG9uR1E4alJCdk9uamZpYzBKTlZIeVlab1lZNzZtYmJ3VGlhVVZ2U3lXcEFMcDltZ0p6enpzUS82NDA_d3hfZm10PWpwZWc" alt="640?wx_fmt=jpeg" style="zoom:33%;" />

答案自然是 3 X 10 = 30天。

如果面包的长度是 N 寸呢？

此时吃掉整个面包，需要 3 X n = 3n 天。

如果用一个函数来表达这个相对时间，可以记作 T（n） = 3n。

**场景2：**给小灰一条长16寸的面包，小灰每5天吃掉面包剩余长度的一半，第一次吃掉8寸，第二次吃掉4寸，第三次吃掉2寸......那么小灰把面包吃得只剩下1寸，需要多少天呢？

这个问题翻译一下，就是数字16不断地除以2，除几次以后的结果等于1？这里要涉及到数学当中的对数，以2位底，16的对数，可以简写为log16。

因此，把面包吃得只剩下1寸，需要 5 X log16 = 5 X 4 = 20 天。

如果面包的长度是 N 寸呢？

需要 5 X logn = 5logn天，记作 T（n） = 5logn。

**场景3：**给小灰一条长10寸的面包和一个鸡腿，小灰每2天吃掉一个鸡腿。那么小灰吃掉整个鸡腿需要多少天呢？

<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy9OdE81c2lhbEpaR3BENmxyb1pYUWVVUzJNVUlXRk5MNVYzekhQR09kMTFvaDZhRWp1SHBtWXNoaFdyOVZ4MVZUVVVCdHFvOUtGenlDTU9KckpHZTM2NGcvNjQwP3d4X2ZtdD1qcGVn" alt="640?wx_fmt=jpeg" style="zoom:33%;" />

答案自然是2天。因为只说是吃掉鸡腿，和10寸的面包没有关系 。

如果面包的长度是 N 寸呢？

无论面包有多长，吃掉鸡腿的时间仍然是2天，记作 T（n） = 2。

**场景4：**给小灰一条长10寸的面包，小灰吃掉第一个一寸需要1天时间，吃掉第二个一寸需要2天时间，吃掉第三个一寸需要3天时间.....每多吃一寸，所花的时间也多一天。那么小灰吃掉整个面包需要多少天呢？

答案是从1累加到10的总和，也就是55天。

如果面包的长度是 N 寸呢？

此时吃掉整个面包，需要 1+2+3+......+ n-1 + n = (1+n)*n/2 = 0.5n^2 + 0.5n。

记作 T（n） = 0.5n^2 + 0.5n。

<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy9OdE81c2lhbEpaR29weW1TeFg4MlRZSkFHM3lWaWNGdWE0T1RKUnJwN1ZCb0wxMHlOVDg2NDZTUElSVmJhTmxZdWp2VEI1aWI0U0ZDMlhSS3VoUVlyam56US82NDA_d3hfZm10PWpwZWc" alt="640?wx_fmt=jpeg" style="zoom:33%;" />

上面所讲的是吃东西所花费的相对时间，这一思想同样适用于对程序基本操作执行次数的统计。刚才的四个场景，分别对应了程序中最常见的四种执行方式：





有了基本操作执行次数的函数 T（n），是否就可以分析和比较一段代码的运行时间了呢？还是有一定的困难。

比如算法A的相对时间是T（n）= 100n，算法B的相对时间是T（n）= 5n^2，这两个到底谁的运行时间更长一些？这就要看n的取值了。

所以，这时候有了渐进时间复杂度（asymptotic time complexity）的概念，官方的定义如下：

若存在函数 f（n），使得当n趋近于无穷大时，T（n）/ f（n）的极限值为不等于零的常数，则称 f（n）是T（n）的同数量级函数。

记作 T（n）= O（f（n）），称O（f（n）） 为算法的渐进时间复杂度，简称时间复杂度。

渐进时间复杂度用大写O来表示，所以也被称为大O表示法。



**大O符号（Big O notation）是用于描述函数渐近行为的数学符号。更确切地说，它是用另一个（通常更简单的）函数来描述一个函数数量级的渐近上界**



<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy9OdE81c2lhbEpaR3I1VGJxWU10d2VoTGE1N3o2TG5OTzRzSGZzdUVJdVNLaWM3cnVwTWVoWjh3UlZIRVM1YkVzMG1QV0tZWlBNQ084MThKMTVLSTZub0JBLzY0MD93eF9mbXQ9anBlZw" alt="img" style="zoom:50%;" />





**如何推导出时间复杂度呢？有如下几个原则：**

1. 如果运行时间是常数量级，用常数1表示；
2. 只保留时间函数中的最高阶项；
3. 如果最高阶项存在，则省去最高阶项前面的系数。

**场景1：**

T（n） = 3n 

最高阶项为3n，省去系数3，转化的时间复杂度为：

T（n） = O（n）

**场景2：**

T（n） = 5logn 

最高阶项为5logn，省去系数5，转化的时间复杂度为：

T（n） = O（logn）



**场景3：**

T（n） = 2

只有常数量级，转化的时间复杂度为：

T（n） = O（1）



**场景4：**

T（n） = 0.5n^2 + 0.5n

最高阶项为0.5n^2，省去系数0.5，转化的时间复杂度为：

T（n） = O（n^2）













**亦或运算**

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701111209477.png" alt="image-20220701111209477" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701112821514.png" alt="image-20220701112821514" style="zoom:33%;" />

交换两个数的值 

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701113017752.png" alt="image-20220701113017752" style="zoom:50%;" />

注意上面的用法是有条件的，a/b的内存必须是不同的两个区域，值可以一样，但值所在的内存地址必须不同

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701143433653.png" alt="image-20220701143433653" style="zoom:50%;" />





![image-20220701144456647](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701144456647.png)

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701153748149.png" alt="image-20220701153748149" style="zoom:50%;" />





![image-20220701155021103](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701155021103.png)



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701155241851.png" alt="image-20220701155241851" style="zoom:50%;" />





<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701155823483.png" alt="image-20220701155823483" style="zoom:50%;" />





<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701162508190.png" alt="image-20220701162508190" style="zoom: 67%;" />

### 对数器

![image-20220701162527358](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701162527358.png)

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701162944156.png" alt="image-20220701162944156" style="zoom:50%;" />

## 冒泡排序（Bubble Sort）

- 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
- 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
- 针对所有的元素重复以上的步骤，除了最后一个。
- 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

通过两层循环控制：

- 第一个循环（外循环），负责把需要冒泡的那个数字排除在外；
- 第二个循环（内循环），负责两两比较交换。

![动图](https://pic4.zhimg.com/v2-33a947c71ad62b254cab62e5364d2813_b.webp)

**性能分析**

- 平均时间复杂度：O(N^2)
- 最佳时间复杂度：O(N)
- 最差时间复杂度：O(N^2)
- 空间复杂度：O(1)
- 排序方式：In-place
- 稳定性：稳定

解析说明：

冒泡排序涉及相邻两两数据的比较，故需要嵌套两层 for 循环来控制;

外层循环 n 次，内层最多时循环 n – 1次、最少循环 0 次，平均循环(n-1)/2;

所以循环体内总的比较交换次数为：n*(n-1) / 2 = (n^2-n)/2 ;

按照计算时间复杂度的规则，去掉常数、去掉最高项系数，其复杂度为O(N^2) ;

最优的空间复杂度为开始元素已排序，则空间复杂度为 0;

最差的空间复杂度为开始元素为逆排序，则空间复杂度为 O(N);

平均的空间复杂度为O(1) .





























## 插入排序

插入排序的代码实现虽然没有冒泡排序和选择排序那么简单粗暴，但它的原理应该是最容易理解的了，因为只要打过扑克牌的人都应该能够秒懂。插入排序是一种最简单直观的排序算法，它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

插入排序和冒泡排序一样，也有一种优化算法，叫做拆半插入。



 **性能分析**

> 平均时间复杂度：O(N^2)
> 最差时间复杂度：O(N^2)
> 空间复杂度：O(1)
> 排序方式：In-place
> 稳定性：稳定

如果插入排序的目标是把n个元素的序列升序排列，那么采用插入排序存在最好情况和最坏情况：

> (1) 最好情况：序列已经是升序排列，在这种情况下，需要进行的比较操作需(n-1)次即可。
> (2) 最坏情况：序列是降序排列，那么此时需要进行的比较共有n(n-1)/2次。

插入排序的赋值操作是比较操作的次数减去(n-1)次。平均来说插入排序算法复杂度为O(N^2)。

最优的空间复杂度为开始元素已排序，则空间复杂度为 0；

最差的空间复杂度为开始元素为逆排序，则空间复杂度最坏时为 O(N);

平均的空间复杂度为O(1)







将第一待排序序列第一个元素看做一个有序序列，把第二个元素到最后一个元素当成是未排序序列。

从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列的适当位置。（如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。）

![img](https://www.runoob.com/wp-content/uploads/2019/03/insertionSort.gif)

时间复杂度：O(n^2)

数组初始化：[ 8，2，5，9，7 ]，我们把数组中的数据分成两个区域，已排序区域和未排序区域，初始化的时候所有的数据都处在未排序区域中，已排序区域是空。

![img](https://img-blog.csdnimg.cn/20190624180826322.png)

第一轮，从未排序区域中随机拿出一个数字，既然是随机，那么我们就获取第一个，然后插入到已排序区域中，已排序区域是空，那么就不做比较，默认自身已经是有序的了。（当然了，第一轮在代码中是可以省略的，从下标为1的元素开始即可）

![img](https://img-blog.csdnimg.cn/20190624180833656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pfZmVuZzEyNDg5,size_16,color_FFFFFF,t_70)

第二轮，继续从未排序区域中拿出一个数，插入到已排序区域中，这个时候要遍历已排序区域中的数字挨个做比较，比大比小取决于你是想升序排还是想倒序排，这里排升序：

![img](https://img-blog.csdnimg.cn/2019062418084159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pfZmVuZzEyNDg5,size_16,color_FFFFFF,t_70)

第三轮，排 5 ：

![img](https://img-blog.csdnimg.cn/20190624180915879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pfZmVuZzEyNDg5,size_16,color_FFFFFF,t_70)

第四轮，排 9 ：

![img](https://img-blog.csdnimg.cn/20190624180931541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pfZmVuZzEyNDg5,size_16,color_FFFFFF,t_70)

第五轮，排 7

![img](https://img-blog.csdnimg.cn/20190624180939411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pfZmVuZzEyNDg5,size_16,color_FFFFFF,t_70)

排序结束。






















## 选择排序

① 第一轮从下标为 1 到下标为 n-1 的元素中选取最小值，若小于第一个数，则交换
② 第二轮从下标为 2 到下标为 n-1 的元素中选取最小值，若小于第二个数，则交换
③ 依次类推下去……

![动图](https://pic1.zhimg.com/v2-1c7e20f306ddc02eb4e3a50fa7817ff4_b.webp)





![img](https://pic4.zhimg.com/v2-23667fac97d8ee2d6d256b8302c38eaf_r.jpg)

**复杂度分析**

> 平均时间复杂度：O(N^2)
> 最佳时间复杂度：O(N^2)
> 最差时间复杂度：O(N^2)
> 空间复杂度：O(1)
> 排序方式：In-place
> 稳定性：不稳定











## N(logN)排序







## 递归

递归，在计算机科学中是指**一种通过重复将问题分解为同类的子问题而解决问题的方法**。简单来说，递归表现为函数调用函数本身。

递归最恰当的比喻，就是查词典。我们使用的词典，本身就是递归，为了解释一个词，需要使用更多的词。当你查一个词，发现这个词的解释中某个词仍然不懂，于是你开始查这第二个词，可惜，第二个词里仍然有不懂的词，于是查第三个词，这样查下去，直到有一个词的解释是你完全能看懂的，那么递归走到了尽头，然后你开始后退，逐个明白之前查过的每一个词，最终，你明白了最开始那个词的意思。

**分而治之**





**递归的特点**

实际上，递归有两个显著的特征,终止条件和自身调用:

- 自身调用：原问题可以分解为子问题，子问题和原问题的求解方法是一致的，即都是调用自身的同一个函数。
- 终止条件：递归必须有一个终止的条件，即不能无限循环地调用本身。

​	![preview](https://pic1.zhimg.com/v2-2f23016591a7cd0467683c60c42fb574_r.jpg)

### 递归与栈的关系

其实，递归的过程，可以理解为出入栈的过程的，这个比喻呢，只是为了方便更好理解递归哈。以上代码例子计算sum（n=3）的出入栈图如下：

<img src="https://pic4.zhimg.com/v2-ccd34a35fc9a198b4d87faace911bdcb_r.jpg" alt="preview" style="zoom: 67%;" />

函数sum（n=5）的递归执行过程，如下：

<img src="https://pic2.zhimg.com/v2-bd62067622b9fd7d9540924197465c7d_r.jpg" alt="img" style="zoom:67%;" />



- 计算sum（5）时，先sum（5）入栈，然后原问题sum（5）拆分为子问题sum（4），再入栈，直到终止条件sum（n=1）=1，就开始出栈。
- sum（1）出栈后，sum（2）开始出栈，接着sum（3）。
- 最后呢,sum（1）就是后进先出，sum（5）是先进后出，因此递归过程可以理解为栈出入过程啦~



**即递归的应用场景一般有哪些呢？**

- 阶乘问题
- 二叉树深度
- 汉诺塔问题
- 斐波那契数列
- 快速排序、归并排序（分治算法也使用递归实现）
- 遍历文件，解析xml文件



解决递归问题一般就**三步曲**，分别是：

- 第一步，定义函数功能
- 第二步，寻找递归终止条件
- 第二步，递推函数的等价关系式



**递归存在的问题**

- 递归调用层级太多，导致栈溢出问题
- 递归重复计算，导致效率低下

**栈溢出问题**

- 每一次函数调用在内存栈中分配空间，而每个进程的栈容量是有限的。
- 当递归调用的层级太多时，就会超出栈的容量，从而导致调用栈溢出。









## 递归的经典应用场景





## 递归排序



要点：采用分治法的策略，将已有的有序子序列合并为完全有序的序列，首先要让子序列有序，然后再使子序列间有序，最后等到完全有序的序列。

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701163628984.png" alt="image-20220701163628984" style="zoom:50%;" />

## master公式

子问题等规模 T(N/b)

master公式----->求解时间复杂度

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701175442345.png" alt="image-20220701175442345" style="zoom:50%;" />



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701173840557.png" alt="image-20220701173840557" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701175359083.png" alt="image-20220701175359083" style="zoom:50%;" />

<img src="https://images2017.cnblogs.com/blog/1254488/201710/1254488-20171014092551668-1322456004.png" alt="img" style="zoom:50%;" />











## 归并排序 merge-sort

复杂度分析

> 平均时间复杂度：O(nlogn)
> 最佳时间复杂度：O(n)
> 最差时间复杂度：O(nlogn)
> 空间复杂度：O(n)
> 排序方式：In-place
> 稳定性：稳定

不管元素在什么情况下都要做这些步骤，所以花销的时间是不变的，所以该算法的最优时间复杂度和最差时间复杂度及平均时间复杂度都是一样的为：O( nlogn )

归并的空间复杂度就是那个临时的数组和递归时压入栈的数据占用的空间：n + logn；所以空间复杂度为: O(n)。



## <img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701200934268.png" alt="image-20220701200934268" style="zoom:67%;" />

### 1. 基本思想

归并排序是用分治思想，分治模式在每一层递归上有三个步骤：

- **分解（Divide）**：将n个元素分成个含n/2个元素的子序列。
- **解决（Conquer）**：用合并排序法对两个子序列递归的排序。
- **合并（Combine）**：合并两个已排序的子序列已得到排序结果。

![动图](https://pic4.zhimg.com/v2-a29c0dd0186d1f8cef3c5ebdedf3e5a3_b.webp)

![image-20220701203424103](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701203424103.png)



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701203208280.png" alt="image-20220701203208280" style="zoom:67%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701203618866.png" alt="image-20220701203618866" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701203949435.png" alt="image-20220701203949435" style="zoom:50%;" />



### 小和问题

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701210441314.png" alt="image-20220701210441314" style="zoom:50%;" />

**小和问题**
在一个数组中，每一个数左边比当前数小的数累加起来，叫做这个数组的小和。求一个数组 的小和。
例子： [1,3,4,2,5] 1左边比1小的数，没有； 3左边比3小的数，1； 4左边比4小的数，1、3； 2左边比2小的数，1； 5左边比5小的数，1、3、4、2； 所以小和为1+1+3+1+1+3+4+2=16
逆序对问题 在一个数组中，左边的数如果比右边的数大，则折两个数构成一个逆序对







  <img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701211343668.png" alt="image-20220701211343668" style="zoom:50%;" />



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701213823661.png" alt="image-20220701213823661" style="zoom:50%;" />



   

 <img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701213914867.png" alt="image-20220701213914867" style="zoom:50%;" />

### 逆序对











## 荷兰国旗问题

![image-20220701221649770](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701221649770.png)





<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701223313462.png" alt="image-20220701223313462" style="zoom:67%;" />









## 快速排序

快速排序由于排序效率在同为O(N*logN)的几种排序方法中效率较高，因此经常被采用，再加上快速排序思想----分治法也确实实用，因此很多软件公司的笔试面试，包括像腾讯，微软等知名IT公司都喜欢考这个

![image-20220701225230915](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220701225230915.png)





快速排序使用分治法（Divide and conquer）策略来把一个序列（list）分为两个子序列（sub-lists）。

> ① 从数列中挑出一个元素，称为 “基准”（pivot），
> ② 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
> ③ 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

递归到最底部时，数列的大小是零或一，也就是已经排序好了。这个算法一定会结束，因为在每次的迭代（iteration）中，它至少会把一个元素摆到它最后的位置去。



![动图](https://pic2.zhimg.com/v2-d4e5d0a778dba725091d8317e6bac939_b.webp)





平均时间复杂度：O(NlogN)
最佳时间复杂度：O(NlogN)
最差时间复杂度：O(N^2)



**但是快速排序在最坏情况下的[时间复杂度](http://data.biancheng.net/view/2.html)和冒泡排序一样，是 `O(n2)`**

![img](https://img-blog.csdnimg.cn/20190731222607972.png)

把第一位57作为基准位，用变量把它存起来，因为一会就没了

把所有比57小的数放在57的左面，把比57大的数放在57的右面
两边同时进行，左边找大的，右边找小的，把小的放左边，大的放右边，具体操作如下：
第一趟：从指针j开始，24小于57，放到左边，把57覆盖掉

![img](https://img-blog.csdnimg.cn/20190731223510101.png)

之后：指针i右移，指向68，68>57，放到右边

![img](https://img-blog.csdnimg.cn/20190731223702547.png)	

之后：指针j左移指向33，33<57，放到左边

![img](https://img-blog.csdnimg.cn/20190731223807807.png)

之后：指针i右移指向59，59>57，放右边

![img](https://img-blog.csdnimg.cn/20190731224131598.png)

之后：指针j左移指向96，96>57，j再左移指向28，28<57，放左边

![img](https://img-blog.csdnimg.cn/20190731224653660.png)

之后：指针i右移指向52，52<57,i继续右移指向72，72>57,放右边

![img](https://img-blog.csdnimg.cn/20190731224932626.png)

之后：指针j左移，与指针i重合指向NULL，这时放入57

![img](https://img-blog.csdnimg.cn/20190731225216633.png)

这时发现左边的数都比57小，右边都比57大

然后再对57左边的数，即：0到i-1进行快速排序（同样操作，把24作为基准，左边小，右边大），对57右边的数，即：i+1到n进行快速排序（以72位基准，左边小，右边大）直到不能再进行排序为止






























![image-20220702095406569](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702095406569.png)



![image-20220702095441538](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702095441538.png)



**0702**



# 堆

堆一般指的是二叉堆，顾名思义，二叉堆是完全二叉树或者近似完全二叉树

![img](https://pic4.zhimg.com/80/v2-54a318a1acd0cccb8ac0018ccf58815f_1440w.jpg)

![image-20220702101237204](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702101237204.png)

完全二叉树：节点从左到右布置

非完成二叉树：节点跳过左节点，放在右节点，形成了左节点空缺

![image-20220702105540346](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702105540346.png)



**heap-insert**   向上

![image-20220702102411853](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702102411853.png)

 ![image-20220702102829322](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702102829322.png)

 

**heapify**  堆化   向下

![image-20220702103355257](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702103355257.png)

 



## 堆排序 heap sort

堆排序运行时间为 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5Clg+n%29) ，快于插入排序

堆数据结构是一种数组对象，可以被视为一棵完全二叉树，树中每个节点与数组中存放该节点值的元素对应。树的每一层是满的，最后一层除外（最后一层从一个节点的左子树开始填）。

![img](https://pic1.zhimg.com/v2-c5e4696f0b83dff5b8cae70505769224_r.jpg)

堆可以用数组来进行实现。树的根为 ![[公式]](https://www.zhihu.com/equation?tex=A%5B1%5D) ，给定了某个节点 ![[公式]](https://www.zhihu.com/equation?tex=i) ，其父节点 ![[公式]](https://www.zhihu.com/equation?tex=parent%28i%29) ,左子节点 ![[公式]](https://www.zhihu.com/equation?tex=left%28i%29) 和右子节点 ![[公式]](https://www.zhihu.com/equation?tex=right%28i%29) 在数组中的下标可以简单计算如下：

![[公式]](https://www.zhihu.com/equation?tex=parent%28i%29%3D%5Cleft%5B+i%2F2+%5Cright%5D)

![[公式]](https://www.zhihu.com/equation?tex=left%28i%29%3D2i)

![[公式]](https://www.zhihu.com/equation?tex=right%28i%29%3D2i%2B1)



二叉堆有两种，最大堆和最小堆。最大堆是指父节点比子节点大，最大元素存放在根节点，并且在以一个节点为根的子树中，各节点的值都不大于该子树根节点的值。

![[公式]](https://www.zhihu.com/equation?tex=A%5Bparent%28i%29%5D%5Cgeq+A%5Bi%5D)

最小堆恰好相反， ![[公式]](https://www.zhihu.com/equation?tex=A%5Bparent%28i%29%5D%5Cleq+A%5Bi%5D)

算法步骤如下：

1. 首先对数组进行建堆，这样得到最大堆
2. 取堆的根节点，也就是最大值
3. 保持树的结构不变，将根节点与最后一个值交换，然后对根节点进行MAX-HEAPIFY，这样第二大的值就成为根节点，因此类推

![img](https://pic2.zhimg.com/v2-269869f0cd7d3e5c267ed1e2e52ded9d_r.jpg)

这里需要注意两点：

1. 实现的时候才用了额外的list来存放，也可以采用额外的变量heap-size来进行处理，每次只处理 ![[公式]](https://www.zhihu.com/equation?tex=A%5B1%2Cheapsize%5D) 范围的数据，这样就是完全的in-place了
2. 循环过程中，每次都将 ![[公式]](https://www.zhihu.com/equation?tex=A%5Bheapsize%5D%2CA%5B1%5D) 进行交换，这样就不会修改树的结构，然后直接进行MAX-HEAPIFY





![image-20220702110143025](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702110143025.png)













![image-20220702111445420](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702111445420.png)

**小根堆**

![image-20220702112039862](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702112039862.png)



![image-20220702113433940](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702113433940.png)





## 比较器

![image-20220702113506811](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702113506811.png)

![image-20220702113752846](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702113752846.png)

比较器在C++里面就是重载运算符





## 桶排序 Bucket sort

桶排序（Bucket sort）或所谓的箱排序，是一个排序算法，工作的原理是将数组分到有限数量的桶里。每个桶再个别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排序），最后依次把各个桶中的记录列出来记得到有序序列。桶排序是鸽巢排序的一种归纳结果。当要被排序的数组内的数值是均匀分配的时候，桶排序使用线性时间（Θ(n)）。但桶排序并不是比较排序，他不受到O(n log n)下限的影响。

**基本思想**

桶排序假设待排序的一组数均匀独立的分布在一个范围中，并将这一范围划分成几个子范围（桶）。

然后基于**某种映射函数f** ，将待排序列的关键字 k 映射到第i个桶中 (即桶数组B 的下标i) ，那么该关键字k 就作为 B[i]中的元素 (每个桶B[i]都是一组大小为N/M 的序列 )。

接着将各个桶中的数据有序的合并起来 : 对每个桶B[i] 中的所有元素进行比较排序 (可以使用快排)。然后依次枚举输出 B[0]….B[M] 中的全部内容即是一个有序序列。

> 补充： 映射函数一般是 f = array[i] / k; k^2 = n; n是所有元素个数

为了使桶排序更加高效，我们需要做到这两点：

> 1、在额外空间充足的情况下，尽量增大桶的数量；
> 2、使用的映射函数能够将输入的 N 个数据均匀的分配到 K 个桶中；

同时，对于桶中元素的排序，选择何种比较排序算法对于性能的影响至关重要。

**2. 实现逻辑**

- 设置一个定量的数组当作空桶子。
- 寻访序列，并且把项目一个一个放到对应的桶子去。
- 对每个不是空的桶子进行排序。
- 从不是空的桶子里把项目再放回原来的序列中。

![动图](https://pic3.zhimg.com/v2-b29c1a8ee42595e7992b6d2eb1030f76_b.webp)



 **示意图**

元素分布在桶中：

![img](https://www.runoob.com/wp-content/uploads/2019/03/Bucket_sort_1.svg_.png)

然后，元素在每个桶中排序：

![img](https://www.runoob.com/wp-content/uploads/2019/03/Bucket_sort_2.svg_.png)



**不基于比较的排序**

### 计数排序

计数排序的核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

\1. 计数排序的特征

当输入的元素是 n 个 0 到 k 之间的整数时，它的运行时间是 Θ(n + k)。计数排序不是比较排序，排序的速度快于任何比较排序算法。

由于用来计数的数组C的长度取决于待排序数组中数据的范围（等于待排序数组的最大值与最小值的差加上1），这使得计数排序对于数据范围很大的数组，需要大量时间和内存。例如：计数排序是用来排序0到100之间的数字的最好的算法，但是它不适合按字母顺序排序人名。但是，计数排序可以用在基数排序中的算法来排序数据范围很大的数组。

通俗地理解，例如有 10 个年龄不同的人，统计出有 8 个人的年龄比 A 小，那 A 的年龄就排在第 9 位,用这个方法可以得到其他每个人的位置,**也就排好了序**。当然，年龄有重复时需要特殊处理（保证稳定性），这就是为什么最后要反向填充目标数组，以及将每个数字的统计减去 1 的原因。

 算法的步骤如下：

- （1）找出待排序的数组中最大和最小的元素
- （2）统计数组中每个值为i的元素出现的次数，存入数组C的第i项
- （3）对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）
- （4）反向填充目标数组：将每个元素i放在新数组的第C(i)项，每放一个元素就将C(i)减去1

![img](https://www.runoob.com/wp-content/uploads/2019/03/countingSort.gif)



基数（个-十-百-千-万）base位，每个位数作为一个基

![image-20220702143433563](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702143433563.png)

### **基数排序（Radix Sort）**



基数排序是一种非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。由于整数也可以表达字符串（比如名字或日期）和特定格式的浮点数，所以基数排序也不是只能使用于整数。

1、 基本思想

原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。基数排序的方式可以采用LSD（Least significant digital）或MSD（Most significant digital），LSD的排序方式由键值的最右边开始，而MSD则相反，由键值的最左边开始。

- **MSD**：先从高位开始进行排序，在每个关键字上，可采用计数排序
- **LSD**：先从低位开始进行排序，在每个关键字上，可采用桶排序

2. 实现逻辑

> ① 将所有待比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零。
> ② 从最低位开始，依次进行一次排序。
> ③ 这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。







![image-20220702144511626](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702144511626.png)

![image-20220702144536325](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702144536325.png)

![image-20220702144753482](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702144753482.png)

 

![img](https://pic4.zhimg.com/v2-5ae4857fa248035ecec780583c5e3303_r.jpg)



在上图中，首先将所有待比较数字统一为统一位数长度，接着从最低位开始，依次进行排序。

- 按照个位数进行排序。
- 按照十位数进行排序。
- 按照百位数进行排序。



![img](https://www.runoob.com/wp-content/uploads/2019/03/radixSort.gif)



![img](https://img-blog.csdn.net/20170517212304744?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG91YmxlX2hhcHBpbmVzcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)









![image-20220702180325774](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220702180325774.png)







而一般的递归算法就要有O(n)的空间复杂度了，因为每次递归都要存储返回信息



![img](https://pic2.zhimg.com/v2-ae19249c2f3624cc6869430f0221b831_r.jpg)



O(1)常数阶 < O(logn)对数阶 < O(n)线性阶 < O(n2)平方阶 < O(n3)(立方阶) < O(2n) (指数阶)















# 稳定性

排序后数据之间的相对位置 保持不变

假定在待排序的记录序列中，存在多个具有相同的关键字的记录，若经过排序，这些记录的相对次序保持不变，即在原序列中，r[i]=r[j]，且r[i]在r[j]之前，而在排序后的序列中，r[i]仍在r[j]之前，则称这种排序算法是稳定的；否则称为不稳定的。



假定在待排序的记录序列中，存在多个具有相同的关键字的记录，若经过排序，这些记录的相对次序保持不变，

即在原序列中，A1=A2，且A1在A2之前，

而在排序后的序列中，A1仍在A2之前，

则称这种排序算法是稳定的；否则称为不稳定的。







![image-20220703155716520](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703155716520.png)



![image-20220703160013703](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703160013703.png)



![image-20220703160420641](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703160420641.png)

![image-20220703160428845](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703160428845.png)



![image-20220703160759365](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703160759365.png)

不能，论文中能实现，现实没法子









![image-20220703161225477](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703161225477.png)

![image-20220703161511407](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703161511407.png)



![image-20220703161645641](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703161645641.png)

利用各种排序算法的优势进行组合，综合

一个调度的优势，一个是常数时间优势









# 哈希表-有序表

0703



哈希函数的作用是帮我们把非int的**「键」**或者**「关键字」**转化成int，可以用来做数组的下标。比如我们上面说的将学生的姓名作为**「键」**或者**「关键字」**，这是就需要哈希函数来完成



![image-20220703162011131](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703162011131.png)

![image-20220703162640097](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703162640097.png)



增---删---改---查









![image-20220703164325039](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703164325039.png)

![image-20220703164429887](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703164429887.png)

![image-20220703164541677](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703164541677.png)

![image-20220703164658408](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703164658408.png)



## 链表

![image-20220703165224115](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703165224115.png)



![image-20220703171252254](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703171252254.png)



![image-20220703173251210](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703173251210.png)

![image-20220703173426230](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703173426230.png)

1、用栈的方法

![image-20220703174623770](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703174623770.png)

2、

![image-20220703174717018](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703174717018.png)

![image-20220703174752613](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703174752613.png)





![image-20220703173802472](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703173802472.png)

**快慢指针**









![image-20220703174558115](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703174558115.png)

![image-20220703175237098](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703175237098.png)



![image-20220703175405499](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703175405499.png)

![image-20220703175446166](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703175446166.png)



![image-20220703175517862](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703175517862.png)



![image-20220703194229981](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703194229981.png)



用哈希表做

![image-20220703194918875](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703194918875.png)



 不用哈希表

![image-20220703195213292](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703195213292.png)

![image-20220703195250711](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703195250711.png)

![image-20220703195315318](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703195315318.png)

![image-20220703195355579](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220703195355579.png)



![image-20220704171835432](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704171835432.png)

有环无环  



**判断一个链表有环无环**





![image-20220704220938872](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704220938872.png)





快慢指针  F/S  F:走两步  S:走一步

![image-20220704221148128](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704221148128.png)





快慢指针相遇后，快指针回到开头，慢指针留着原地

快慢指针变成一次走一步，最后就会相遇到环的交点节点处

 ![image-20220704221318032](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704221318032.png)

![image-20220704222348027](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704222348027.png)



判断两个链表是否会相交，找相交节点

![image-20220704222912402](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704222912402.png)

单链表相交后，相交节点以后的后续的节点都是共有的



![image-20220704223101430](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704223101430.png)



判断end1与end2的地址是否一致，一致才有可能相交，不是就这两个链表不可能相交



找相交节点：

​	链表1长度100，链表2长度80，那么先让链表1先走20步，然后链表1-2同时开始走，一定会在相交点相遇（因为两相交的单链表，相交后的节点数以相同的）

长链表先走差值步，然后同时开始走，就会在相交点相遇



![image-20220704223621108](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704223621108.png)



![image-20220704223719923](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704223719923.png)

![image-20220704223859294](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704223859294.png)

![image-20220704224150163](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704224150163.png)

case1：**不存在一个链表无环，一个有环链表，这两种链表不可能有相交节点。**



**两个有环链表的相交问题求解**

case2：当两个链表都有环

1：链表有环，但各自独立

2：有环，入环节点是共同的

3：有环，但入环节点不是同一个

![image-20220704224512992](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704224512992.png)



loop1/loop2链表的环的入口节点

把loop1、loop2作为遍历的终止节点

![image-20220704224906468](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704224906468.png)

上面的cur1 = n>0 ? head1:head2;

因为当两个链表长度不一样，前面的两个while循环的结果就会是的n的结果不是0 大于0表示是链表1是长度更长的，否则是链表2长度更长

![image-20220704225007554](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704225007554.png)

![image-20220704225113405](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220704225113405.png)







## 二叉树的遍历策略

![image-20220705000010342](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705000010342.png)



（1）递归序

![image-20220705000349024](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705000349024.png)

![image-20220705000948463](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705000948463.png)





（1.1）先序:先打印头节点，再打印左子树所有节点，再打印右子树所有节点 

![image-20220705001420380](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705001420380.png)



（1.2）中序：左-头-右

![image-20220705001615620](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705001615620.png)





（1.3）后续 ：左-右-头

![image-20220705001702894](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705001702894.png)

![image-20220705001729284](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705001729284.png)



利用递归能三次达到节点自己，选择打印自己节点的时机不同，划分出三种遍历顺序方法





（2）非递归遍历方法

任何递归函数可以改为非递归函数

（递归-系统自动来操作栈处理）、（非递归-自己手动操作栈）

![image-20220705113147104](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705113147104.png)

![image-20220705113329254](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705113329254.png)





后序遍历

![image-20220705113906737](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705113906737.png)



中序遍历 

进栈顺序是：左-右-头 （这里说的是进栈顺序）

![image-20220705114220471](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705114220471.png)

![image-20220705114348852](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705114348852.png)



![image-20220705114456720](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705114456720.png)

![image-20220705114806943](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705114806943.png)











整个树是可以被左边界分解的



![image-20220705115114137](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705115114137.png)

![image-20220705115303011](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705115303011.png)



![image-20220705143801262](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705143801262.png)

**宽度遍历用队列**

头左右

![image-20220705144424135](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705144424135.png)

![image-20220705144622344](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705144622344.png)





![image-20220705145809872](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705145809872.png)

![image-20220705145848425](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705145848425.png)



![image-20220705150030569](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705150030569.png)

![image-20220705150118206](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220705150118206.png)



不用哈希表的方法



















































































































































































































































































































































































































































































































































































































































































