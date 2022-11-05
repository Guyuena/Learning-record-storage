



# 用随机梯度下降来优化人生

**要有目标**。你需要有目标。短的也好，长的也好。认真定下的也好，别人那里捡的也好。就跟随机梯度下降需要有个目标函数一样。

**目标要大**。不管是人生目标还是目标函数，你最好不要知道最后可以走到哪里。如果你知道，那么你的目标就太简单了，可能是个凸函数。你可以在一开始的时候给自己一些小目标，例如期末考个80分，训练一个线性模型。但接下来得有更大的目标，财富自由也好，100亿参数的变形金刚也好，得足够一颗赛艇。

**坚持走**。不管你的目标多复杂，随机梯度下降都是最简单的。每一次你找一个大概还行的方向（梯度），然后迈一步（下降）。两个核心要素是方向和步子的长短。但最重要的是你得一直走下去，能多走几步就多走几步。

**痛苦的卷**。每一步里你都在试图改变你自己或者你的模型参数。改变带来痛苦。但没有改变就没有进步。你过得很痛苦不代表在朝着目标走，因为你可能走反了。但过得很舒服那一定在原地踏步。需要时刻跟自己作对。

**可以躺平**。你用你内心的激情来迈步子。步子太小走不动，步子太长容易过早消耗掉了激情。周期性的调大调小步长效果挺好。所以你可以时不时休息休息。

**四处看看**。每一步走的方向是你对世界的认识。如果你探索的世界不怎么变化，那么要么你的目标太简单，要么你困在你的舒适区了。随机梯度下降的第一个词是随机，就是你需要四处走走，看过很多地方，做些错误的决定，这样你可以在前期迈过一些不是很好的舒适区。

**快也是慢**。你没有必要特意去追求找到最好的方向和最合适的步子。你身边当然会有幸运之子，他们每一步都在别人前面。但经验告诉我们，随机梯度下降前期进度太快，后期可能乏力。就是说你过早的找到一个舒适区，忘了世界有多大。所以你不要急，前面徘徊一段时间不是坏事。成名无需太早。

**赢在起点**。起点当然重要。如果你在终点附近起步，可以少走很多路。而且终点附近的路都比较平，走着舒服。当你发现别人不如你的时候，看看自己站在哪里。可能你就是运气很好，赢在了起跑线。如果你跟别人在同一起跑线，不见得你能做更好。

**很远也能到达**。如果你是在随机起点，那么做好准备前面的路会非常不平坦。越远离终点，越人迹罕见。四处都是悬崖。但随机梯度下降告诉我们，不管起点在哪里，最后得到的解都差不多。当然这个前提是你得一直按照梯度的方向走下去。如果中间梯度炸掉了，那么你随机一个起点，调整步子节奏，重新来。

**独一无二**。也许大家有着差不多的目标，在差不多的时间毕业买房结婚生娃。但每一步里，每个人内心中看到的世界都不一样，导致走的路不一样。你如果跑多次随机梯度下降，在各个时间点的目标函数值可能都差不多，但每次的参数千差万别。不会有人关心你每次训练出来的模型里面参数具体是什么值，除了你自己。

**简单最好** 。当然有比随机梯度下降更复杂的算法。他们想每一步看想更远更准，想步子迈最大。但如果你的目标很复杂，简单的随机梯度下降反而效果最好。深度学习里大家都用它。关注当前，每次抬头瞄一眼世界，快速做个决定，然后迈一小步。小步快跑。只要你有目标，不要停，就能到达。









