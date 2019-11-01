基础知识
==========

基础概念
---------

变换和算子
``````````
变换和算子的最本质区别在于，经过“算子”运算，变量没有变，比如微分就是一种典型的算子，而经过“变换”运算则会改变变量的形式。

运算
``````

点积/内积/数量积（dot product/scalar product):
    两个向量的夹角

叉积：
    两个向量的法向量

Hadamard product（哈达玛积): 矩阵对应位置的数字相乘
   .. math::
           A\odot B =\left[\begin{array}{ccc}a_{11} & a_{12} & a_{13}\\
                                    a_{21} & a_{22} & a_{23}\\
                                    a_{31} & a_{32} & a_{33}\end{array} \right]
           \left[\begin{array}{ccc}b_{11} & b_{12} & b_{13}\\
                                    b_{21} & b_{22} & b_{23}\\
                                    b_{31} & b_{32} & b_{33}\end{array}\right]
           =\left[\begin{array}{ccc}a_{11} \cdot b_{11} & a_{12} \cdot b_{12} & a_{13} \cdot b_{13}\\
                                    a_{21} \cdot b_{21} & a_{22} \cdot b_{22} & a_{23} \cdot b_{23}\\
                                    a_{31} \cdot b_{31} & a_{32} \cdot b_{32} & a_{33} \cdot b_{33}\end{array} \right]
           :label: eq_hadamard_product

Kronecker product（克罗内克积/直积/张量积）:
   .. math::
           A\otimes B =\left[\begin{array}{ccc}a_{11} & a_{12} & a_{13}\\
                                    a_{21} & a_{22} & a_{23}\\
                                    a_{31} & a_{32} & a_{33}\end{array} \right]
           \left[\begin{array}{ccc}b_{11} & b_{12} & b_{13}\\
                                    b_{21} & b_{22} & b_{23}\\
                                    b_{31} & b_{32} & b_{33}\end{array}\right]
           =\left[\begin{array}{ccc}a_{11} B & a_{12} B & a_{13} B\\
                                    a_{21} B & a_{22} B & a_{23} B\\
                                    a_{31} B & a_{32} B & a_{33} B\end{array} \right]
           :label: eq_kronecker_product


拉普拉斯算子
  .. math::
     \bigtriangleup f = \bigtriangledown ^2f = \sum_i{\frac{\partial ^2 f}{\partial ^2 x_i}} = Tr(Hession(f))


各种变换
``````````

傅里叶变换
  `傅里叶级数 <https://www.matongxue.com/madocs/619.html>`_

  傅立叶级数是针对周期函数的，为了可以处理非周期函数，需要傅立叶变换

  `傅里叶变换 <https://www.matongxue.com/madocs/473.html>`_

  `傅里叶级数到傅里叶变换 <https://www.matongxue.com/madocs/712.html>`_

拉普拉斯变换
  为了解决傅里叶变换某些情况下不可积, 引出了拉普拉斯变换

  `拉普拉斯变换 <https://www.matongxue.com/madocs/723.html>`_
    
  `拉普拉斯变换中的S是个什么鬼？ <https://www.jianshu.com/p/400d93229ff5>`_

  `Where the Laplace Transform comes from (Arthur Mattuck, MIT) 1.0 <https://www.youtube.com/watch?v=zvbdoSeGAgI>`_
   
  `Where the Laplace Transform comes from (Arthur Mattuck, MIT) 2 <https://www.youtube.com/watch?v=hqOboV2jgVo>`_
    

特殊的函数
``````````

克罗内克函数(Kronecker delta function)
  .. math:: 
        \delta_{ij} = \left\{ 
        \begin{array}{ll} 
        0 & \textrm{if $i \ne j$} \\
        1 & \textrm{if $i= j$} 
        \end{array} \right.

狄拉克函数(dirac delta)
  .. math::
     \delta(x) = \left \{
     \begin{array}{ll}
     0 & x\ne 0 \\
     1 & x =+ \infty
     \end{array} \right.

有趣的知识
-----------

自然数为什么叫自然数



复杂度计算
----------

