











<img src="https://pic3.zhimg.com/v2-4520ee96aef365f42b32d2cf8175bbfe_r.jpg" alt="preview" style="zoom:33%;" />

整个SwinTransformerBlock包含两个window Attention(关于Window Attention机制后面会详细说明)，其中第一个也就是在每个Window内做Attention操作，但是这样的弊端就是窗口与窗口之间的数据没办法做Attention，这也就无法像ViT一样对全局数据进行建模提取特征，所以第二个Window Attention就是来解决这个问题。















SW-MSA详解
前面有说，采用W-MSA模块时，只会在每个窗口内进行自注意力计算，所以窗口与窗口之间是无法进行信息传递的。为了解决这个问题，作者引入了Shifted Windows Multi-Head Self-Attention（SW-MSA）模块，即进行偏移的W-MSA。如下图所示，左侧使用的是刚刚讲的W-MSA（假设是第L层），那么根据之前介绍的W-MSA和SW-MSA是成对使用的，那么第L+1层使用的就是SW-MSA（右侧图）。根据左右两幅图对比能够发现窗口（Windows）发生了偏移（可以理解成窗口从左上角分别向右侧和下方各偏移了⌊ M 2 ⌋ \left \lfloor \frac {M} {2} \right \rfloor⌊ 
2
M

 ⌋个像素）。看下偏移后的窗口（右侧图），比如对于第一行第2列的2x4的窗口，它能够使第L层的第一排的两个窗口信息进行交流。再比如，第二行第二列的4x4的窗口，他能够使第L层的四个窗口信息进行交流，其他的同理。那么这就解决了不同窗口之间无法进行信息交流的问题。

