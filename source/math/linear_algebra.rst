线性代数
==========

:download:`stanford cs229 linalg <../_doc/math/cs229-linalg.pdf>`

主线： 有没有解 -> 解的个数 -> 解的结构

矩阵
------

`从高斯消元法到矩阵乘法 <https://www.matongxue.com/madocs/755.html>`_

`矩阵乘法的理解 <https://www.matongxue.com/madocs/555.html>`_


行列式
-------

`行列式的来历与克拉默法则 <https://www.matongxue.com/madocs/791.html>`_

`行列式的本质 <https://www.zhihu.com/question/36966326/answer/162550802>`_

矩阵的属性
-----------

`矩阵的秩(rank) <https://www.matongxue.com/madocs/254/>`_

   - 学术：矩阵中最大线性不相关向量个数
   - 具象：变换后的空间维度

   `矩阵的秩与解的关系 <https://www.zhihu.com/question/21832377/answer/202231378>`_

   `列秩等于行秩的原因 <https://www.matongxue.com/madocs/290/>`_

`相似矩阵 <https://www.matongxue.com/madocs/491.html>`_

   同一个变换在不同基下的矩阵

   相似不变量
      A. 行列式 :math:`\lambda_1 \times \lambda_2 \times \cdots \times \lambda_n`
      B. 迹 :math:`\lambda_1 + \lambda_2 + \cdots + \lambda_n`

`矩阵的迹（trace) <https://www.matongxue.com/madocs/483/>`_ 

`EVD 特征值的理解 <https://www.zhihu.com/question/21874816/answer/181864044>`_

因为特征值分解必须满足方阵的前提，因此引入奇异值分解

.. note::

   矩阵的谱分解，特征分解，对角化都是同一个概念

`奇异值分解(SVD) <https://www.matongxue.com/madocs/306/>`_

   对旋转、缩放和投影三种变换的析构

正定矩阵和半正定矩阵

   - 变换后的向量与自身的夹角小于90度/小于等于90度
   - 所有特征值均大于零/不小于0
   - 曲面(二次型)在底面上方

TODO:

1. 对称矩阵一定n个线性无关的特征向量，所有特征向量构成的矩阵为正交矩阵
