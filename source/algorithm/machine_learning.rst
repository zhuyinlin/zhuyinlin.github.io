机器学习
==========

machine learning的算法分为两类： 生成式模型、判别式模型；其区别在于： 对于输入x，类别标签y，在生成式模型中估计其联合概率分布，而判别式模型估计其属于某类的条件概率分布。

生成方法（generative approach）
--------------------------------
+ 有监督学习
  1. 朴素贝叶斯(Naive Bayes)
  2. 混合高斯模型(GMM)
  3. 隐马尔科夫模型(MRF)

+ 无监督学习
  无监督深度学习
  1. 受限玻尔兹曼机（Restricted Boltzmann Machine, RBM）
  2. 深度信念网络（Deep Belief Network, DBN）
  3. 深度玻尔兹曼机（Deep Boltzmann Machine, DBM）
  4. 广义除噪自编码器（Generalized Denoising Autoencoders）

GAN
``````
 :ref:`gan`

变分自编码器（VAE，Variational Autoencoders） 
````````````````````````````````````````````````

`VAE 原来是这么回事 <https://zhuanlan.zhihu.com/p/34998569>`_

.. topic:: GAN vs AE

   相比于 GAN，AE 的优点：

   A. 使用GAN来生成图片使用的是随机高斯噪声，这意味着我们并不能生成任意我们指定类型的图片；但是使用 auto-encoder 我们就能够通过输出图片的编码过程得到这种类型图片的编码之后的分布，相当于我们是知道每种图片对应的噪声分布，我们就能够通过选择特定的噪声来生成我们想要生成的图片。
   B. 这既是生成网络的优点同时又有着一定的局限性，这就是生成网络通过对抗过程来区分“真”的图片和“假”的图片，然而这样得到的图片只是尽可能像真的，但是这并不能保证图片的内容是我们想要的，换句话说，有可能生成网络尽可能的去生成一些背景图案使得其尽可能真，但是里面没有实际的物体。

在AE中我们没有办法自己去构造隐藏向量，而是需要通过一张图片输入编码我们才知道得到的隐含向量是什么，这时我们就可以通过变分自动编码器（VAE，Variational Autoencoders）来解决这个问题: 只需要在编码过程给它增加一些限制，迫使其生成的隐含向量能够粗略的遵循一个标准正态分布，这就是其与一般的自动编码器最大的不同。

.. topic:: GAN vs VAE

   VAE 与 GANs 的不同是，VAE 中我们知道图像的密度函数（或者说，是我们设定的），而 GAN 中我们并不知道图像的分布。

   另外VAE除了可以让我们随机生成隐含变量，还能够提高网络的泛化能力。

   VAE的缺点也很明显，它是直接计算生成图片和原始图片的均方误差而不是像GAN那样去对抗来学习，这就使得生成的图片会有点模糊。现在已经有一些工作是将VAE和GAN结合起来，使用VAE的结构，但是使用对抗网络来进行训练，


自回归模型（Autoregressive models） 
``````````````````````````````````````


判别方法（discriminative approach）
------------------------------------

LogisticRegression， 
SVM, 
Neural Network

machine learning 的算法也可以分为三类：监督学习、非监督学习、增强学习

增强学习(Reinforcement Learning)
--------------------------------


在AlphaGo Zero出现之前，基于深度学习的增强学习方法按照使用的网络模型数量可以分为两类: 

- 一类使用一个DNN"端到端"地完成全部决策过程(比如DQN, Deep Q-Learning)，这类方法比较轻便，对于离散动作决策更适用; 
- 另一类使用多个DNN分别学习policy和value等(比如之前战胜李世石的AlphaGoGo)，这类方法比较复杂，对于各种决策更通用。
- 此次的AlphaGo Zero综合了二者长处，采用类似DQN的一个DNN网络实现决策过程，并利用这个DNN得到两种输出policy和value，然后利用一个蒙特卡罗搜索树完成当前步骤选择。


专业名词
~~~~~~~~~

Total reword: :math:`R=r_1 + r_2 + ... + r_n`

Total future reword : :math:`R_t = r_t + ... + r_n`

discounted future reword: :math:`R_t + \gamma R_{t+1} + ... + \gamma_{n-t} R_{t+n}  \gamma \in [0, 1]`