![img](https://img-blog.csdnimg.cn/e206bb78d43e4630b0d855d4133ff57c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5aSq6Ziz6Iqx55qE5bCP57u_6LGG,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

滑窗处理后，也不是所有窗口之间都要信息交流

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220601214306604.png" alt="image-20220601214306604" style="zoom:50%;" />



原来A-B-C-D 之间计算注意力时是没有信息交流的，划分后，

2中有A，B；

4中有A，C；

5中有A,B,C,D；

6中有B,D；

8中有C,D

这样在计算注意力，就能在各个窗口间进行信息交流









不是说其他4个2x2的小窗口没用，这只是滑窗的产物之一；

为了能更快的计算速度，还就行位置变动，达到和原来4个4x4一样的计算速度

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220601213530589.png" alt="image-20220601213530589" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220601213541835.png" alt="image-20220601213541835" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220601213652833.png" alt="image-20220601213652833" style="zoom:50%;" />

但是，注意到，在划分滑动后，新的4x4窗口内，有些窗口内的部分单元并不是连续的（就是从别的窗口滑动拼接），为了在计算窗口内的注意力大小时能区分开，就引入了mask蒙板这个矩阵数据，让其在计算SW-MSA时，能把不是属于这个窗口的注意力区分出来。

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220601214138975.png" alt="image-20220601214138975" style="zoom: 67%;" />



上面的这段话，解释了window-mask的作用。在计算由区域5和区域3组成的4x4大小矩阵内进行注意力大小计算，但是并不是计算区域内所有token都是来着同一连续内存，只有部分是连续的，为了计算出是来着同一区域的注意力，使用一个mask掩码进行过滤；

当前区域5和区域3都是滑窗注意力，能进行不同窗口间的信息交流，计算的结果就是SW-MSA

mask就单纯为了区分开由“拼装”后的计算区域





![image-20220601215032639](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220601215032639.png)



- 1、把输入进行 窗口处理

- 2、窗口滑动，shift-window

- 3-1计算滑窗内部的注意力大小

- 3-2每个滑窗注意力计算结果和对应窗口的mask蒙板相加，进行过滤

- 4、把计算结果恢复回原来的形状

![image-20220602104559874](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220602104559874.png)



**计算每个区域的内部注意力大小，如果当前的窗口注意力时 SW-MSA，那么对计算的注意力结果加上mask掩码进行“过滤”**



```
attn = attn.view(B_ // nW, nW, self.num_heads, N, N) + mask.unsqueeze(1).unsqueeze(0)  # mask 添加
print("WindowAttention forward attn.view 后 ", attn.shape)

attn = attn.view(-1, self.num_heads, N, N)
attn = self.softmax(attn)
```







**3**

判断输入的shap是否与 window_size 是整数倍关系

```
Hp = int(np.ceil(H / self.window_size)) * self.window_size
Wp = int(np.ceil(W / self.window_size)) * self.window_size
# 拥有和feature map一样的通道排列顺序，方便后续window_partition
img_mask = torch.zeros((1, Hp, Wp, 1), device=x.device)  # [1, Hp, Wp, 1]
```







以一个窗口大小3x3，9个窗口的操作为例







![image-20220508203726835](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508203726835.png)



从一个整好是 3x3 等分的窗口，去实现成滑动窗口，通过将原来的矩阵移动行和列来实现分割，

移动的行数和列数  =  窗口大小window_size/2 向下取整





**4**

![image-20220508203800490](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508203800490.png)



**5**

通过移动数行数列，得到的新的矩阵形式就相当于滑动，在这个新的矩阵上再进行3X3大小的窗口划分，就可以得到可以和邻近窗口有信息交互的新窗口（没有滑动，各个3x3的窗口内的注意力就是局部的，没法和周边交流）

![image-20220508203827105](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508203827105.png)



**6**

```
h_slices = (slice(0, -self.window_size),  # 从高方向，从上往下到倒数第window_size个结束
            slice(-self.window_size, -self.shift_size), # 从高方向，从上往下，从倒数第window_size个开始，到倒数第shift_size个
            slice(-self.shift_size, None)) # 最后一个（最后一行）
# w_slices： 在宽W方向怎么切
w_slices = (slice(0, -self.window_size), # 宽方向，从左往右到倒数第window_size个结束
            slice(-self.window_size, -self.shift_size), # 宽方向，从左往右，从倒数第window_size个开始，到倒数第shift_size个
            slice(-self.shift_size, None)) # 最后一个（最后一列）
```

通过slice()切片函数对矩阵进行切片并赋值标注，达到是滑窗效果

```
# 切片，分成N个部分
for h in h_slices:
    for w in w_slices:
        # 切片，给切片后的各个连续区域赋值，表示当前切片mask归属
        img_mask[:, h, w, :] = cnt
        cnt += 1
print("create_mask 统计窗口数量= ",cnt)
print("切片 img_mask ",img_mask.shape)
```



![image-20220508203842058](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508203842058.png)



**7**

通过  window_partition()把矩阵划分成一个个单独的小窗口

```
mask_windows = window_partition(img_mask, self.window_size)  # [nW, Mh, Mw, 1]
```



![image-20220508204127320](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204127320.png)



**8**

**最难理解的一部分**



将一个个小窗口展平

```
# 将mask_windows展平
mask_windows = mask_windows.view(-1, self.window_size * self.window_size)  # [nW, Mh*Mw]
```



![image-20220508204156037](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204156037.png)

**9**

通过添加维度以及利用矩阵运算的广播机制

```
mask_windows.unsqueeze(1) 


mask_windows.unsqueeze(2) 

attn_mask = mask_windows.unsqueeze(1) - mask_windows.unsqueeze(2)  # [nW, 1, Mh*Mw] - [nW, Mh*Mw, 1]
```

```
#  # [nW, 1, Mh*Mw] - [nW, Mh*Mw, 1]  就会进行广播
# [nW, 1, Mh*Mw] --> [nW, Mh*Mw, Mh*Mw]
# [nW, Mh*Mw, 1] --> [nW, Mh*Mw, Mh*Mw]
```





![image-20220508204244086](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204244086.png)



**10**

相减  左右

通过两个矩阵之间的相减，求出在计算各个窗口的内部注意力大小时，用的是哪个蒙板mask

比如下面的，右边就是计算结果，就是上面滑窗口第九个窗口在计算内部注意力大小时需要用到的蒙板掩码mask，用来区分出当前窗口内，那些区域是连续的，是来自同一个原始窗口内的数据

![image-20220508204325506](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204325506.png)



















**11**

![image-20220508204714126](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204714126.png)

**12**

![image-20220508204651024](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204651024.png)

**13**

![image-20220508204733342](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204733342.png)



**14**

![image-20220508204815920](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204815920.png)



**15**

**列表操作**

![image-20220508200203148](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508200203148.png)







**16**

 **行标操作**

![image-20220508200218034](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508200218034.png)



**17**

**行标列标相加**

![image-20220508200312562](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508200312562.png)























![image-20220508204535985](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508204535985.png)













**25**

![image-20220508205132606](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508205132606.png)











## 相对位置编码

![[公式]](https://www.zhihu.com/equation?tex=Attention%28Q%2CK%2CV%29%3DSoftMax%28QK%5E%7BT%7D%2F%5Csqrt%7Bd%7D%2BB%29V)





绝对位置编码是在进行self-attention**计算之前**为每一个token添加一个可学习的参数，相对位置编码如上式所示，是在进行self-attention**计算时**，在计算过程中添加一个可学习的相对位置参数



假设window_size = 2*2即每个窗口有4个token ![[公式]](https://www.zhihu.com/equation?tex=%28M%3D2%29) ，如图1所示，在计算self-attention时，每个token都要与所有的token计算QK值，如图6所示，当位置1的token计算self-attention时，要计算位置1与位置(1,2,3,4)的QK值，即以位置1的token为中心点，中心点位置坐标(0,0)，其他位置计算与当前位置坐标的偏移量。

![preview](https://pic1.zhimg.com/v2-bcda7ffdbf89cdc5aa90f6ba07e4c044_r.jpg)

​																			图6 相对位置索引求解流程图



图6最后生成的是相对位置索引,relative_position_index.shape = ![[公式]](https://www.zhihu.com/equation?tex=%28M%5E%7B2%7D%2AM%5E%7B2%7D%29) ，在网络中注册成为一个不可学习的变量，relative_position_index的作用就是根据最终的索引值找到对应的可学习的相对位置编码。relative_position_index的数值范围(0~8)，即 ![[公式]](https://www.zhihu.com/equation?tex=%282M-1%29%2A%282M-1%29) ,所以相对位置编码可以由一个3*3的矩阵表示，如图7所示：



<img src="https://pic4.zhimg.com/v2-a9d97c2d2a3e76beff0f83acc5e286e7_r.jpg" alt="preview" style="zoom:50%;" />

​																					图7 相对位置编码



图7中的0-8为索引值，每个索引值都对应了 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 维可学习数据**(根据图1，每个token都要计算 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 个QK值，每个QK值都要加上对应的相对位置编码)**

继续以图6中 ![[公式]](https://www.zhihu.com/equation?tex=M%3D2) 的窗口为例，当计算位置1对应的 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 个QK值时，应用的relative_position_index = [ 4, 5, 7, 8] ![[公式]](https://www.zhihu.com/equation?tex=%28M%5E%7B2%7D%29)个 ，对应的数据就是图7中位置索引4,5,7,8位置对应的 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 维数据，即relative_position.shape = ![[公式]](https://www.zhihu.com/equation?tex=%28M%5E%7B2%7D%2AM%5E%7B2%7D%29)



[Swin Transformer 论文详解及程序解读 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/401661320)







## cyclic shift滑动窗口



shifted window self-attention，为了解决不重叠窗口之间没有关联的问题，采用了shifted window的方法。

<img src="https://pic4.zhimg.com/80/v2-f992b53ee68f849e2eeb80e81968e3d3_1440w.jpg" alt="img" style="zoom: 50%;" />

在每个窗口内部计算注意力大小（A local window to perform self-attention）

论文中以 ![[公式]](https://www.zhihu.com/equation?tex=%28M%2F2%2C+M%2F2%29) 向下取整的窗口重新对原图进行分割，并将之前没有联系的新窗口合并得到新的窗口划分方案，如图8所示，带来的问题就是窗口个数增加了，为了避免窗口增加导致的额外计算量并保证不重叠窗口间有关联，论文提出了cyclic shift方法，如图9所示：

比如M=4



![img](https://pic3.zhimg.com/80/v2-8d6170c524178f10f44259066b6bc9ca_1440w.jpg)

​																						图9 cyclic shift window





通过图9的方式可以**在保证不重叠窗口间有联系的基础上不增加窗口的个数**，新的窗口可能会由之前不相关的自窗口构成，**为了保证shifted window self-attention计算的正确性，只能计算相同子窗口的self-attention，不同子窗口的self-attention结果要归0，否则就违背了shifted window self-attention 的原则。**



在进行cyclic shift之前，需要给子**窗口进行编码**，编码之后通过torch.roll对窗口进行滚动，达到cyclic shift的效果，如图10所示：

<img src="https://pic2.zhimg.com/80/v2-c6cf952e2981cdd77661ac09e4de1fe9_1440w.jpg" alt="img" style="zoom:50%;" />

​																			图10 cyclic shift

在(5,3)\(7,1)\(8,6,2,0)组成的新窗口中，只有相同编码的部分才能计算self-attention，不同编码位置间计算的self-attention需要归0，根据self-attention公式，最后需要进行Softmax操作，不同编码位置间计算的self-attention结果通过mask加上-100，在Softmax计算过程中，Softmax(-100)无线趋近于0，达到归0的效果。

<img src="https://pic1.zhimg.com/80/v2-d7d5407285453298b9dc5bc6e022fb48_1440w.jpg" alt="img" style="zoom: 33%;" />

​																								图11 mask形式

mask和相对位置编码有同样的形式，可以根据图1的计算过程理解一下

![[公式]](https://www.zhihu.com/equation?tex=mask.shape%3D%28numwindows%2C+M%5E%7B2%7D%2CM%5E%7B2%7D%29)

第一个 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 代表有 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 个tokens，第二个 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 代表每个token要计算 ![[公式]](https://www.zhihu.com/equation?tex=M%5E%7B2%7D) 次QK值。



例如：

在H=W=8，window_size=4，shift_size=2的情况下，生成mask如下：

```python
attn_mask =  tensor([[[   0.,    0.,    0.,  ...,    0.,    0.,    0.],
         [   0.,    0.,    0.,  ...,    0.,    0.,    0.],
         [   0.,    0.,    0.,  ...,    0.,    0.,    0.],
         ...,
         [-100., -100., -100.,  ...,    0., -100., -100.],
         [-100., -100., -100.,  ..., -100.,    0.,    0.],
         [-100., -100., -100.,  ..., -100.,    0.,    0.]]])
```

![[公式]](https://www.zhihu.com/equation?tex=mask.shape%3D%284%2C16%2C16%29)

可以根据图10,11试着理解一下attn_mask的结果







<img src="https://pic2.zhimg.com/v2-8b61983e137764f67b79dd688ab2acf5_r.jpg" alt="preview" style="zoom: 33%;" />



​																									图1

***相比较于文本信息，图片有更大的像素分辨率，Transformer的计算复杂度是token数量的平方(图1,每个token都要与其他token计算QK值)，如果将每个像素值算作一个token，其计算量非常大，不利于在多种机器视觉任务中的应用\***





在计算 Attention 时，希望 **仅留下具有相同 index 的 Query 和 Key 的计算结果**，而 **忽略不同 index** 的 Query 和 Key 的计算结果，如下所示：

<img src="https://img-blog.csdnimg.cn/20210915204126858.png" alt="img" style="zoom:50%;" />

**而若要在原始的四个窗口下得到正确计算结果，则必须给 Attention 运算结果加入一个 mask (如上图最右侧所示)，**



在 Window Attention 模块的前向过程代码中，包含一段：

        if mask is not None:
            nW = mask.shape[0]
            attn = attn.view(B_ // nW, nW, self.num_heads, N, N) + mask.unsqueeze(1).unsqueeze(0)
            attn = attn.view(-1, self.num_heads, N, N)
            attn = self.softmax(attn)
其将值设为 **-100** 的 mask 直接加到 **Attention Map** 上，并在 reshape 后通过 **Softmax** 近似忽略之。



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220603102301212.png" alt="image-20220603102301212" style="zoom:50%;" />















![image-20220508203609361](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508203609361.png)









![image-20220508195939884](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508195939884.png)

![image-20220508200106559](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220508200106559.png)















