caffe 学习笔记
=========================

介绍
------
   
  caffe 可用于c++、python和matlab

Install
--------

- ubuntu :ref:`caffe-install-ubuntu`

Caffe 使用
----------------------------

已训练好的caffemodel 
~~~~~~~~~~~~~~~~~~~~~~~~
  已训练好的模型在 “models/bvlc_对应模型名称”文件夹下的 **readme.md** 文件中提供下载链接。

使用caffe编译自己的c++ 代码  
----------------------------

1. At the top of your .cpp program which is linking to the Caffe library, you will need to make the following definition ``#define CPU_ONLY``
2. Now if we try to compile anything now, Caffe will make this complaint: `caffe/proto/caffe.pb.h: No such file or directory`  Some of the header files are missing from the Caffe include directory. Thus, you’ll need to generate them with these commands from **within the Caffe root directory** :
  
  .. code-block:: bash

     $ protoc src/caffe/proto/caffe.proto --cpp_out=.
     $ mkdir include/caffe/proto 
     $ mv src/caffe/proto/caffe.pb.h include/caffe/proto

3. Finally, I copied libcaffe.so into /usr/lib and the caffe directory containing the header libraries (caffe_root/include/caffe) into the /usr/include directory. To compile this on a **Mac**  (after installing OpenBLAS with Homebrew), I just had to run:

  .. code-block:: bash
   
     $ g++ classification.cpp -lcaffe -lglog -lopencv_core -lopencv_highgui -lopencv_imgproc -I /usr/local/Cellar/openblas/0.2.14_1/include -L /usr/local/Cellar/openblas/0.2.14_1/lib -o classifier
   
  Alternatively, you could do what I did on my **Linux**  machine and instead of copying header files, I just linked directly to those directories when I compiled:

  .. code-block:: bash

     $ g++ classification.cpp -lcaffe -lglog -lopencv_core -lopencv_highgui -lopencv_imgproc -I ~/caffe/include -L ~/caffe/build/lib -I /usr/local/Cellar/openblas/0.2.14_1/include -L /usr/local/Cellar/openblas/0.2.14_1/lib -o classifier``

训练
----

`sover及其配置 <http://www.cnblogs.com/denny402/p/5074049.html>`_

`caffe中的lr_policy选择 <http://blog.csdn.net/eagelangel/article/details/51761292>`_

`caffe学习笔记（4）：lr_policy之各模型形式的总结 <http://blog.csdn.net/qq_30401249/article/details/51460480>`_

`fine_tune <https://zhuanlan.zhihu.com/p/22624331>`__
