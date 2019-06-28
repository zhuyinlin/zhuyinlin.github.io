斯坦福课程笔记
===============

cs231
-----

chapter 1
~~~~~~~~~~

TODO
``````
Approximate Nearest Neighbor (ANN) 
FLANN
k-mean
kdtree

chapter 2
~~~~~~~~~~


预处理
``````
图像： 
    1. 0 均值--> zero mean centering is arguably more important but we will have to wait for its justification until we understand the dynamics of gradient descent.
    2. 1方差

损失函数
``````````
include the regularization penalty: 

1. the set of W is not necessarily unique: if some parameters W correctly classify all examples (so loss is zero for each example), then any multiple of these parameters λW where λ>1 will also give zero loss because this transformation uniformly stretches all score magnitudes and hence also their absolute differences. this methos encode some preference for a certain set of weights W over others to remove this ambiguity
2. including the L2 penalty leads to the appealing max margin property in SVMs
3. penalizing large weights tends to improve generalization, because it means that no input dimension can have a very large influence on the scores all by itself since the L2 penalty prefers smaller and more diffuse weight vectors -->  lead to less overfitting.


softmax
````````

.. math::
        a_j &= \frac{e^{z_j}}{\sum_k e^{z_k}}

        \frac{\partial a_j}{\partial z_i} &= a_j(1-a_j)  if j == i:

        \frac{\partial a_j}{\partial z_i} &= -a_j a_i  if j \neq i:


TODO
``````
kernels, duals, the SMO algorithm
One-Vs-All (OVA) SVM, All-vs-All (AVA) SVM
Structured SVM
Leaky ReLU
