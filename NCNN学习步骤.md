# 👋超有用NCNN参考资料整理

[👋超有用NCNN参考资料整理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/449765328)

[ncnn源码学习（一）：学习顺序 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/454835595)



# ncnn源码学习（一）：学习顺序

## 【前言】

这段时间做了很多ncnn的工作，但说实话一直没读过ncnn的源码。很久之前就听大家说过ncnn的代码非常优秀了，最近挪了点时间，读一读，后面还会再更新的。



## 正文

## 【代码版本】

截止到目前ncnn已经快5年了，代码也越来越庞大，很多都是一些新添加的功能支持啥的。这回读代码，我们比较关心的是ncnn整个的架构以及它代码的逻辑是怎么走的，所以这里我选用了ncnn最早的一个release的代码，这个代码的整体使用逻辑是现在的是一样，而且代码量比较少，看起来比较方便，我用的是这个：

[https://github.com/Tencent/ncnn/releases/tag/20170724github.com/Tencent/ncnn/releases/tag/20170724](https://github.com/Tencent/ncnn/releases/tag/20170724)

## 【demo】

我们来看一下ncnn自带的一个经典demo：squeezenet的代码，这里我只贴出与ncnn有关的部分：

```cpp
// 网络加载
ncnn::Net squeezenet;
squeezenet.load_param("squeezenet_v1.1.param");
squeezenet.load_model("squeezenet_v1.1.bin");

// 数据预处理
ncnn::Mat in = ncnn::Mat::from_pixels_resize(image.data, ncnn::Mat::PIXEL_BGR, image.cols, image.rows, 227, 227);
const float mean_vals[3] = { 104.f, 117.f, 123.f };
in.substract_mean_normalize(mean_vals, 0);

// 网络推理
ncnn::Extractor ex = squeezenet.create_extractor();
ex.input("data", in);
ncnn::Mat out;
ex.extract("prob", out);
```

从上面的代码可以看到，整个过程大体可以分为四个部分：

1. 网络加载

2. 1. load_param
   2. load_model

3. 数据预处理

4. 1. from_pixels_resize
   2. substract_mean_normalize

5. 网络推理

6. 1. create_extractor
   2. input
   3. extract

7. 每一层的推理（隐含在模型内的）

# ncnn源码学习（二）：模型加载

## 【正文】

这次我们先来看一段ncnn应用代码中，最先出现的部分，模型加载：

```cpp
ncnn::Net squeezenet;
squeezenet.load_param("squeezenet_v1.1.param");
squeezenet.load_model("squeezenet_v1.1.bin");
```

首先我们能看到一个ncnn的类Net，这个就是用来记录网络的，我们跳过这个，有四个东西是我们比较关心的：

- 方法：load_param和load_model
- 文件：*.param和*.bin（可用netron可视化）

## 【load_param】

load_param的具体代码在net.cpp的第92行处，考虑到该函数是加载param文件的，先贴一下squeezenet_v1.1.param的内容，其中的各部分的含义我都按照源码的变量名指示了出来，看图：

![img](https://pic1.zhimg.com/v2-0ae054b8e3552ee86027ddd1344d3c9c_r.jpg)

在一个param文件里面，只有第一行与其他行之分。

- 第一行

- - 第一个数：指示了该param所表示的模型，一共有多少layer，layer的值是从第二行开始一直数下去的，所以如果增删某几行，这里也要对应修改
  - 第二个数，指示了该模型有多少个blob，可以理解成有多少个数据流节点。例如一个卷积就是一个输入blob一个输出blob，数据其实就是在blob之间流来流去的。

- 其他行：除了第一行，其他的都是layer行，上图用第三行举例，具体行内容依次为：

- - layer_type：该行layer对应的类型，例如输入Input、卷积Convolution、激活ReLU、池化Pooling等
  - layer_name：该行layer的名字，这个可以自己起，模型导出的时候导出工具自动生成的
  - bottom_count：这里有点反常识，这里的bottom是表示该layer在谁的下面，而不是它下面是谁。举例有一个网络片段：Conv->ReLU->Pooling，这里ReLU是Conv的bottom，是Pooling的top。所以这个参数要理解为该layer是多少个大哥的“小弟”
  - top_count：这个对比着bottom_count来理解就是该layer是多少个小弟的“大哥”
  - bottom_name：这个名字的数量是由bottom_count指示的，上图因为bottom_count为1所以只有一个。这里就是写明你头上大哥的名字
  - blob_name：可以对比理解为top_name，指示的是你底下小弟的名字
  - 特有参数：这个是该layer特有的一些参数，不是同一读参，由各具体layer实例读。例如卷积有kernel_size、stride_size、padding_size，ReLU又是无参的，Softmax则需要一个指示维度的参，具体参具体分析。

有了param文件的分析，我们结合源码对比写出load_param的伪代码了：

```python3
# layers列表，存下所有layer
# blobs列表，背后维护，为find_blob服务
layer_count, blob_count = read(param_file) # 读取第一行数据
for param_file is not EOF: # 循环读取每一行的layer数据
    layer_type, layer_name, bottom_count, top_count = read(param_file) # 读取前四个固定参数
    layer = create_layer(layer_type) # 根据layer类型创建一个layer
    for bottom_count:
        bottom_name = read(param_file) # 读取每一个bottom_name
        blob = find_blob(bottom_name) # 查找该blob，没有的话就要新建一个
        blob.consumers.append(layer) # 当前层是这个blob的消费者，这里的blob是大哥，当前层是小弟，没钱花找大哥要
        layer.bottoms.append(blob) # 记住谁是你的大哥！
    for top_count:
        blob_name= read(param_file) # 读取每一个blob_name
        blob = find_blob(bottom_name) # 查找该blob，没有的话就要新建一个
        blob.producer = layer # 当前层是这个blob的生产者，这里的blob是小弟，当前层是大哥，大哥要给小弟们派钱
        layer.tops.append(blob) # 花名册上把小弟名字写一写
    layer.param = read(param_file) # 读取该层的一些特殊参数
    layers.append(layer )
```

其实整个流程是很简单，对于某一层数据来说，首先要认清自己的身份(layer_type)，记住谁是你大哥(bottom_name)，记住谁是你小弟(blob_name)，认清自己几斤几两(特有参数)。

后面的load_model我这里就先不写了，因为这个涉及到layer的特有参数，这个我们后面拉几个layer出来单独分析。

## 【一点点感悟】

这个大哥小弟的例子还是我写这个文章的时候无意想到的，觉得很有意思。ncnn推理有一个特点，那就是从底往上递归深搜最短路跑的，这个我觉得用大哥小弟来举例子就很好。这里我先写了怕后面忘了。假定有人给了大哥一笔钱，让他把钱分给小弟(用户调用了input方法塞数据了)，等会会有小弟来要钱(用户调用了extract方法要数据)。这时候大哥有两个方案：1.老老实实给每一个小弟发钱，2.哪个小弟要钱单独给他发。考虑到一个复杂的网络，有很多的大哥、中哥、小哥、大第、中弟、小弟。发钱是一级一级往下发，小弟要钱是一级一级往上要，每一个要钱都是要底下所有人的份子。我们分析一下这两个方案：

- 方案一：每个人都发，大家都有钱，有些小弟还不需要钱，你也给他发了

- - 优点：每个人你都发了，缺钱也别来问，都给你了（直接完整推理，要什么数据取就行了）
  - 缺点：大哥都发出去了，累死累活的（全部计算量）

- 方案二：来要钱才给他，有些不要钱的不给了

- - 优点：大哥省事，谁要给谁（节省计算量）
  - 缺点：每个小弟要钱都要往上打报告，大哥再给他们发（取不同节点数据中间需要再推理）



# ncnn源码学习（三）：数据处理

## 【正文】

这次我们先来看一段ncnn应用代码中，除了推理外最重要的一部分代码，数据处理：

```text
ncnn::Mat in = ncnn::Mat::from_pixels_resize(image.data, ncnn::Mat::PIXEL_BGR, image.cols, image.rows, 227, 227);
in.substract_mean_normalize(mean_vals, norm_vals);
```

这一部分的代码有两块组成

- from_pixels_resize：将cv::Mat数据转换到ncnn::Mat同时进行resize操作
- substract_mean_normalize：这个就是减均值除方差

## 【from_pixels_resize】

先看名字，from_pixels_resize由两部分组成：

1. from_pixels：从unsigned char*的数组转换到ncnn::Mat
2. resize：unsigned char*的数组下进行resize

源码中是先进行resize再进行from_pixels的。

### A. resize

这个代码支持三种图像类型：单通道的GRAY、三通道的RGB和BGR、四通道的RGBA。源码使用的都是bilinear插值，这里我们挑个简单的单通道GRAY的来看看，函数名字很直观，就叫做resize_bilinear_c1，后面的c1就是chennel 1的意思。具体的代码在mat_pixel.cpp的第1414行，这个我就不细说了，大家可以去看这个文章：

[TNN MatConverter Resizeblog.csdn.net/yiran103/article/details/116566779![img](https://pic1.zhimg.com/v2-2a5027b5bff83f50a189c6146b4f7548_ipico.jpg)](https://blog.csdn.net/yiran103/article/details/116566779)

这个虽然写的是TNN的，但仔细看下来会发现其实跟NCNN的实现是一样的（变量名也一样）。

这个大体流程就是：

1. 先算x、y方向上插值点的位置索引xofs和yofs
2. 再算x、y方向上插值点左右的两个插值系数ialpha和ibeta
3. 遍历插值，x方向上的插值用xofs和ialpha得到，y方向上的插值用yofs和ibeta得到

这个计算的细节还是很多的，大家感兴趣的可以去仔细研究一下，我这里就不细写了。ncnn的代码为了效率，可能写的不是特别直观。

### B. from_pixels

这个就很简单了，就是开辟一块ncnn::Mat的内存，然后遍历数组一个一个填进去就好了，同样的这里支持三通道、三通道、四通道，而且一些颜色转换RGB2BGR、RGB2GRAY这些都是在这里实现支持的。我们挑一个典型的RGB2GRAY的实现来看，源码在mat_pixel.cpp的第539行，函数名就是from_rgb2gray：

```cpp
static Mat from_rgb2gray(const unsigned char* rgb, int w, int h)
{
    const unsigned char Y_shift = 8;//14
    const unsigned char R2Y = 77;
    const unsigned char G2Y = 150;
    const unsigned char B2Y = 29;

    Mat m(w, h, 1);
    if (m.empty())
        return m;

    float* ptr = m;

    int size = w * h;
    int remain = size;

    for (; remain > 0; remain--)
    {
        *ptr = (rgb[0] * R2Y + rgb[1] * G2Y + rgb[2] * B2Y) >> Y_shift;

        rgb += 3;
        ptr++;
    }

    return m;
}
```

这个代码很直观，前面就是定义了转换时R、G、B对应要乘的系数，这里作者用的是整数乘法，所以系数放大了 ![[公式]](https://www.zhihu.com/equation?tex=2%5E8) ，后面算结果那里要右移回去。后面就是一个暴力for循环，全部遍历把数据塞进去ncnn::Mat就完了。但这里我还想放一下GRAY2RGB的代码，看下很值得注意的细节：

```cpp
static Mat from_gray2rgb(const unsigned char* gray, int w, int h)
{
    Mat m(w, h, 3);
    if (m.empty())
        return m;

    float* ptr0 = m.channel(0);
    float* ptr1 = m.channel(1);
    float* ptr2 = m.channel(2);

    int size = w * h;

    int remain = size;

    for (; remain>0; remain--)
    {
        *ptr0 = *gray;
        *ptr1 = *gray;
        *ptr2 = *gray;

        gray++;
        ptr0++;
        ptr1++;
        ptr2++;
    }

    return m;
}
```

从这个可以看出来，获取ncnn::Mat的三个通道的数据，是要用channel方法索引出来的，这里就是一个需要留意的点，ncnn::Mat的数据存储，channel间的需要对齐，不一定是连续的，也就是不要理所当然的用channel(0)的指针，自己加加加想去访问其他channel的数据，很容易翻车（我就因为这个翻车过），这个我们后面有时间可以好好写一写ncnn的数据排布。

## 【substract_mean_normalize】

substract_mean_normalize的源码在mat.cpp的第25行，这个代码是支持只mean不norm，只norm不mean，mean和norm都做得，由于这些都大同小异，我就直接贴都做mean和norm的代码了：

```cpp
void Mat::substract_mean_normalize(const float* mean_vals, const float* norm_vals)
{
    int size = w * h;

    for (int q = 0; q < c; q++)
    {
        float* ptr = data + cstep * q;
        const float mean = mean_vals[q];
        const float norm = norm_vals[q];

        int remain = size;

        for (; remain > 0; remain--)
        {
            *ptr = (*ptr - mean) * norm;
            ptr++;
        }
    }
}
```

上面比较核心的就是一句：

```cpp
*ptr = (*ptr - mean) * norm;
```

就是遍历Mat的所有数据，给他减mean乘norm，要注意这里是乘norm，不是一般说的除方差，方差的倒数才是这里的norm。

# ncnn源码学习（四）：模型推理

## 【正文】

这次我们接着看一段ncnn代码中，最重要的部分：

```cpp
ncnn::Extractor ex = squeezenet.create_extractor();
ex.input("data", in);
ncnn::Mat out;
ex.extract("prob", out);
```

这段代码中有三个比较关键的东西：

1. ncnn::Extractor或者说是create_extractor，这个其实就是一个专门用来维护推理过程数据的一个类，跟ncnn::Net解耦开，不糊弄到一块而已，这个最主要的就是开辟了一个大小为网络的blob size的std::vector<ncnn::Mat>来维护计算中间的数据
2. input，这个更简单，就是在上一步开辟的vector中，把该input的blob的数据in塞进去
3. extract，这个东西就比较核心了，我们这回主要看这个

## 【extract】

我们先看extract的源代码，在net.cpp的第733行：

```cpp
int Extractor::extract(const char* blob_name, Mat& feat)
{
    int blob_index = net->find_blob_index_by_name(blob_name);
    if (blob_index == -1)
        return -1;

    int ret = 0;

    if (blob_mats[blob_index].dims == 0)
    {
        int layer_index = net->blobs[blob_index].producer;
        ret = net->forward_layer(layer_index, blob_mats, lightmode);
    }

    feat = blob_mats[blob_index];

    return ret;
}
```

- find_blob_index_by_name是查找输入的blob名字在vector中的下标，没什么神奇的

- 判断blob_mats[blob_index].dims ==0值得一说

- - 如果这个blob没有计算过，那么该blob对应的数据应该是空的，说明要进行推理
  - 如果blob计算过，就有数据了，那么dims就不会等于0，说明数据应该有了，不用再算了，直接取得就完事了

这里面最重要的就是net->forward_layer，其实兜兜转转又回到了ncnn::Net，ncnn::Extractor也可以从代码中看出来主要就是维护数据而已。

## 【forward_layer】

forward_layer的源码在net.cpp的第529行，代码有点长，我就贴个函数的定义好了：

```cpp
int Net::forward_layer(int layer_index, std::vector<Mat>& blob_mats, bool lightmode)
```

有三个参，第一个layer_index就是说我要计算哪一层，blob_mats就是记录计算中的数据的，lightmode这个从代码上来看是配合inplace来做一些动态的release，及时释放内存资源啥的。

ncnn这个代码还挺有意思的，推理还分了layer是不是“一个输入一个输出”和“其它”。这里一个输入一个输出的代码比较简单，我们先看这个，我把其中一些不重要的都删掉了：

```cpp
// 1. 获取当前层
const Layer* layer = layers[layer_index];
// 2. 获取当前层的“大哥”和“小弟”
int bottom_blob_index = layer->bottoms[0];
int top_blob_index = layer->tops[0];
// 3. “大哥”还没吃饭得大哥先吃
if (blob_mats[bottom_blob_index].dims == 0)
{
    int ret = forward_layer(blobs[bottom_blob_index].producer, blob_mats, lightmode);
    if (ret != 0)
        return ret;
}
// 4. 开饭了！
Mat bottom_blob = blob_mats[bottom_blob_index];
Mat top_blob;
int ret = layer->forward(bottom_blob, top_blob);
if (ret != 0)
    return ret;
// 5. 给“小弟”也喂口饭
blob_mats[top_blob_index] = top_blob;
```

从上面的代码还是我的注释，就很好理解的，就是一个递归，当前层需要的输入blob还没算，就递归进去算，算完就算自己，这里layer->forward是每一层的专属实现，这个我们后面单独开几个文章讲一下一些典型的layer。

然后我们再看非“一个输入一个输出”的复杂一点的：

```cpp
const Layer* layer = layers[layer_index];
// 1. 头上的每一位“大哥”都要先吃饭
std::vector<Mat> bottom_blobs;
bottom_blobs.resize(layer->bottoms.size());
for (size_t i=0; i<layer->bottoms.size(); i++)
{
    int bottom_blob_index = layer->bottoms[i];
    if (blob_mats[bottom_blob_index].dims == 0)
    {
        int ret = forward_layer(blobs[bottom_blob_index].producer, blob_mats, lightmode);
        if (ret != 0)
            return ret;
    }
    bottom_blobs[i] = blob_mats[bottom_blob_index];
}
// 2. “大哥”们吃完饭就到自己吃
std::vector<Mat> top_blobs;
top_blobs.resize(layer->tops.size());
int ret = layer->forward(bottom_blobs, top_blobs);
if (ret != 0)
    return ret;
// 3. 给所有“小弟”都喂口饭
for (size_t i=0; i<layer->tops.size(); i++)
{
    int top_blob_index = layer->tops[i];
    blob_mats[top_blob_index] = top_blobs[i];
}
```

从上面的代码和我的注释也很容易看出来，多输入多输出，其实也没什么特别的，就是输入的所有节点都要先算完，然后再算自己，最后给底下节点塞数据。

## 【后话】

至此，ncnn的推理过程，可以说我们已经囫囵吞枣的看完了，ncnn推理的整体思路也已经出来了，我这里简单梳理一下：

1. 读取param和bin文件，记录下每一层的layer、layer的输入输出节点、layer的特定参数

2. 推理

3. 1. 维护一个列表用于存所有节点的数据

   2. 给输入节点放入输入数据

   3. 计算输出节点的layer

   4. 1. 计算layer所需的输入节点还没给输入——>递归调用上一层layer计算
      2. 有输入了——>计算当前layer
      3. 输出结果

后面我的计划是调几个典型的layer来看一下它们的init，forward的一些实现，例如卷积、池化、激活。然后再看一下ncnn::Mat的内存管理。最后就是尝试自己实现一个tiny-ncnn。

# ncnn源码学习（五）：relu算子

## 【正文】

首先我们先来看一个网络中，最简单的relu算子，relu也是一个无参算子。ncnn源码中，relu算子是在relu.h和relu.cpp这两个文件里面。这里要简单说明一下，由于relu比较简单，而leaky relu跟relu相比只是在小于0的地方乘了一个系数，所以作者就将relu和leaky relu写到一块，由一个只作用于小于0区域的斜率slope控制。

relu这个算子算上初始化一共有6个方法：

1. ReLU，最重要的是声明了该layer是one_blob_only的，也就是上一篇文章提到的单输入单输出的算子
2. load_param(FILE* paramfp)，从文件指针加载参数
3. load_param_bin(FILE* paramfp)，从16进制文件加载参数
4. load_param(const unsigned char*& mem)，从数组加载参数
5. forward，layer推理
6. forward_inplace，inplace的layer推理

这堆里面，我们主要看最基本的load_param(FILE* paramfp)和forward。

## 【load_param】

如前面文章所说，ncnn的模型由网络结构param文件和网络参数bin文件组成，由于relu的参比较简单，就是一个float数slope，所这个参是放到了param文件的，就是前面文章提到的，layer特定参数的位置，如下所示：

```text
ReLU             relu_conv1       1 1 conv1 conv1_relu_conv1 0.000000
```

上面是我从param中截取出来的一段，可以看到它后面的slope是0，说明他是个relu。我们看下源码是怎么写的：

```cpp
int ReLU::load_param(FILE* paramfp)
{
    int nscan = fscanf(paramfp, "%f", &slope);
    if (nscan != 1)
    {
        fprintf(stderr, "ReLU load_param failed %d\n", nscan);
        return -1;
    }

    return 0;
}
```

可以看到，很简单，就是用fscanf从文件里面读了一个float数出来。

## 【forward】

由于支持slope，而作者将relu和带slope的leaky relu用if分开写了，relu在小于直接给0，leaky relu在小于0是*slope，这里我就只贴leaky relu部分的代码，relu的可以脑部一下赋值为0就好了：

```cpp
int ReLU::forward(const Mat& bottom_blob, Mat& top_blob) const
{
    int w = bottom_blob.w;
    int h = bottom_blob.h;
    int channels = bottom_blob.c;
    int size = w * h;

    top_blob.create(w, h, channels);
    if (top_blob.empty())
        return -100;

    for (int q=0; q<channels; q++)
    {
        const float* ptr = bottom_blob.channel(q);
        float* outptr = top_blob.channel(q);

        for (int i=0; i<size; i++)
        {
            if (ptr[i] < 0)
                outptr[i] = ptr[i] * slope;
            else
                outptr[i] = ptr[i];
        }
    }

    return 0;
}
```

可以看到，代码其实很简单，就是开辟一个跟输入大小一样的ncnn::Mat，然后逐个通道，逐个位置的元素，按照公式的定义算。（PS：其实nihui最初的版本forward这里写bug了，forward_inplace就没有问题，后面可能考虑到relu这个东西没必要这么伺候，非inplace的forward也从源码里面去掉了）

# ncnn源码学习（六）：pooling算子

## 【正文】

这次我们来看同样是很重要的pooling池化算子，同样的，pooling和relu一样，也是一个无权重的算子，只有几个配置参数而已。pooling源码在pooling.h和pooling.cpp这两个文件里面，与relu类似，同样实现了非常多的方法，这里我们只看两个最重要的：load_param(FILE* paramfp)和forward。

## 【load_param】

首先我们来看一个真实的param文件里面，pooling层是怎么样的：

```text
Pooling          pool1            1 1 conv1_relu_conv1 pool1 0 3 2 0 0
```

后面那5个layer特定参数“0 3 2 0 0”分别代表着：pooling_type、kernel_size、stride、pad、global_pooling。我们再来看具体的加载源码：

```cpp
int Pooling::load_param(FILE* paramfp)
{
    int nscan = fscanf(paramfp, "%d %d %d %d %d", &pooling_type, &kernel_size, &stride, &pad, &global_pooling);
    if (nscan != 5)
    {
        fprintf(stderr, "Pooling load_param failed %d\n", nscan);
        return -1;
    }

    return 0;
}
```

这个比较简单，就是从文件里面fscanf得到pooling层特有的五个参数。

## 【forward】

在forwar的代码里面，通过pooling_type和global_pooling这两个变量的控制，一共组合了四种实现：

- 非全局池化：最大值池化、平均值池化
- 全局池化：最大值池化、平均值池化

考虑到实现都是大同小异的，我就挑全局最大值池化和非全局平均值池化这两个具体写一写，剩下的大家类比一下就好了。

### A. 全局最大值池化

我们先来看源码：

```cpp
int Pooling::forward(const Mat& bottom_blob, Mat& top_blob) const
{
    int w = bottom_blob.w;
    int h = bottom_blob.h;
    int channels = bottom_blob.c;

    top_blob.create(1, 1, channels);
    if (top_blob.empty())
        return -100;

    int size = w * h;
    for (int q=0; q<channels; q++)
    {
        const float* ptr = bottom_blob.channel(q);
        float* outptr = top_blob.channel(q);

        float max = ptr[0];
        for (int i=0; i<size; i++)
        {
            max = std::max(max, ptr[i]);
        }
        outptr[0] = max;
    }
    return 0;
}
```

全局最大值池化很简单，就是将原本的(c,h,w)的数据，计算每个chennel的最大值，把数据做成(c,1,1)，这个很简单，看看代码就好了。

### B. 非全局平均值池化

这个池化更具有一般性，先看源码：

```cpp
int Pooling::forward(const Mat& bottom_blob, Mat& top_blob) const
{
    int w = bottom_blob.w;
    int h = bottom_blob.h;
    int channels = bottom_blob.c;
    // 按照用户需要的pad进行padding
    Mat bottom_blob_bordered = bottom_blob;
    if (pad > 0)
    {
        copy_make_border(bottom_blob, bottom_blob_bordered, pad, pad, pad, pad, BORDER_CONSTANT, 0.f);
        if (bottom_blob_bordered.empty())
            return -100;

        w = bottom_blob_bordered.w;
        h = bottom_blob_bordered.h;
    }

    int outw = (w - kernel_size) / stride + 1;
    int outh = (h - kernel_size) / stride + 1;
    // size跟stride不匹配的话也要padding一下
    int wtail = (w - kernel_size) % stride;
    int htail = (h - kernel_size) % stride;
    if (wtail != 0 || htail != 0)
    {
        int wtailpad = 0;
        int htailpad = 0;
        if (wtail != 0)
            wtailpad = kernel_size - wtail;
        if (htail != 0)
            htailpad = kernel_size - htail;

        Mat bottom_blob_bordered2;
        copy_make_border(bottom_blob_bordered, bottom_blob_bordered2, 0, htailpad, 0, wtailpad, BORDER_REPLICATE, 0.f);
        if (bottom_blob_bordered2.empty())
            return -100;

        bottom_blob_bordered = bottom_blob_bordered2;

        w = bottom_blob_bordered.w;
        h = bottom_blob_bordered.h;

        if (wtail != 0)
            outw += 1;
        if (htail != 0)
            outh += 1;
    }
    // 创建存结果的ncnn::Mat
    top_blob.create(outw, outh, channels);
    if (top_blob.empty())
        return -100;
    // 计算滑窗内各元素的下标偏移量，主要用于加速索引
    const int maxk = kernel_size * kernel_size;
    std::vector<int> _space_ofs(maxk);
    int* space_ofs = &_space_ofs[0];
    {
        int p1 = 0;
        int p2 = 0;
        int gap = w - kernel_size;
        for (int i = 0; i < kernel_size; i++)
        {
            for (int j = 0; j < kernel_size; j++)
            {
                space_ofs[p1] = p2;
                p1++;
                p2++;
            }
            p2 += gap;
        }
    }
    // 正式的pooling计算
    for (int q=0; q<channels; q++)
    {
        const Mat m(w, h, bottom_blob_bordered.channel(q));
        float* outptr = top_blob.channel(q);

        for (int i = 0; i < outh; i++)
        {
            for (int j = 0; j < outw; j++)
            {
                const float* sptr = m.data + m.w * i*stride + j*stride;

                float sum = 0;
                for (int k = 0; k < maxk; k++)
                {
                    float val = sptr[ space_ofs[k] ];
                    sum += val;
                }
                outptr[j] = sum / maxk;
            }
            outptr += outw;
        }
    }

    return 0;
}
```

从上面的代码和注释可以看到，这个过程分四部：padding用户的指定值->padding到能计算的size->计算滑窗下标偏移量->正式计算，这个稍微看一下就很好理解。不过代码虽然看起来很简单， 但其实细节还是很多的，当然，如果不自己动手做的话，是很难发现的。

# ncnn源码学习（七）：convolution算子

## 【正文】

有了前六个文章的铺垫，终于轮到一个网络中，最最最重要而卷积算子部分。卷积的源码位于convolution.h和convolution.cpp这两个文件。卷积与前面看的relu和pooling不同，他是有权重的layer，因此我们这次要看三个方法：load_param(FILE* paramfp)、load_model(FILE* binfp)以及forward(const Mat& bottom_blob, Mat& top_blob)。

## 【load_param】

看过前面两个算子文章的都懂，我们先上真实的param的卷积是怎么写的：

```text
Convolution      conv1            1 1 data conv1 64 3 1 2 0 1 1728
```

后面的7个layer特定参数分别是：num_output、kernel_size、dilation、stride、pad、bias_term、weight_data_size。我们直接上源码：

```cpp
int Convolution::load_param(FILE* paramfp)
{
    int nscan = fscanf(paramfp, "%d %d %d %d %d %d %d", &num_output, &kernel_size, &dilation, &stride, &pad, &bias_term, &weight_data_size);
    if (nscan != 7)
    {
        fprintf(stderr, "Convolution load_param failed %d\n", nscan);
        return -1;
    }

    return 0;
}
```

这个很简单，一眼过，就是fscanf一下7个参数。

## 【load_model】

这个就跟前面两个无权重的算子不一样，卷积是有权重，ncnn中的权重数据都存到了.bin文件，直接看源码：

```cpp
int Convolution::load_model(FILE* binfp)
{
    int nread;
    // 读取权重的一些基本信息
    union
    {
        struct
        {
            unsigned char f0;
            unsigned char f1;
            unsigned char f2;
            unsigned char f3;
        };
        unsigned int tag;
    } flag_struct;
    nread = fread(&flag_struct, sizeof(flag_struct), 1, binfp);
    if (nread != 1)
    {
        fprintf(stderr, "Convolution read flag_struct failed %d\n", nread);
        return -1;
    }
    unsigned int flag = flag_struct.f0 + flag_struct.f1 + flag_struct.f2 + flag_struct.f3;

    weight_data.create(weight_data_size);
    if (weight_data.empty())
        return -100;

    if (flag_struct.tag == 0x01306B47) // float16的权重读取，跳过
    {
        // ......
    }
    else if (flag != 0) // 量化数据的权重读取，跳过
    {
        // ......
    }
    else if (flag_struct.f0 == 0) // 最原始的float32的读取
    {
        // raw weight data
        nread = fread(weight_data, weight_data_size * sizeof(float), 1, binfp);
        if (nread != 1)
        {
            fprintf(stderr, "Convolution read weight_data failed %d\n", nread);
            return -1;
        }
    }

    // 有bias项的话也要读bias
    if (bias_term)
    {
        bias_data.create(num_output);
        if (bias_data.empty())
            return -100;
        nread = fread(bias_data, num_output * sizeof(float), 1, binfp);
        if (nread != 1)
        {
            fprintf(stderr, "Convolution read bias_data failed %d\n", nread);
            return -1;
        }
    }

    return 0;
}
```

从上面的代码可以看出来，其实读取并不神秘。比较特别的是读取了一个结构体flag_struct，这个结构体包含了一些权重的信息，例如是不是float16数据，是不是量化了的数据，这些我都注释掉了，我们就看最简单最原始的float32的。只看float32的话，其实代码也很简单，就是按照从param里面读到的权重size和bias size，直接从文件里面读指定长度数据就完了。

## 【forward】

以防大家不了解conv2d是怎么算的，特地找了pytorch上的解释：

[https://pytorch.org/docs/master/generated/torch.nn.Conv2d.html#torch.nn.Conv2dpytorch.org/docs/master/generated/torch.nn.Conv2d.html#torch.nn.Conv2d](https://pytorch.org/docs/master/generated/torch.nn.Conv2d.html#torch.nn.Conv2d)

光看公式没用，我们看ncnn的源码：

```cpp
int Convolution::forward(const Mat& bottom_blob, Mat& top_blob) const
{
    int w = bottom_blob.w;
    int h = bottom_blob.h;
    int channels = bottom_blob.c;

    // padding
    Mat bottom_blob_bordered = bottom_blob;
    if (pad > 0)
    {
        copy_make_border(bottom_blob, bottom_blob_bordered, pad, pad, pad, pad, BORDER_CONSTANT, 0.f);
        if (bottom_blob_bordered.empty())
            return -100;

        w = bottom_blob_bordered.w;
        h = bottom_blob_bordered.h;
    }

    const int kernel_extent = dilation * (kernel_size - 1) + 1;

    int outw = (w - kernel_extent) / stride + 1;
    int outh = (h - kernel_extent) / stride + 1;

    top_blob.create(outw, outh, num_output);
    if (top_blob.empty())
        return -100;

    // 计算kernel各元素的下标偏移量
    const int maxk = kernel_size * kernel_size;
    std::vector<int> _space_ofs(maxk);
    int* space_ofs = &_space_ofs[0];
    {
        int p1 = 0;
        int p2 = 0;
        int gap = w * dilation - kernel_extent;
        for (int i = 0; i < kernel_size; i++)
        {
            for (int j = 0; j < kernel_size; j++)
            {
                space_ofs[p1] = p2;
                p1++;
                p2 += dilation;
            }
            p2 += gap;
        }
    }

    // 卷积计算
    const float* weight_data_ptr = weight_data;
    for (int p=0; p<num_output; p++)
    {
        float* outptr = top_blob.channel(p);

        for (int i = 0; i < outh; i++)
        {
            for (int j = 0; j < outw; j++)
            {
                float sum = 0.f;

                if (bias_term)
                    sum = bias_data.data[p]; // 加bias

                const float* kptr = weight_data_ptr + maxk * channels * p;

                // channels
                for (int q=0; q<channels; q++)
                {
                    const Mat m = bottom_blob_bordered.channel(q);
                    const float* sptr = m.data + m.w * i*stride + j*stride;

                    for (int k = 0; k < maxk; k++)
                    {
                        float val = sptr[ space_ofs[k] ];
                        float w = kptr[k];
                        sum += val * w; // kernel和feature的乘加操作
                    }

                    kptr += maxk;
                }

                outptr[j] = sum;
            }

            outptr += outw;
        }
    }

    return 0;
}
```

上面这个代码要是不懂卷积具体怎么算的可能不太好懂，可以去看我以前写的一文章：

[嘻嘻嘻：【OpenCL学习记录】用循环简单实现卷积、池化、激活、全连接、批归一化来跑一下LeNet的推理1 赞同 · 0 评论文章![img](https://pic1.zhimg.com/v2-66c9903cee09724be386c24319136bec_180x120.jpg)](https://zhuanlan.zhihu.com/p/393636855)

# ncnn源码学习（八）：split与concat算子

## 【正文】

我们前面已经讨论了一个网络中，必不可少的relu、pooling、convolution算子了，但如同我前面所说的，他们都是“一个输入一个输出”的layer，因此我这里再写一个split与concat算子的文章，split与concat的存在能让网络实现分支的功能，同时split是典型的“单输入多输出”layer，concat则是“多输入单输出”layer。可以很好的类比一下。这两个算子分别在split.h，split.cpp和concat.h，concat.cpp文件里面。在ncnn的实现中，split和concat都是无参数无权重的，也就是说不需要从param读取layer特定参数，不需要从bin读取权重参数。

## 【split】

我们首先看一下，param里面，split这个layer是怎么样的：

```text
Split            splitncnn_0      1 2 relu_squeeze1x1 relu_squeeze1x1_splitncnn_0 relu_squeeze1x1_splitncnn_1
```

从这个写法可以看出来，其实split的参数都已经包含在输入输出blob信息里面了，一个输入两个输出。由于split是无参无权重的，我们直接来看forward的实现：

```cpp
int Split::forward(const std::vector<Mat>& bottom_blobs, std::vector<Mat>& top_blobs) const
{
    const Mat& bottom_blob = bottom_blobs[0];
    for (size_t i=0; i<top_blobs.size(); i++)
    {
        top_blobs[i] = bottom_blob;
    }

    return 0;
}
```

代码非常简洁，就是给输出blob每一个都拷贝上输入blob的值。从函数的入参可以看到，“单输入单输出的时候”入参是ncnn::Mat，而非“单输入单输出”，入参就是vector了。（PS：其实就是继承Layer类选择实现哪个的问题而已）

## 【concat】

看完优雅的split，我们再看concat，先看concat里面的写法：

```text
Concat           fire9/concat     2 1 relu_expand1x1 relu_expand3x3 fire9/concat
```

这个看起来就像是split的镜像一样，我也就不多说了，直接看源码吧：

```cpp
int Concat::forward(const std::vector<Mat>& bottom_blobs, std::vector<Mat>& top_blobs) const
{
    int w = bottom_blobs[0].w;
    int h = bottom_blobs[0].h;

    // total channels
    int top_channels = 0;
    for (size_t b=0; b<bottom_blobs.size(); b++)
    {
        const Mat& bottom_blob = bottom_blobs[b];
        top_channels += bottom_blob.c;
    }

    Mat& top_blob = top_blobs[0];
    top_blob.create(w, h, top_channels);
    if (top_blob.empty())
        return -100;

    int q = 0;
    for (size_t b=0; b<bottom_blobs.size(); b++)
    {
        const Mat& bottom_blob = bottom_blobs[b];

        int channels = bottom_blob.c;
        int size = bottom_blob.cstep * channels;

        const float* ptr = bottom_blob;
        float* outptr = top_blob.channel(q);
        for (int i=0; i<size; i++)
        {
            outptr[i] = ptr[i];
        }

        q += channels;
    }

    return 0;
}
```

从源码，可能大家就能发现一个我没说但大家可能会有点疑问的东西，那就是为什么concat不要参数？答案就是ncnn默认了从channel维度做concat。代码我也不多说了，一看就懂。

## 【后话】

到这篇文章，ncnn的架构和一些常见算子我们就都看完了，到这里除去ncnn::Mat的设计，自己写一个tiny-ncnn应该是问题不大的，再对照各算子的公式，写一个老年慢速版的算子实现应该是问题不大了。

# ncnn源码学习（九）：ncnn::Mat类

## 【正文】

如无意外的话呢，这个文章应该就是ncnn源码学习系列的最后一个文章了。这个文章，我们来看一个，在前面文章的源码中反复出现，但我又一直没提到的东西，那就是ncnn::Mat的内存是怎么设计的。

这次我们就只看一个东西，那就是ncnn::Mat类的一个很重要的方法create，从create这个方法，我们能窥探到很多Mat设计的巧妙与精髓之处。我们分析的create方法位于mat.h文件的第426行，入参是宽、高、通道数的三个int，功能就是开辟一个大小为(c,h,w)的内存空间。

## 【create】

直接看源码：

```cpp
inline void Mat::create(int _w, int _h, int _c)
{
    // 第一部分
    release();

    // 第二部分
    dims = 3;
    w = _w;
    h = _h;
    c = _c;
    cstep = alignSize(w * h * sizeof(float), 16) >> 2;

    // 第三部分
    if (total() > 0)
    {
        size_t totalsize = total() * sizeof(float);
        data = (float*)fastMalloc(totalsize + (int)sizeof(*refcount));
        refcount = (int*)(((unsigned char*)data) + totalsize);
        *refcount = 1;
    }
}
```

这个代码我们乍一看，可以分成三部分：第一部分的释放，因为这个Mat他可能原本不是空的，是有数据的，所以要先释放掉，不然就泄漏了；第二部分的基本配置参数，这里有一个比较特别的东西，cstep，这个我们后面再说；第三部分就是具体的malloc开辟空间了。整体下来很简单，接下来我们对里面的一些细节，仔细分析分析。

## 【alignSize】

```cpp
static inline size_t alignSize(size_t sz, int n)
{
    return (sz + n-1) & -n;
}
```

这个代码很简单，给定想要开辟的内存大小sz和对齐大小n，这个函数可以返回一个数，这个数是≥sz且能被n整除的最小的数，其实就是sz大小要做n大小的对齐，应该要实际开辟多大的问题而已。

## 【fastMalloc】

```cpp
#define MALLOC_ALIGN    16

template<typename _Tp> static inline _Tp* alignPtr(_Tp* ptr, int n=(int)sizeof(_Tp))
{
    return (_Tp*)(((size_t)ptr + n-1) & -n);
}

static inline void* fastMalloc(size_t size)
{
    unsigned char* udata = (unsigned char*)malloc(size + sizeof(void*) + MALLOC_ALIGN);
    if (!udata)
        return 0;
    unsigned char** adata = alignPtr((unsigned char**)udata + 1, MALLOC_ALIGN);
    adata[-1] = udata;
    return adata;
}
```

可以看到，这里面的alignPtr跟前面的alignSize很像，从名字也可以看出来，alignSize做的是开辟空间“长度”的对齐，alignPtr是开辟空间“指针”的对齐。fastMalloc的功能就是开辟一个指定长度+对齐长度的空间，然后对分配到的空间的头指针进行对齐。

## 【再看create】

看完前面两个create的重要组成部分之后，我们回顾一下create：

```cpp
inline void Mat::create(int _w, int _h, int _c)
{
    // 第一部分
    release();

    // 第二部分
    dims = 3;
    w = _w;
    h = _h;
    c = _c;
    cstep = alignSize(w * h * sizeof(float), 16) >> 2;

    // 第三部分
    if (total() > 0)
    {
        size_t totalsize = total() * sizeof(float);
        data = (float*)fastMalloc(totalsize + (int)sizeof(*refcount));
        refcount = (int*)(((unsigned char*)data) + totalsize);
        *refcount = 1;
    }
}
```

首先就是释放，然后计算要开辟空间的对齐长度，然后就是开辟一个指针也对齐了的空间，一眼就看完了。这里面有个refcount，这个我理解应该是用来统计当前Mat被引用了多少次的，这个可以不用理会。

这里还有一点要说一下，就是cstep的对齐计算，只用了w和h，所以很显然，结合名字我们就能知道它是一个chennel的对齐大小，因为对齐过，所以cstep不一定等于w*h，所以想用一个指针，直接通过w和h乘加，索引遍历整个数据，是不行的，因为跨channel之间由于cstep的对齐，它们中间会有空位置是没用的。

# ncnn源码学习（十）：ncnn转pytorch

[![嘻嘻嘻](https://pica.zhimg.com/v2-41bc128494fd66543a82b783b6d4a609_xs.jpg?source=172ae18b)](https://www.zhihu.com/people/edvince)

[嘻嘻嘻](https://www.zhihu.com/people/edvince)

## 【前言】

源码学习九的那个文章我说不再更新源码学习，但这回还是更新十了，但这回也可以说不是源码学习，正如题目所示，这次我们来尝试做一个ncnn模型转pytorch模型！

## 正文

如果看过前面九个文章的话，基本上对ncnn已经有一个比较清晰的认知了。前面的文章，都是基于ncnn最早的2017年的一版代码写的，那个比较干净，东西少，但是这回我们要做ncnn2pytorch，再用太老的就没啥意思了，所以这回我用的ncnn的20210720的这一版代码开展的工作。源码我放在github了：

[https://github.com/EdVince/ncnn2pytorchgithub.com/EdVince/ncnn2pytorch](https://github.com/EdVince/ncnn2pytorch)

### 【项目分析】

1. ncnn模型由param和bin文件构成，pytorch由一个继承nn.Module的类构成
2. ncnn的param的每一行的layer以及其参数，可以构建pytorch模型的__init__部分，把所有layer定下来
3. ncnn的param的每一行layer的输入和输出blob关系可以确定forward时候数据和层的先后顺序
4. ncnn的bin可以为确定的pytorch的模型每一个带权重layer初始化权值

### 【ncnn部分(网络抽取)】

1. 由于目前param文件写的比较复杂，所以我们不直接从param文件解析，我们使用ncnn的load_param和load_model先把模型加载了，让ncnn帮我们处理好模型解析。我们再从ncnn::Net的layers()里面，把每一层layer的信息拿出来，把layer的基本信息和特定的参数保存到txt文件里面去
2. layer的参数好拿，但是网络图的确定是比较难的，这里我们通过给ncnn的所有输入节点给定dump输入，然后extract所有的输出节点，让ncnn跑一遍前向推理过程，我们通过在推理代码里面插入“钩子”，把模型前向的各layer顺序抓出来，同样保存到txt文件
3. 有权重的layer基本都是少数，都是卷积一类的，这个也抓出来写到bin文件里面就好了

### 【pytorch部分(网络生成)】

1. init部分，python上解析每一个layer的txt文件夹，直接把对应的layer创建代码拼出来就行了
2. forward部分，解析保存了layer运行顺序的txt文件，按照顺序在forward中把数据流和layer之间计算的关系拼出来
3. 拼出来网络后，直接写进去py文件，然后再动态加载进来
4. 解析每一个保存了权重的bin文件，对照着塞回去模型
5. 保存一下pytorch文件，缓存住权重

### 【问题分析】

1. ncnn算子比较多，保存layer基本信息和特有参数的时，要每一个算子都写一个保存代码
2. ncnn兼容性太好，有些算子反倒是pytorch不支持了，这个无解了，要是硬写的话，pytorch的网络会很不优雅
3. ncnn算子功能性较强，每一个算子，python上都要写一个跟pytorch算子的桥接解析代码，例如create_layer这类东西

### 【现有问题】

1. 由于我并没有真正的ncnn2pytorch的需求，这是只是写来玩玩，给大家一个参考而已，所以代码功能一点都不丰富，所以也可以叫做（穷版）ncnn2pytorch
2. 目前我只成功转化呢ncnn自带demo的squeezenet_v1.1模型，同样的也只写了这个模型用到的算子的处理，要是套有其它算子的模型进去，需要自己额外实现这些算子的处理
3. 转换过程中，ncnn的padding十分功能强大，支持了pytorch、tensorflow、onnx的四五种padding方法，转到pytorch就傻眼了，很不好转，目前转换模型推理精度有一点问题，估计padding导致的

### 【结果】

下图是原始demo程序squeezenet_v1.1模型的运行结果：

![img](https://pic2.zhimg.com/80/v2-9c2ea78ae6f9698df53f9a6578b5d951_1440w.png)

下图是使用转换模型(ncnn->pytorch->onnx->ncnn)的运行结果，可以看到这个分类的趋势是对的，但是数值上还差那么一点，这个有缘再说：

![img](https://pic2.zhimg.com/80/v2-e1607900ef5a1bdecec6170bc9b849cd_1440w.png)

### 【讨论】

ncnn2pytorch的意义在哪？

1. 将现有的ncnn模型，通过ncnn->pyorch->onnx转到onnx平台，就可以转到目前支持onnx的任意框架了
2. 将现有的ncnn模型，转到pyotrch之后，在自己的数据集上进行微调以适应任务
3. ncnn2pytorch，个人认为会在需求及其旺盛detect任务上有比较大的需求，第二个需求就是白嫖模型