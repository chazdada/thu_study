一般深度学习调参方法：

- random search
- grid search

### 搜索空间

定义了优化问题的变量

### 搜索策略

- 强化学习
- 进化算法
- 贝叶斯优化

### 评价预估

- 用一些低保真的训练集来训练模型，减少训练时间，但有一定的bias
- 借鉴于工程优化中的代理模型
- 参数级别的迁移
- 单次架构搜索

## NEURAL ARCHITECTURE SEARCH WITH REINFORCEMENT LEARNING
### abstract

- neural networks are still hard to design
- use a recurrent network to generate the model descriptions
- train this RNN with reinforcement learning to maximize the expected accuracy of the generated architectures on a validation set.

### introduction

- success that shift from feature designing to architecture designing（从功能设计转向结构设计），requires a lot of expert knowledge and takes ample time.

- presents Neural Architecture Search, a gradient-based method for finding good architectures

  ![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107114204611.png) 

- reasons:
  - neural network can be typically specified by a **variable-length string**
  - possible to use a recurrent network – the controller – to generate such string
  - Training the network specified by the string – the “child network” – on the real data will result in an accuracy on a validation set
  - Using this accuracy as the reward signal, we can compute the policy gradient to update the controller.

### related work

- Hyperparameter optimization
  - only search models from a fixed-length space
  - Often work better if they are supplied with a good initial model 
  - Bayesian optimization methods that allow to search non fixed length architectures 
- Modern neuro-evolution algorithms
  - less practical at a large scale
  - slow or require many heuristics to work well
- auto-regressive
  - predicts hyperparameters one a time, conditioned on previous predictions. This idea is borrowed from the decoder in end-to-end sequence to sequence learning.
- learning to learn or meta-learning
- reinforcement learning

### method

#### GENERATE MODEL DESCRIPTIONS WITH A CONTROLLER RECURRENT NEURAL NETWORK

![Image text](https://github.com/chazdada/thu_study/blob/master/images/1570328193638.png)

- the process of generating an architecture stops if the number of layers exceeds a certain value.
- the controller RNN finishes generating an architecture, a neural network with this architecture is built and trained.
- the accuracy of the network on a held-out validation set is recorded
- The parameters of the controller RNN, θc, are then optimized in order to maximize the expected validation accuracy of the proposed architectures

#### TRAINING WITH REINFORCE

- predicts can be viewed as a list of actions a1:T 

- child network will achieve an accuracy R on a held-out dataset. R as the reward signal.

- maximize its expected reward, represented by J(θc)

  ![Image text](https://github.com/chazdada/thu_study/blob/master/images/1570328567943.png)

- use a policy gradient method to iteratively update θc

  ![Image text](https://github.com/chazdada/thu_study/blob/master/images/1570328615804.png)

  ​			![1570328630804](C:\Users\10179\AppData\Roaming\Typora\typora-user-images\1570328630804.png)

- m is the number of different architectures that the controller samples in one batch

- T is the number of hyperparameters our controller has to predict to design a neural network architecture

- an unbiased estimate for our gradient, but has a very high variance. In order to reduce the variance of this estimate we employ a baseline function

  ![Image text](https://github.com/chazdada/thu_study/blob/master/images/1570328795961.png)

#### Accelerate Training with Parallelism and Asynchronous Updates

![Image text](https://github.com/chazdada/thu_study/blob/master/images/1570328949608.png)

#### INCREASE ARCHITECTURE COMPLEXITY WITH SKIP CONNECTIONS AND OTHER LAYER TYPES

introduce a method that allows our controller to propose skip connections or branching layers, thereby widening the search space.

#### GENERATE RECURRENT CELL ARCHITECTURES
- The controller RNN needs to label each node in the tree with a combination method 
- an activation function (tanh, sigmoid, etc.) to merge two inputs and produce one output.

#### GENERATE RECURRENT CELL ARCHITECTURES

- The controller RNN needs to label each node in the tree with a combination method (addition, elementwise multiplication, etc.)
- an activation function (tanh, sigmoid, etc.) to merge two inputs and produce one output. 
- The controller RNN needs to first predict 3 blocks, each block specifying a combina- tion method and an activation function for each tree index. After that it needs to predict the last 2 blocks that specify how to connect ct and ct−1 to temporary variables inside the tree.

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107122927838.png)

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107122943067.png)

### results

cifar10

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107123111599.png)

PTB

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107123208568.png)

## Efficient Neural Architecture Search via Parameter Sharing

### Abstract

- searching for an optimal subgraph within a large computa- tional graph. 
- The controller is trained with pol- icy gradient to select a subgraph that maximizes the expected reward on a validation set.
- the model corresponding to the selected subgraph is trained to minimize a canonical cross entropy loss.
- Sharing parameters among child models allows ENAS 

### Introduction

- NAS is computationally expensive and time consuming
- improve the efficiency of NAS by forcing all child models to share weights to eschew training each child model from scratch to convergence. 

### Methods
- represent NAS’s search space using a single directed acyclic graph (DAG). all possible child models in a search space of NAS
- the nodes represent the local computations and the edges represent the flow of information
- each node have their own parameters, which are used only when the particular computation is activated.ENAS’s design allows parameters to be shared among all child models.

#### Designing Recurrent Cells
ENAS’s controller is an RNN that decides:

- which edges are activated
- which computations are performed at each node in the DAG.

-  This design of our search space for RNN cells is different from the search space for RNN cells in Zoph & Le (2017), where the authors fix the topology of their architectures as a binary tree and only learn the operations at each node of the tree. In contrast, our search space allows ENAS to design both the topologyand the operations in RNN cells,

![Image text](https://github.com/chazdada/thu_study/blob/master/images/1570350099833.png)

#### Training ENAS and Deriving Architectures

controller network is an LSTM with 100 hidden units

- the decision in the previous step is fed as input embedding into the next step. At the first step, the controller network receives an empty embedding as input. 

two sets of learnable parameters:

- The first phase trains ω, the shared parameters of the child models

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107125522160.png)

- The second phase trains θ, the parameters of the controller LSTM, for a fixed num- ber of steps

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107125547655.png)

#### Designing Convolutional Networks

设计整个卷积神经网络

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107125635149.png)

控制RNN做两个决定：

- 与之前哪一个节点相连
- 使用哪一种计算操作

#### Designing Convolutional Cells

设计小的模块，然后连接起来

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107125928170.png)

RNN控制决定：

- 哪两个节点作为当前节点的输入
- 对于两个采样节点的两种输入

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107130139815.png)

### 实验结果

PTB

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107130331107.png)

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107130356420.png)

CIFAR-10

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107130459245.png)

![Image text](https://github.com/chazdada/thu_study/blob/master/images/image-20191107130513051.png)