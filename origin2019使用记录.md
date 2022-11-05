# 1、添加列数据的列标签属性

![image-20220521114616607](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220521114616607.png)





可以找到，双击竖排列，选项中有一个绘图设定，里面有标签

![image-20220521114714504](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220521114714504.png)

![image-20220521114747855](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220521114747855.png)





# 2、添加列属性标签设置按钮

![image-20220522112820374](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220522112820374.png)







# 3、数据等间隔采样并画出轨迹



1、首先打开我们需要提取数据的图谱。点击菜单栏中“Analysis”下面的“mathematics”，在下拉菜单中点击“Interpolate/Extrapolate”，选择后面的Open Dialog。

[![img](https://iknow-pic.cdn.bcebos.com/78310a55b319ebc46db2577b8c26cffc1f17160d?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_600%2Ch_800%2Climit_1%2Fquality%2Cq_85%2Fformat%2Cf_auto)](https://iknow-pic.cdn.bcebos.com/78310a55b319ebc46db2577b8c26cffc1f17160d)

2、这时候弹出一个新的对话框，在“Number of Points”后面，输入的数据代表我们提取了多少个数据，我们可以根据需要进行提取。

[![img](https://iknow-pic.cdn.bcebos.com/ac6eddc451da81cb8e45a22f5c66d016082431b4?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_600%2Ch_800%2Climit_1%2Fquality%2Cq_85%2Fformat%2Cf_auto)](https://iknow-pic.cdn.bcebos.com/ac6eddc451da81cb8e45a22f5c66d016082431b4)

3、在下面的两个“X Maximum”后面的两个数据分别是我们提取数据的起点和终点。当我们把以上的操作完成后，点击“OK”即可，在这里选择提取100个数据，范围为图谱的原始范围。

[![img](https://iknow-pic.cdn.bcebos.com/1e30e924b899a901b60f7bd413950a7b0208f51e?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_600%2Ch_800%2Climit_1%2Fquality%2Cq_85%2Fformat%2Cf_auto)](https://iknow-pic.cdn.bcebos.com/1e30e924b899a901b60f7bd413950a7b0208f51e)

4、双击左边的“Book1”，就可以看到从图谱中提取的100个数据。









3、2 在graph中采样画图

![image-20220522143944215](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220522143944215.png)

![image-20220522144008950](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220522144008950.png)

![](D:\Origin2019_project_files\testgrapha1.png)





红色的线就是采样后的轨迹图



# 4、Origin中如何选取一定间隔的数据点和作图

**操作方法一**

\1. 打开origin内的热重数据，可以看到数据量较多，直接作图只能做成直线图



![img](https://pic1.zhimg.com/80/v2-b34625c9383a6ffcf81126c00d3a72e0_720w.jpg)




\2. 选取数据，正常做成点线图如下，不太美观



![img](https://pic4.zhimg.com/80/v2-9d51208bc84f3e422b9e003552a9680b_720w.jpg)





\3. 右击作出来的数据图，点击Plot details，在弹出的对话框中，先择Line，**一定记得取消勾选Gap to Symbol**，否则在数据处理后，无法显示曲线，只出现选取的点，即成为散点图



![img](https://pic1.zhimg.com/80/v2-14db33877f042913cc8e997738e7b17c_720w.jpg)



\4. 选择Drop line选项卡处，勾选skip points，可以设置合适的数字范围，此处为200，代表每隔200个数字取一个点，图像会发生变化如下



![img](https://pic3.zhimg.com/80/v2-1948cc28e4f0681396a957bb55b85862_720w.jpg)



\5. 对图像进行调整图下：



![img](https://pic1.zhimg.com/80/v2-ebac21c8c5dbad6b689c301eab79f604_720w.jpg)





------

**操作方法二**

\1. 打开数据，在表格中新建一个单独的列，如下所示，选中该列，右击，选择**Set column values**





![img](https://pic3.zhimg.com/80/v2-0d1d0a170c5df4ebc71be3db763127a6_720w.jpg)





\2. 此处以对y轴数据进行选取为例，在弹出的对话框中，按照英文格式输入**Col(D)[200\*i]**，其中Col是列的简称，D是上图中D列，也是要进行等距取点的列，200为隔多少点进行数据选取，i是自变量。设置好之后点击Apply即为选取成功。

同样的对x轴数据进行选取，该步骤同上。





![img](https://pic4.zhimg.com/80/v2-27b72c3e182b97daf83bc7ee19b3b7b7_720w.jpg)





\3. 对选取好的数据进行作图即可，如下所示：



![img](https://pic2.zhimg.com/80/v2-55c5dfe3d7a491df26ed9c583fdc4915_720w.jpg)











# Origin2019图片伸缩后坐标轴刻度消失，双击坐标轴选择在刻度栏勾选显示也没有用，是怎么回事呢？

2019b版本的bug

2019b版本，**若出现刻度线消失情况**，可以**不用卸载重装**。找到**根目录**下的**修复卸载程序**，**双击**他，然后选择**修改**，最后在弹出界面**选择32位**，**点击**下一步**直到**完成即可。（注：完成后仍需重新替换crack里面的文件）