学习地址[awesome-vit/README_zh-CN.md at main · open-mmlab/awesome-vit (github.com)](https://github.com/open-mmlab/awesome-vit/blob/main/README_zh-CN.md)



# Attention

人类利用有限的注意力资源从大量信息中快速筛选出高价值信息的手段，是人类在长期进化中形成的一种生存机制，人类视觉注意力机制极大地提高了视觉信息处理的效率与准确性。

深度学习中的注意力机制从本质上讲和人类的选择性视觉注意力机制类似，核心目标也是从众多信息中选择出对当前任务目标更关键的信息。



![](C:\Users\jc\Pictures\Vision-Transforme思维导图.png)







![](C:\Users\jc\Pictures\150126580-11f75810-e4da-4c38-a0fd-ee6a8c7a0a18.png)



**Token 化模块**

![](C:\Users\jc\Pictures\2.png)



**位置编码模块**

![](C:\Users\jc\Pictures\3.png)

**注意力模块**

![](C:\Users\jc\Pictures\4.png)





**NLP领域的Attention**

<img src="https://pic3.zhimg.com/80/v2-add603bc247f87617e978c28b7bf1272_720w.jpg" alt="img" style="zoom:50%;" />





**软注意力(soft attention)**，另一种则是**强注意力(hard attention)**。以及被用来做文本处理的NLP领域的**自注意力机制**



1. **软注意力机制。**可分为基于输入项的软注意力（Item-wise Soft Attention）和基于位置的软注意力（Location-wise Soft Attention）
2. **强注意力机制**。可分为基于输入项的强注意力（Item-wise Hard Attention）和基于位置的强注意力（Location-wise Hard Attention）。
3. **自注意力机制**。是注意力机制的变体，其减少了对外部信息的依赖，更擅长捕捉数据或特征的内部相关性。自注意力机制在文本中的应用，主要是通过计算单词间的互相影响，来解决长距离依赖问题



软注意力的关键点在于，这种注意力更关注区域或者通道，而且软注意力是确定性的注意力，学习完成后直接可以通过网络生成，最关键的地方是**软注意力是可微的**，这是一个非常重要的地方。可以微分的注意力就可以通过神经网络算出梯度并且前向传播和后向反馈来学习得到注意力的权重。

强注意力与软注意力不同点在于，首先强注意力是更加关注点，也就是图像中的每个点都有可能延伸出注意力，同时强注意力是一个随机的预测过程，更强调动态变化。当然，最关键是强注意力是一个不可微的注意力，训练过程往往是通过强化学习(reinforcement learning)来完成的。















**深度学习中的注意力可以被广义地解释为重要性权重向量（a vector of importance weights）：为了预测或推断一个元素，例如图像中的一个像素或句子中的一个单词，我们使用注意力向量来估计它与其他元素的相关性有多强（有许多文献中用 `attends to` 来描述），并将其与注意力向量加权的值之和作为目标的近似值**。



**传统的编码器解码器网络是从编码器的最后隐藏状态中构建一个单独的上下文向量（这个去看RNN网络编码器的最后隐层输出，这就是一个上下文向量），而注意力机制是在上下文向量和整个源输入之间创建快捷方式（shortcuts）。这些快捷方式连接的权重可针对每个输出元素进行自定义**。



**虽然上下文向量可以访问整个输入序列，但我们不需要担心遗忘。源和目标之间的对齐（alignment）由上下文向量学习和控制**。







## 对齐分数（alignment scores）



对齐分数矩阵是一个很好的辅助工具，可以清楚地显示源词和目标词之间的相关性。



**解释举例**

![](C:\Users\jc\Pictures\5.png)



现在定义NMT引入的注意机制。假设，我们有一个长度为 n  的源序列 x，并试图输出一个长度为 m  的目标序列 y :

![image-20220511093634463](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220511093634463.png)

![](C:\Users\jc\Pictures\6.png)

![](C:\Users\jc\Pictures\7.png)

<img src="C:\Users\jc\Pictures\8.png" style="zoom: 33%;" />





## Attention机制的思想

**对不同的item感兴趣程度、注意力分布不同，考虑对不同的item施加不同的权重，即求当前query关于不同key下的注意力分布及当前query的注意力分数**

把Attention机制从Encoder-Decoder框架中剥离，并进一步做抽象，可以更容易看懂Attention机制的本质思想。

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214040039-360917432.png)



我们可以这样来看待Attention机制（参考图9）：将Source中的构成元素想象成是由一系列的<Key,Value>数据对构成，此时给定Target中的某个元素Query，通过计算Query和各个Key的相似性或者相关性，得到每个Key对应Value的权重系数，然后对Value进行加权求和，即得到了最终的Attention数值。所以本质上Attention机制是对Source中元素的Value值进行加权求和，而Query和Key用来计算对应Value的权重系数。即可以将其本质思想改写为如下公式：

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214040494-1181115496.png)

其中，Lx=||Source||代表Source的长度，公式含义即如上所述。上文所举的机器翻译的例子里，因为在计算Attention的过程中，Source中的Key和Value合二为一，指向的是同一个东西，也即输入句子中每个单词对应的语义编码，所以可能不容易看出这种能够体现本质思想的结构。

当然，从概念上理解，把Attention仍然理解为从大量信息中有选择地筛选出少量重要信息并聚焦到这些重要信息上，忽略大多不重要的信息，这种思路仍然成立。聚焦的过程体现在权重系数的计算上，权重越大越聚焦于其对应的Value值上，即权重代表了信息的重要性，而Value是其对应的信息。

