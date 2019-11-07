## DARTS

传统体系结构搜索是离散域上的黑盒优化问题，需要大量的结构评估。

搜索空间连续，可以使用梯度下降算法进行结构的优化。

贡献：

- 基于双层优化，提出了一种可微网络架构搜索的方法

- 在CIFAR-10取得了高竞争力的结果，在PTB上超越了目前最高技术水平
- 搜索效率得到了显著的提高
- 学得模型可以迁移学习

#### 搜索空间

computation cell 作为最终结构的一个块，cell可以堆叠成为卷积神经网络或者循环神经网络

keys:

- 一个cell由N个node组成的有向无环图，node表示特征图，每条边表示对node进行的操作。假设每个cell有两个输入节点和一个输出节点。

- 每个cell里node都是基于其所有的前导节点的

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107100223813.png)

- 0操作表示两个node之间没有操作

#### 松弛与优化

O表示一系列候选操作（卷积、池化、0），为了使搜索空间连续，使用softmax求混合操作

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107100636411.png)

a表示权重，架构搜索的目标就是学习一系列的a，在搜索结束时，通过将每个混合操作o(i,j)替换成最可能的操作，就可以得到一个离散的架构。 

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107100905623.png)

验证集被作为奖励或者适应度DARTS目的是使用梯度下降优化验证集损失。

a bilevel optimization problem

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107101240693.png)

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107101340901.png)

#### 近似架构下降

由于内部优化非常耗时，采用一种近似的方法

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107101531766.png)

w是当前算法权重值，y是学习率

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107101715908.png)

![image-20191107101733791](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\image-20191107101733791.png)

第二项中有个矩阵向量代价昂贵

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107101856629.png)

复杂度

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107101943884.png)

#### 得到离散架构

在所有先前节点所有候选非0操作中保留k个最强操作

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107102240535.png)

去掉0边有两个原因：

- 与其他算法公平比较，保证k个非0的进入边
- 0边的作用未知，不影响分类

#### 实验结果

两步：

- 使用DARTS搜索cell结构
- 使用cell堆成更大的结构

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107104500107.png)

架构评估

- cifar10

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107104644116.png)

- PTB

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107104747985.png)

- ImageNet

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107104853599.png)