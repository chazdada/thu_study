## Auto-DeepLab: Hierarchical Neural Architecture Search for Semantic Image Segmentation

### 背景

现在的工作存在问题：

- 注重于搜索重复的cell结构，然后手动设计外部网络去控制分辨率的改变
- 这种选择简化了搜索空间，但对于表现出更多网络级架构变化的密集图像预测来说，变得越来越困难。

- NAS最开始应用于处理图像分类问题，直接将用于图像分类的方法移植到图像分割任务中不可取。语义分割必须在高分辨率图像上进行。

所以要解决两个问题：

- 一个更加轻松和通用的搜索空间，捕捉由更高的分辨率带来的架构变化
- 一个更高效的架构搜索技术，因为更高的分辨率，需要更繁重的计算。

### 搜索空间

观察到现代CNN模型设计通常采用两级架构：

- 外层网络控制分辨率的改变
- 内层网络管理特定计算过程

将学习重复的cell结构和网络结构结合起来。

提出了类似于网格的网络级搜索空间。

![image-20191231155736501](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231155736501.png)

#### Cell Level Search Space

一个cell是完全卷积模块，一个cell有B个blocks组成。每个block有2个输入和一个输出组成。

所以一个block i在cell l里可以用一个5元组表示(I1, I2, O1, O2, C)。

![image-20191231155718983](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231155718983.png)

O包含8种操作

![image-20191231155811329](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231155811329.png)

C在论文中就是两个操作输出对应元素相加。

#### Network Level Search Space

之前的NAS工作是一旦发现了一个单元结构，就可以使用预定义的模式构建整个网络。这种方式简单直接：

- normal cells
- reduction cells

对于稠密的图像预测，注意到以下两条原则：

- 下一层的空间分辨率不是两倍大，就是两倍小，或者保持不变。
- 最小的空间分辨率向下采样32

### 方法

#### Cell Architecture

![image-20191231160619173](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231160619173.png)

![image-20191231160630317](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231160630317.png)

![image-20191231160720905](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231160720905.png)

#### Network Architecture

![image-20191231160813255](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231160813255.png)

![image-20191231160840324](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231160840324.png)

最后一层后面连接着ASPP模块。

![image-20191231161027740](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231161027740.png)

#### 优化参数

![image-20191231161046151](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231161046151.png)

#### 解码结构

cell结构：选择权重最大的两条路径

网络结构：使用维特比算法

### 实验结果

#### 训练细节

共12层，每个cell 5个block。

分辨率减半时，通道数增加一倍。

40轮，batch_size=2。

#### Cityscapes

![image-20191231162301319](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231162301319.png)

5000张精确标注图片，20000张粗略标注图片。19个分割标签。

#### PASCAL VOC 2012

![image-20191231162456974](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231162456974.png)

21个类，通过数据扩充得到10582张训练图片。

#### ADE20K

![image-20191231162553134](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191231162553134.png)

150个分割类，20000张训练图和2000张验证图片。