也可以将Attention机制看作一种软寻址（Soft Addressing）:Source可以看作存储器内存储的内容，元素由地址Key和值Value组成，当前有个Key=Query的查询，目的是取出存储器中对应的Value值，即Attention数值。通过Query和存储器内元素Key的地址进行相似性比较来寻址，之所以说是软寻址，指的不像一般寻址只从存储内容里面找出一条内容，而是可能从每个Key地址都会取出内容，取出内容的重要性根据Query和Key的相似性来决定，之后对Value进行加权求和，这样就可以取出最终的Value值，也即Attention值。所以不少研究人员将Attention机制看作软寻址的一种特例，这也是非常有道理的。

至于Attention机制的具体计算过程，如果对目前大多数方法进行抽象的话，可以将其归纳为两个过程：第一个过程是根据Query和Key计算权重系数，第二个过程根据权重系数对Value进行加权求和。而第一个过程又可以细分为两个阶段：第一个阶段根据Query和Key计算两者的相似性或者相关性；第二个阶段对第一阶段的原始分值进行归一化处理；这样，可以将Attention的计算过程抽象为如图10展示的三个阶段。

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214041017-2129103820.png)

**在第一个阶段**，可以引入不同的函数和计算机制，根据Query和某个Key_i，计算两者的相似性或者相关性，最**常见的方法包括**：求两者的向量点积、求两者的向量Cosine相似性或者通过再引入额外的神经网络来求值，即如下方式：

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214041577-528264751.png)



第一阶段产生的分值根据具体产生的方法不同其数值取值范围也不一样，第二阶段引入类似SoftMax的计算方式对第一阶段的得分进行数值转换，一方面可以进行归一化，将原始计算分值整理成所有元素权重之和为1的概率分布；另一方面也可以通过SoftMax的内在机制更加突出重要元素的权重。即一般采用如下公式计算：

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214042022-397623281.png)

第二阶段的计算结果a_i即为value_i对应的权重系数，然后进行加权求和即可得到Attention数值：

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214042422-2015919654.png)

通过如上三个阶段的计算，即可求出针对Query的Attention数值，目前绝大多数具体的注意力机制计算方法都符合上述的三阶段抽象计算过程。





**在注意力的帮助下，源序列和目标序列之间的依赖关系不再受中间距离的限制！鉴于机器翻译中注意力的巨大改善，它很快被扩展到计算机视觉领域**

![](C:\Users\jc\Pictures\9.png)

- **`(^)` 它增加了 \**1 / n 1/\sqrt{n}1/\*n\*\** 的比例因子，这是因为当输入较大时，softmax函数可能具有极小的梯度，难以进行有效学习**。









可以举一个更简单的例子，假设有一个训练好的分类网络，输入一张图片，训练好的分类网络权重 W 和图片 X 进行注意力计算，从 X 中提取能够有助于分类的特征，该特征最终可以作为类别分类依据。W 和 X 都是矩阵，**要想利用 W 矩阵来达到重加权 X 目的，等价于计算 W 和 X 的相似度(点乘)，然后将该相似度变换为权重概率分布，再次作用于 X 上就可以**。

**以一个简单猫狗二分类例子说明。**网络最终输出是 2x1 的向量，第一个数大则表示猫类别，否则为狗类别，假设网络已经训练好了，其 W 为 shape 为 2x1 的向量，值为 **[[0.1, 0.5]]**，X 表示输入图片 shape 也是 2x1，其值为 **[[0.1, 0.8]]**，可以看出其类别是狗，采用如下的计算步骤即可正确分类：

- W 和 X 的转置相乘，即计算 W 中每个值和 X 中每个值的相似度，得到 2x2 矩阵，值为 [[0.01,0.08], [0.05,0.4]]。
- 第二个维度进行 Softmax，将其转化为概率权重图即为 [[0.4825, 0.5175], [0.4134, 0.5866]]。
- 将上述概率权重乘以 X，得到 shape 为 2x1 输出，值为 [[0.4622, 0.5106]]。
- 此时由于第二个值大，所以正确分类为狗。


X 是含有狗的图片矩阵，能够正确分类的前提是训练好的 W 矩阵中第二个数大于第一个数。可以简单理解上述过程是**计算 W 和 X 的相似度，如果两个向量相似(都是第二个比第一个数大)，那么就分类为狗，否则就分类为猫**。









# Self-Attention

**自我注意（Self-attention），也称为内部注意（intra-attention），是一种将单个序列的不同位置联系起来以计算同一序列的表示的注意机制。它在机器阅读、抽象概括或图像描述生成方面非常有用**。



而Self-Attention顾名思义，指的不是Target和Source之间的Attention机制，而是Source内部元素之间或者Target内部元素之间发生的Attention机制，也可以理解为Target=Source这种特殊情况下的注意力计算机制

