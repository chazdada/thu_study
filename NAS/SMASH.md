## SMASH

训练步骤：

- 随机采样一个网络结构，使用HyperNet生成结构的权重
- 通过反向传播训练该系统
- 模型训练完毕，随机采样一批结构，并使用HyperNet生成权重
- 选择在验证数据集上效果最好的结构，使用正常训练方式训练该结构

算法组成的两个核心问题：

- 如何采样结构
  - 使用memory- bank方式表示前馈神经网络， 允许将复杂的、分支的拓扑作为二进制向量进行采样和编码。 
- 对于采样的结构如何给定参数
  - 使用了一个HyperNet，它学会了直接从二进制体系结构编码映射到权重空间。 

前提假设：

- 只要HyperNet学习生成合理的权值，生成权值的网络的验证错误将与使用正常训练权值时的性能相关，架构的差异是变化的主要因素。 

#### 定义可变网络配置

memory-bank

keys：

- 不把网络看作用于正向传播一系列信号的操作，而是看作一组存储库，可以读写。

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191106150835730.png)

- 每一层都是一个从内存子集读取数据、修改数据并将结果写入另一个内存子集的操作。
- 基础网络架构

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191106150808116.png)

- 在每个块上随机采样存储库数目和每个存储库的通道数目。
- 在定义一个块中的每个层时，随机选择读写模式和要在读取数据上执行的op定义。 

####  学习将架构映射到权重 

HyperNet使用的是神经网络

动态HyperNet的变体：根据采样架构c的网络编码生成权重W

目标： 对于任何给定的c，都相当接近最优的W，这样就可以根据使用HyperNet生成的权值的验证错误对每个c进行排序。 

keys：

- HyperNet是全卷积的，这样输出张量W的维数就会随着输入c的维数而变化，这样就得到了标准格式BCHW的4D张量，批量大小为1，因此没有输出元素是完全独立的。 

- 通过增加c的高度或宽度来改变主网络的深度和宽度 

  ![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191106152509423.png)

#### 实验

##### 验证smash的相关性

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191106153247780.png)

标准测试

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191106153701295.png)