如果是常规的Target不等于Source情形下的注意力计算，其物理含义正如上文所讲，比如对于机器翻译来说，本质上是目标语单词和源语单词之间的一种单词对齐机制。那么如果是Self-Attention机制，一个很自然的问题是：通过Self Attention到底学到了哪些规律或者抽取出了哪些特征呢？或者说引入Self-Attention有什么增益或者好处呢？我们仍然以机器翻译中的Self Attention来说明，图11和图12是可视化地表示Self-Attention在同一个英语句子内单词间产生的联系。

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214042906-362241627.png)

图11 可视化Self Attention实例

![img](https://img2020.cnblogs.com/blog/1470684/202006/1470684-20200606214043466-1501386193.png)

图12 可视化Self Attention实例



从两张图（图11、图12）可以看出，Self Attention可以捕获同一个句子中单词之间的一些句法特征（比如图11展示的有一定距离的短语结构）或者语义特征（比如图12展示的its的指代对象Law）。



很明显，引入Self Attention后会更容易捕获句子中长距离的相互依赖的特征，因为如果是RNN或者LSTM，需要依次序序列计算，对于远距离的相互依赖的特征，要经过若干时间步步骤的信息累积才能将两者联系起来，而距离越远，有效捕获的可能性越小。

但是Self-Attention在计算过程中会直接将句子中任意两个单词的联系通过一个计算步骤直接联系起来，所以远距离依赖特征之间的距离被极大缩短，有利于有效地利用这些特征。除此外，Self-Attention对于增加计算的并行性也有直接帮助作用。





















# Transformer

## Key, Value and Query

transformer 中的主要部件是多头自我注意力机制（multi-head self-attention mechanism）。transformer 将输入的编码表示视为一组键值对，( K , V ) (\mathbf{K}, \mathbf{V})(**K**,**V**)，两者都是 n n*n* 维（输入序列长度）；在NMT的上下文中，键和值都是编码器隐藏状态。在解码器中，前一个输出被压缩成一个查询（query，维度为 m m*m* 的 Q \mathbf{Q}**Q**），下一个输出是通过映射这个查询和该组键和值产生的。



**转换器**采用[ scaled dot-product attention](https://lilianweng.github.io/lil-log/2018/06/24/attention-attention.html#summary)：输出是值的加权和，其中分配给每个值的权重由具有所有键的查询的点积确定：



![](C:\Users\jc\Pictures\10.png)

![](C:\Users\jc\Pictures\12.png)

<img src="C:\Users\jc\Pictures\14.png" style="zoom:25%;" />



**多头机制不是只计算一次注意力，而是并行地多次运行缩放的点积注意力。独立的注意力输出被简单地连接并线性转换成预期的维度**



![](C:\Users\jc\Pictures\13.png)

图解transformer ：https://jalammar.github.io/illustrated-transformer/



# 为什么 dot-product attention 需要被 scaled？

[(58条消息) 为什么 dot-product attention 需要被 scaled？_夏树让的博客-CSDN博客](https://blog.csdn.net/qq_37430422/article/details/105042303)







**Attention九层塔——理解Attention的九层境界**



1. **看山是山——Attention是一种注意力机制**
2. **看山看石——数学上看，Attention是一种广泛应用的加权平均**
3. **看山看峰——自然语言处理中，Attention is all you need**
4. **看山看水——BERT系列大规模无监督学习将Attention推到了新的高度**
5. **水转山回——计算机视觉中，Attention是有效的非局域信息融合技术**
6. **山高水深——计算机视觉中，Attention will be all you need**
7. **山水轮回——结构化数据中，Attention是辅助GNN的利器**
8. **山中有山——逻辑可解释性与Attention的关系**
9. **山水合一——Attention的多种变种及他们的内在关联**





## 1. Attention是一种注意力机制

顾名思义，attention的本意是生物的注意力机制在人工智能中的应用。注意力机制有什么好处呢？简单地说，可以关注到完成目标场景中所需要的特征。比如说有一系列的特征 ![[公式]](https://www.zhihu.com/equation?tex=f_1%2C+f_2%2C+...%2C+f_n) 。可能目标场景仅仅需要 ![[公式]](https://www.zhihu.com/equation?tex=f_2%2C+f_3)，那么attention可以有效地“注意到”这两个特征，而忽略其他的特征。attention最早出现在了递归神经网络（RNN）中[[1\]](https://zhuanlan.zhihu.com/p/362366192#ref_1)，作者Sukhbaatar举了这样的例子：

![img](https://pic2.zhimg.com/80/v2-8b3ffdc6320140696ffc96c1571ceaf9_720w.jpg)

上图中，如果我们需要结合（1）到（4）这四句话，根据问题Q回答出正确答案A。可以看到，Q与（3）没有直接的关联，但是我们需要从（3）中得到正确答案bedroom。一个比较自然地想法是，我们引导模型的注意力，从问题开始，从四句话中寻找线索，从而回答问题。

![img](https://pic1.zhimg.com/80/v2-cf0be463207af67c6cc47f68ef35c9c8_720w.jpg)

如上图所示，通过问题中的apple，我们转到了第四句话，然后注意力转移到第三句话，确定回答中的bedroom。

到这里，我们应该已经抓住了attention最早的理解，**达到了第一层——看山是山**。

现在我们的问题是，如何设计这样的模型，以达到这样的效果？

最早的实现是基于显式的存储器，把每一步的结果都存下来，“人工实现”注意力的转移。

还是上面的例子，

![img](https://pic2.zhimg.com/80/v2-a5b5df8902420bb10688fcf6ed1dc38d_720w.jpg)

如上图所示，通过对存储器的处理，和对注意到的东西的更新，实现attention。这种方法比较简单，但是很hand-crafted，后来已经逐渐废弃了，我们需要升级我们的认知，达到比较抽象层次。

## 2. Attention是一种加权平均

Attention的经典定义，是来源于Attention is all you need这篇旷世奇作[[2\]](https://zhuanlan.zhihu.com/p/362366192#ref_2)。虽然前面一些工作也发现了类似的技术（如self-attention），但是这篇文章因为提出了“attention就是一切你想要的”这一大胆而逐渐被证实的论断，而享有了载入史册的至高荣耀。这一经典定义，就是下面的公式。

![[公式]](https://www.zhihu.com/equation?tex=Attention%28Q%2C+K%2C+V%29+%3D+softmax%28%5Cfrac%7BQK%5ET%7D%7B%5Csqrt%7Bd_k%7D%7D%29V)

公式含义下面讲，先讲讲意义。这一公式，也基本上是近五年来，科研人员最早接触到的经典定义。**这一公式在自然语言处理中的地位，即将接近牛顿定律在经典力学中的地位，已经成为了搭建复杂模型的基本公式。**

这个公式看似复杂，但是理解了之后就会发现非常的简单和基本。先讲一下每个字母的含义。字面意思：Q表示query，表示的是K表示key，V表示value， ![[公式]](https://www.zhihu.com/equation?tex=d_k) 是K的维度。这时候就要有人问了，什么是query，什么是key，什么是value？因为这三个概念都是这篇文章引入的，所以说，这篇文章中的公式摆在Q的这个位置的东东就是query，摆在K这个位置的就叫key，摆在V这个位置的就是value。这就是最好的解读。**换句话说，这个公式类似于牛顿定律，本身是可以起到定义式的作用的。**

为了便于大家理解，我在这里举几个例子解释一下这三个概念。

1、 【搜索领域】在bilibili找视频，key就是bilibili**数据库中的关键字序列**（比如宅舞、鬼畜、马保国等），query就是你**输入的关键字序列**，比如马保国、鬼畜，value就是你**找到的视频序列**。

2、【推荐系统】在淘宝买东西，key就是淘宝数据库中所有的商品信息，query就是你最近关注到的商品信息，比如高跟鞋、紧身裤，value就是推送给你的商品信息。

上面两个例子比较的具体，我们往往在人工智能运用中，key，query，value都是隐变量特征。因此，他们的含义往往不那么显然，我们需要把握的是这种计算结构。

回到公式本身，这个公式本质上就是表示**按照关系矩阵进行加权平均。**关系矩阵就是 ![[公式]](https://www.zhihu.com/equation?tex=QK%5ET) ，而softmax就是把关系矩阵归一化到概率分布，然后按照这个概率分布对V进行重新采样，最终得到新的attention的结果。

下图展示了在NLP中的Attention的具体含义。我们现在考虑一个单词it的特征，那么它的特征将根据别的单词的特征加权得到，比如说可能the animal跟it的关系比较近（因为it指代the animal），所以它们的权值很高，这种权值将影响下一层it的特征。更多有趣的内容请参看 The Annotated Transformer[[3\]](https://zhuanlan.zhihu.com/p/362366192#ref_3)和illustrate self-attention[[4\]](https://zhuanlan.zhihu.com/p/362366192#ref_4)。

![img](https://pic4.zhimg.com/80/v2-2dda63075eb71a8239bc56b8a726a4ab_720w.jpg)

看到这里，大概能明白attention的基础模块，**就达到了第二层，看山看石**。

## 3. 自然语言处理中，Attention is all you need。

Attention is all you need这篇文章的重要性不只是提出了attention这一概念，更重要的是提出了Transformer这一**完全基于attention的结构**。完全基于attention意味着不用递归recurrent，也不用卷积convolution，而完全使用attention。下图是attention与recurrent，convolution的计算量对比。

![img](https://pic4.zhimg.com/80/v2-ebd874107b8fdc3bae690873782f76a3_720w.jpg)

可以看到，attention比recurrent相比，需要的序列操作变成了O(1)，尽管每层的复杂性变大了。这是一个典型的计算机内**牺牲空间换时间**的想法，由于计算结构的改进（如加约束、共享权重）和硬件的提升，这点空间并不算什么。

convolution也是典型的不需要序列操作的模型，但是其问题在于它是依赖于2D的结构（所以天然适合图像），同时它的计算量仍然是正比于输入的边长的对数的，也就是Ologk(n)。但是attention的好处是**最理想情况下可以把计算量降低到O(1)。**也就是说，在这里我们其实已经能够看到，attention比convolution确实有更强的潜力。

Transformer的模型放在下面，基本就是attention模块的简单堆叠。由于已经有很多文章讲解其结构，本文在这里就不展开说明了。它在机器翻译等领域上，吊打了其他的模型，展示了其强大的潜力。明白了Transformer，就已经初步摸到了attention的强大，**进入了看山看峰的境界**。

![img](https://pic4.zhimg.com/80/v2-42c990f7900530045c0e99a2f1cf1177_720w.jpg)

## 4. 看山看水——BERT系列大规模无监督学习将Attention推到了新的高度。

Bidirectional Encoder Representation from Transformers  ： BERT



BERT[[5\]](https://zhuanlan.zhihu.com/p/362366192#ref_5)的推出，将attention推到了一个全新的层次。BERT创造性地提出在大规模数据集上无监督预训练加目标数据集微调（fine-tune）的方式，采用统一的模型解决大量的不同问题。BERT的效果非常好，在11个自然语言处理的任务上，都取得了非凡的提升。GLUE上提升了7.7%，MultiNLI提升了4.6%，SQuAD v2.0提升了5.1%。

BERT的做法其实非常简单，本质就是大规模预训练。利用大规模数据学习得到其中的语义信息，再把这种语义信息运用到小规模数据集上。BERT的贡献主要是：1）提出了一种双向预训练的方式。（2）证明了可以**用一种统一的模型来解决不同的任务**，而不用为不同的任务设计不同的网络。（3）在11个自然语言处理任务上取得了提升。

（2）和（3）不需要过多解释。这里解释一下（1）。之前的OpenAI GPT传承了attention is all you need，采用的是单向的attention（下图右），也就是说输出内容只能attention到之前的内容，但是BERT（下图左）采用的是双向的attention。BERT这种简单的设计，使得他大幅度超过了GPT。这也是AI届一个典型的小设计导致大不同的例子。

![img](https://pic1.zhimg.com/80/v2-d7adc4443cca026893c4a477a78b9f70_720w.jpg)BERT和GPT的对比

BERT提出了几个简单的**无监督的预训练方式**。第一个是Mask LM，就是挡住一句话的一部分，去预测另外一部分。第二个是Next Sentence Prediction (NSP) ，就是预测下一句话是什么。这种简单的预训练使得BERT抓住了一些基本的语义信息和逻辑关系，帮助BERT在下流任务取得了非凡的成就。

理解了BERT是如何一统NLP江湖的，**就进入了看山看水的新境界。**

## 5. 水转山回——计算机视觉中，Attention是有效的非局域信息融合技术。

Attention机制对于计算机视觉能不能起到帮助作用呢？回到我们最初的定义，attention本身是一个加权，加权也就意味着可以融合不同的信息。CNN本身有一个缺陷，每次操作只能关注到卷积核附近的信息（local information），不能融合远处的信息（non-local information)。而attention可以把远处的信息也帮忙加权融合进来，起一个辅助作用。基于这个idea的网络，叫做non-local neural networks[[6\]](https://zhuanlan.zhihu.com/p/362366192#ref_6) 。

![img](https://pic3.zhimg.com/80/v2-3812314e1a90330354d75eb860ad7daa_720w.jpg)比如图中的球的信息，可能和人的信息有一个关联，这时候attention就要起作用了

这篇提出的non-local（非局部）操作和attention非常像，假设有 ![[公式]](https://www.zhihu.com/equation?tex=%5Cbold%7Bx_i%7D+) 和 ![[公式]](https://www.zhihu.com/equation?tex=%5Cbold%7Bx_j%7D) 两个点的图像特征，可以计算得到新的特征 ![[公式]](https://www.zhihu.com/equation?tex=%5Cbold%7By_i%7D+) 为：

![[公式]](https://www.zhihu.com/equation?tex=%5Cbold%7By_i%7D+%3D+%5Cfrac%7B1%7D%7BC%28%5Cbold%7Bx%7D%29%7D%5CSigma_%7Bj%7Df%28%5Cbold%7Bx_i%7D%2C+%5Cbold%7Bx_j%7D%29g%28%5Cbold%7Bx_j%7D%29)

公式里的 ![[公式]](https://www.zhihu.com/equation?tex=C%28%5Cbold%7Bx%7D%29) 为归一化项，函数f和g可以灵活选择（注意之前讲的attention其实是f和g选了特例的结果）。在论文中，f取得是高斯关系函数，g取得是线性函数。提出的non-local模块被加到了CNN基线方法中，在多个数据集上取得了SOTA结果。

之后还有一些文献提出了其他把CNN和attention结合的方法[[7\]](https://zhuanlan.zhihu.com/p/362366192#ref_7)，都取得了提升效果。看到了这里，也对attention有了新的层次的理解。

## 6. 山高水深——计算机视觉中，Attention will be all you need。

在NLP中transformer已经一统江湖，那么在计算机视觉中，transformer是否能够一统江湖呢？这个想法本身是non-trivial的，因为语言是序列化的一维信息，而图像天然是二维信息。CNN本身是天然适应图像这样的二维信息的，但transformer适应的是语言这种是一维信息。上一层已经讲了，有很多工作考虑把CNN和attention加以结合，那么能否设计纯transformer的网络做视觉的任务呢？

**最近越来越多的文章表明，Transformer能够很好地适应图像数据，有望在视觉届也取得统治地位。**

第一篇的应用到的视觉Transformer来自Google，叫Vision Transformer[[8\]](https://zhuanlan.zhihu.com/p/362366192#ref_8)。这篇的名字也很有趣，an image is worth 16x16 words，即一幅图值得16X16个单词。这篇文章的核心想法，就是把一幅图变成16x16的文字，然后再输入Transformer进行编码，之后再用简单的小网络进行下有任务的学习，如下图所示。

![img](https://pic1.zhimg.com/80/v2-947fb5bac4c832790d336827c2e63888_720w.jpg)

Vision transformer主要是把transformer用于图像分类的任务，那么能不能把transformer用于目标检测呢？Facebook提出的模型DETR（detection transformer)给出了肯定的回答[[9\]](https://zhuanlan.zhihu.com/p/362366192#ref_9)。DETR的模型架构也非常简单，如下图所示，输入是一系列提取的图片特征，经过两个transformer，输出一系列object的特征，然后再通过前向网络将物体特征回归到bbox和cls。更详细的介绍可以参看 

[@陀飞轮](https://www.zhihu.com/people/9414183bd7a1ed7354cbbdbaa41a870c)

 的文章：



![img](https://pic2.zhimg.com/80/v2-cc3dd3a0aeb677bd9563ba96e8d09f25_720w.jpg)

[计算机视觉"新"范式: Transformer818 赞同 · 37 评论文章![img](https://pic3.zhimg.com/v2-f746c1cc0f1a25c773e8b5e2810ef1b2_ipico.jpg)](https://zhuanlan.zhihu.com/p/266069794)

在计算机视觉的其他领域，Transformer也在绽放新的活力。目前Transformer替代CNN已经成为一个必然的趋势，也就是说，Attention is all you need将在计算机视觉也成立。看到这里，**你将会发现attention山高水深，非常玄妙。**

## 7. 山水轮回——结构化数据中，Attention是辅助GNN的利器。

前面几层我们已经看到，attention在一维数据（比如语言）和二位数据（比如图像）都能有很好的应用，那么对于高维数据（比如图数据），能否有出色的表现呢？

最早地将attention用于图结构的经典文章是Graph Attention Networks（GAT，哦对了这个不能叫做GAN）[[10\]](https://zhuanlan.zhihu.com/p/362366192#ref_10)。图神经网络解决的基本问题是，给定图的结构和节点的特征，如何获取一个图的特征表示，来在下游任务（比如节点分类）中取得好的结果。那么爬到第七层的读者们应该可以想到，attention可以很好的用在这种关系建模上。

GAN的网络结构也并不复杂，即便数学公式有一点点多。直接看下面的图。

![img](https://pic1.zhimg.com/80/v2-874c9ff3d6466351f8e98f227d2adcf8_720w.jpg)GAT的网络结构

每两个节点之间先做一次attention获取一组权重，比如图中的 ![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%5Calpha_%7B1%2C+2%7D) 表示1和2之间的权重。然后再用这组权重做一个加权平均，再使用leakyRelu做一个激活。最后把多个head的做一个平均或者联结即可。

看懂了原来GAT其实就是attention的一个不难的应用，**就进入了第七层，山水轮回。**

## 8.山中有山——逻辑可解释性与Attention的关系

尽管我们已经发现attention非常有用，如何深入理解attention，是一个研究界未解决的问题。甚至进一步说，什么叫做深入理解，都是一个全新的问题。大家想想看，CNN是什么时候提出来的？LeNet也就是98年。CNN我们还没理解的非常好，attention对于我们来说更新了。

我认为，**attention是可以有比CNN更好的理解的。**为什么？简单一句话，attention这种加权的分析，天然就具有可视化的属性。而可视化是我们理解高维空间的利器。

给两个例子，第一个例子是NLP中的BERT，分析论文显示[[11\]](https://zhuanlan.zhihu.com/p/362366192#ref_11)，学习到的特征有非常强的结构性特征。

![img](https://pic3.zhimg.com/80/v2-00987d2317b9ce4f8e45103a17a68da6_720w.jpg)

还有一个FACEBOOK最近的的工作DINO[[12\]](https://zhuanlan.zhihu.com/p/362366192#ref_12)，下图图右是无监督训练得到的attention map。是不是非常的震惊？

![img](https://pic3.zhimg.com/80/v2-d413c33a662eb9255aaf29c901252676_720w.jpg)

**到目前为止，读者已经到了新的境界，山中有山。**

## 9.山水合一——Attention的多种变种及他们的内在关联 

就跟CNN可以搭建起非常厉害的检测模型或者更高级的模型一样，attention的最厉害的地方，是它可以作为基本模块搭建起非常复杂的（用来灌水的）模型。

这里简单列举一些attention的变种[[13\]](https://zhuanlan.zhihu.com/p/362366192#ref_13)。首先是全局attention和部分attention。

![img](https://pic4.zhimg.com/80/v2-b673e48e1a55b24284c71adf58d428c3_720w.jpg)

全局attention就是上面讲的，部分attention主要是还允许某些特征在做attention之前先做融合，再进一步attention。最近爆火的swin transformer就可以看作是把这个变种发扬光大了。视频：[灵魂画手带你一分钟读懂吊打CNN的swintransformer](https://www.zhihu.com/zvideo/1359837715438149632)。

接下来是hard attention和soft attention。

![img](https://pic1.zhimg.com/80/v2-b58f252f80590449584aa1e0f3629f68_720w.jpg)

之前我们讲的基本都是soft attention。但是站到采样的角度来讲，我们可以考虑hard attention，把概率当成一个分布，然后再进行多项式采样。这个或许在强化学习里面，有启发性作用。

最近又有一堆觉得MLP也挺强的工作[[14\]](https://zhuanlan.zhihu.com/p/362366192#ref_14)。笔者认为，他们也是参考了attention的模式，采用了不同的结构达到同一种效果。当然，说不定attention最后会落到被MLP吊打的下场。

但是attention的理念，永远不会过时。attention作为最朴素也最强大的数据关系建模基本模块，必将成为每个AI人的基本功。













# BERT





**而Transformer又可以进行堆叠，形成一个更深的神经网络**

<img src="https://img-blog.csdnimg.cn/20210401145455377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDc5OTIxNw==,size_16,color_FFFFFF,t_70" alt="img" style="zoom:67%;" />

<img src="https://pic2.zhimg.com/v2-ba05a54f0bf152a971468099b02999c6_720w.jpg?source=172ae18b" alt="查看源图像" style="zoom:67%;" />

<img src="https://imgmd.oss-cn-shanghai.aliyuncs.com/BERT_IMG/bert-%E6%A8%A1%E5%9E%8B%E7%BB%93%E6%9E%84.jpg" alt="img" style="zoom:67%;" />



BERT的全称为Bidirectional Encoder Representation from Transformers，是一个预训练的语言表征模型。它强调了不再像以往一样采用传统的单向语言模型或者把两个单向语言模型进行浅层拼接的方法进行预训练，而是采用新的**masked language model（MLM）**，以致能生成**深度的双向**语言表征。

Masked LM 的任务描述为：给定一句话，随机抹去这句话中的一个或几个词，要求根据剩余词汇预测被抹去的几个词分别是什么，如下图所示。

<img src="https://img-blog.csdnimg.cn/20210401145712542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDc5OTIxNw==,size_16,color_FFFFFF,t_70" alt="img" style="zoom:50%;" />

<img src="https://img-blog.csdnimg.cn/2021040115401131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDc5OTIxNw==,size_16,color_FFFFFF,t_70" alt="img" style="zoom:50%;" />



