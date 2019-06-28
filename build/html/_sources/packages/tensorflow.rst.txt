tensorflow 学习笔记
=========================

.. contents::
    :local:
    :backlinks: top

介绍
------
   
  tensorflow 可用于c、c++和python
  
  最好是从源码安装（在开发android app时要求）
  
  生成动态链接库(生成的文件在 bazel-bin/tensoflow 下)
    .. code-block:: bash

        # ubuntu(.so)
        bazel build //tensoflow:libtensorflow.so

        # mac(.dylib)
        bazel build //tensorflow

  .. 此处插入工作原理图

  工作流程包括以下几大块：
    - construction phase ——> assembles a graph
    - execution phase    ——> session
    - save & load        ——> saver, superviser (GraphDef)
    - visuable the graph ——> summary, tensorboard

Install
---------
- ubuntu :ref:`tf-install-ubuntu`


从构建模型到Android端
----------------------

训练阶段
~~~~~~~~~

1  构建模型时注意事项
``````````````````````
  tf.app.flag
    不建议使用，未来可能修改，建议使用python的 `argparse module <https://docs.python.org/2.7/library/argparse.html>`_ 实现自己的flag parsing
   
  build a graph that works with variable batch sizes
    使用 placeholder 时将 shape 的第一位设为 None，此时注意构建 model 时的 reshape 问题，将相应位置设为 －1
    
    其他参考 `build a graph that works with variable batch sizes <https://www.tensorflow.org/versions/r0.10/resources/faq.html>`_
  
  网络复用
    | `tutorial <https://www.tensorflow.org/versions/r0.11/how_tos/variable_scope/index.html>`_
    | 定义时使用 tf.get_variable() 以检测 variable 是否重复；
    | 在定义网络时，在 scope 命名的名称下定义每个子网络，可实现复用；
    | 在 validate 时，设置reuse，那么可定义一个测试网络，其参数与 inference 一致；

    .. Warning::

            reuse 默认为 False，只能由 False 设置为 True， 其后保持该状态，无法从 True 设置为 False

1.1 add new op to TF-platform
''''''''''''''''''''''''''''''
  1. tensoflow/core/ops 中注册 op （ _ops.cc 中 ``REGISTER_OP`` for op and grad）
  2. tensoflow/core/kernels 下实现 op （ _op.cc, _op_gpu.cu.cc, _op_gpu.h）
  3. tensoflow/core/ops/ops.pbtxt  中添加 op 的定义  
  4. tensoflow/core/BUILD 的 ``ops`` 、 ``all_kernels`` 中声明前述定义
  5. tensorflow/core/kernels/BUILD 相应 ``cc_library``,  ``tf_kernel_library ``, ``filegroup``
  6. tensoflow/python/ops 中声明 (.py @@, _grad.py @ops.RegisterGradient("XXX"))
  7. tensorflow/contrib/makefile/tf_op_files.txt 加入op kernels 所在路径
  8. 重新编译

     .. code-block:: bash
         
         bazel clean
         ./configure
         bazel build ...
         
1.2 data input pipline
''''''''''''''''''''''
  1. 生成 tfrecord 时的压缩问题，图片做tf.image.encode_XXX, 
  2. 读取 tfrecord 时注意keys_to_feature 每条的格式，图片读出规格，
  3. 读成 batch 注意前面各步骤是否导致图片大小不可知


2  模型&数据的保存和导入
````````````````````````

2.1 Saver(*re:cifar10*)
''''''''''''''''''''''''''

  ::

        saver = tf.train.Saver()
        saver.save(sess,FLAG.checkpoint_dir + 'xxx.ckpt', global_step = #)
        # saver.save(..., write_meta_graph=False) 可在保存 .ckpt 文件时不保存 .meta文件 
        saver.load()

        # restore the weights
        ckpt = tf.train.get_checkpoint_state(FLAG.checkpoint_dir)
        if ckpt and ckpt.model_checkpoint_path:
          saver.restore(sess, ckpt.model_checkpoint_path)

  Saver 将 model 中的 Variable 保存为 checkpoints(.ckpt)

  如果只想保存／导入部分参数，在定义 saver 时指定 var_list 参数，详情参考 API 文档 

  By default, a tf.train.Saver will create ops that (i) save every variable in your graph when you call saver.save() and (ii) lookup (by name) every variable in the given checkpoint when you call saver.restore(). While this works for most common scenarios, you have to provide more information to work with specific subsets of the variables:
  
  1. If you only want to restore a subset of the variables, you can get a list of these variables by calling ``tf.get_collection(tf.GraphKeys.GLOBAL_VARIABLES, scope=G_NETWORK_PREFIX)`` , assuming that you put the "g" network in a common with ``tf.name_scope(G_NETWORK_PREFIX):`` or ``tf.variable_scope(G_NETWORK_PREFIX):`` block. You can then pass this list to the ``tf.train.Saver`` constructor.
  
  2. If you want to restore a subset of the variable and/or they variables in the checkpoint have **different names** , you can pass a dictionary as the ``var_list`` argument. By default, each variable in a checkpoint is associated with a key, which is the value of its ``tf.Variable.name`` property. If the name is different in the target graph (e.g. because you added a scope prefix), you can specify a dictionary that maps string keys (in the checkpoint file) to ``tf.Variable`` objects (in the target graph).

  save model::
          
          w1 = tf.Variable(tf.truncated_normal(shape=[10]), name='w1')
          w2 = tf.Variable(tf.truncated_normal(shape=[20]), name='w2')
          tf.add_to_collection('vars', w1)
          tf.add_to_collection('vars', w2)
          saver = tf.train.Saver()
          sess = tf.Session()
          sess.run(tf.global_variables_initializer())
          saver.save(sess, 'my-model')
          # `save` method will call `export_meta_graph` implicitly.
          # you will get saved graph files:my-model.meta
      
  restore model::
          
          sess = tf.Session()
          new_saver = tf.train.import_meta_graph('my-model.meta')
          new_saver.restore(sess, tf.train.latest_checkpoint('./'))
          all_vars = tf.get_collection('vars')
          for v in all_vars:    
              v_ = sess.run(v)
              print(v_)

  .. Note::

      训练时定义 saver 需在构建好网络后， 设置训练参数（如 lr， momentum 等）前， 否则模型无关的参数会加入到 saver 的 参数列表中，当从已训练好的模型中导入参数时会报错！或者定义 saver 时， 指定参数列表。

      如果保存的模型文件随着step 增大， 可能是在训练loop 中有 add op 的操作，导致graph 随训练增大 —— Try printing out the node names in the graph each step to checkout. Or you can do "tf.get_default_graph().finalize()" after you've done modifying your graph, that'll throw an error on any new modifications




2.2 Supervisor `ref1`_
'''''''''''''''''''''''

.. _ref1: https://www.tensorflow.org/programmers_guide/meta_graph

  :: 
       
        # restore the model 
        tf.import_graph_def

       
  Supervisor 将 model definition 保存为 graph.pbtxt

  Note:
    - 由于 Saver 使用 special collection holding list of Variables that's attached to the model Graph, 而import_graph_def 时不会初始化这个 collection ，因此这二者不能一起使用；
    - 目前可以使用 Pyan Sepassi 的方法——构造Graph时为每个 node 添加名字，然后使用 Saver 导入 weights；
    - 如果要一起使用，在创建Variable的时候，对每个Variable对象使用 tf.add_to_collection(tf.GraphKeys.VARIABLES,variable);


2.3 同时保存模型和数据
''''''''''''''''''''''
  思路:
    | 先使用 tf.import_graph_def 的 constants 替换 original（training）graph 中的 variables；
    | 然后使用 tf.Graph.as_graph_def write out the resulting GraphDef
  
  具体步骤如下:
    1. 构建并训练自己的模型 tf.Graph （记为 g_1）；
    #. 提取variable最终的值并存储为 numpy array （使用session.run()）；
    #. 在新的 tf.Graph （记为g_2）中为每个 variable 创建 tf.constant（），并用步骤2 的值赋值；                   
    #. 使用 tf.import_graph_def 将 g_1 的节点拷贝到 g_2，利用参数 input_map ，将g_1 中的variable 替换为步骤3中创建的 tf.constant()；
    #. 使用 g_2.as_graph_def 保存 graph 的 protocol buffer representation。

------------------------------------------------

**方法1**

  采用“/tensorflow/python/tools/freeze_graph.py”脚本，参考 `A tool... <https://www.tensorflow.org/versions/r0.9/how_tos/tool_developers/index.html#graphdef>`_ 
  
  步骤如下:
    1. Save the checkpoints. (This is important as all your trained variables reside here)
    2. Save the graph definition (raw definition, no variable). 
    3. Use the freeze_graph file to combine the graph structure(step2) with the valuse of each nodes values(step2) and generate a new graph model

**方法2**

  serialize your minimal graph both into textual(.pbtxt) and binary(.pb) protobuf by code:

  ::

        from tensorflow.python.framework.graph_util import convert_variables_to_constants

        minimal_graph = convert_variables_to_constants(sess,sess.graph_def,["output"]) 
        #"output" is the name of prediction node

        tf.train.write_graph(minimal_graph,'.','xxx.proto', as_text=False)
        tf.train.write_graph(minimal_graph,'.','xxx.txt', as_text=True)

        # restore the model 
        tf.import_graph_def

        # serialize the graph
        tf.Graph.as_graph_def

  **convert_variables_to_constants** 做了两件事:
    | it freeze the weights by replacing variables with constants
    | it removes nodes which are not related to feedforward prediction 

  这个方法能够减小保存的 .pb 文件的大小，将不必要的内容删除；直接保存 .pb 会将 tensorflow 的所有方法加载

3  可视化(tensorboard)
`````````````````````````
  保存信息：
  
  .. code-block:: python
            
      #在下一语句前至少调用一次本类语句，否则报错 
      tf.scalar_summary("name",variablename)           
      tf.image_summary("name",imagename)

      summary_op = tf.merge_all_summaries()  # 注意，本句不能放到训练循环内，否则会导致meta文件不断增大
      # 或者：
      loss_summary = tf.scalar_summary(...)
      summary_op = tf.merge_summary([loss_sum])

      summary_writer = tf.train.SummaryWriter('/path/to/logs',sess.graph)

      summary_str = sess.run(summary_op) # 根据情况,有些必须要feed_dict
      summary_writer.add_summary(summary_str,global_step=step)
      summary_writer.close()  #必须加，否则没有东西显示

  显示：

  .. code-block:: bash

     # pip 安装      
     $ tensorboard --logdir=/path/to/logs/
   
     # 源码安装
     $ python tensorflow/tensorboard/tensorboard.py --logdir=/path/to/logs/
    
  通过 sshdf 连接查看时，相对路径不起作用，要退到~, 然后使用绝对路径

  .. code-block:: bash
     
      cd ~
      tensorboard --logdir=<abs_dir>

  .. Note::
     
     每次保存前要将 logs 中的文件删除，否则出现混乱。

4  对已训练好模型的操作
```````````````````````

4.1 Changing Inputs in existing networks `ref2`_
'''''''''''''''''''''''''''''''''''''''''''''''''

.. _ref2: https://github.com/tensorflow/tensorflow/issues/1758

The ``tf.import_graph_def()`` function provides the only (supported) way to perform this surgery, via the optional ``input_map`` argument. Let's say you want to replace the tensor "DecodeJpeg:0" with your new variable. You would do something like the following:
  
.. code-block:: python
      
   graph_def = ...
   tf_new_image = tf.constant(...)
   _ = tf.import_graph_def(graph_def, input_map={"DecodeJpeg:0": tf_new_image})

4.2 将已有网络作为现有网络的一部分重新训练网络 `ref3`_
''''''''''''''''''''''''''''''''''''''''''''''''''''''

.. _ref3: http://stackoverflow.com/questions/35180888/how-to-use-pre-trained-model-as-non-trainable-sub-network-in-tensorflow

若要保留原有参数，optimize 时将欲保留的参数除外，如下：

.. code-block:: python

   opt.minimize(loss, <subset of variables you want to train>)

否则导入参数后正常训练即可

4.3 `retrain models`_

.. _retrain models: https://github.com/tensorflow/models/tree/master/inception#how-to-retrain-a-trained-model-on-the-flowers-data
 
5 set layer-wise learning rate
````````````````````````````````

   `How to set layer-wise learning rate in Tensorflow? <http://stackoverflow.com/questions/34945554/how-to-set-layer-wise-learning-rate-in-tensorflow>`_

   .. code-block:: python

       var_list1 = [variables from first 5 layers]
       var_list2 = [the rest ofvariables]
       opt1 = tf.train.GradientDescentOptimizer(0.00001)
       opt2 = tf.train.GradientDescentOptimizer(0.0001)
       grads = tf.gradients(loss, var_list1 + var_list2)
       grads1 = grads[:len(var_list1)]
       grads2 = grads[len(var_list1):]
       tran_op1 = opt1.apply_gradients(zip(grads1, var_list1))
       train_op2 = opt2.apply_gradients(zip(grads2, var_list2))
       train_op = tf.group(train_op1, train_op2)

调试阶段
~~~~~~~~

`tools <https://wookayin.github.io/tensorflow-talk-debugging/#1>`_

.. Note::
        tfdbg 调试时，不能直接从 .sh 文件运行，否则会导致 `cbreak() returned ERR` 的错误。直接从命令行运行 python 脚本



测试阶段
~~~~~~~~

1  .pb文件的导入(*re:classify_image*)
`````````````````````````````````````````
  有两种表达方式：
    ::
           
          from tensorflow.python.platform import gfile

          # 第1种方法 (当需要构建network时，此法不可用？)
          with tf.Session() as sess:
            with gfile.FastGFile('/path/to/.pbfile','rb') as f:
            # 也可以是tf.gfile.FastGFile
              graph_def = tf.GraphDef()
              graph_def.ParseFromString(f.read())
              sess.graph.as_default()
              tf.import_graph_def(graph_def)

          # 第2种方法 (node 名称前会加上 import)
          with tf.Graph().as_default() as imported_graph:
            with gfile.FastGFile('/path/to/.pbfile','rb') as f:
              graph_def = tf.GraphDef()
              graph_def.ParseFromString(f.read())
              tf.import_graph_def(graph_def)
          sess = tf.Session(graph = imported_graph)

  使用时，为确定调用名称，使用以下语句查看：
    ::

            for node in session.graph_def.node:
              print node.name
            for op in tf.get_default_graph().get_operations():
              print op.name
            for op in sess.graph.get_operations():  # 此句同上
              print op.name

            softmax_tensor =  sess.graph.get_tensor_by_name("XXX")
            prediction = sess.run(softmax_tensor,{"name",input})



C++ 开发
~~~~~~~~ 
   `loading-a-tensorflow-graph-with-the-c-api <https://medium.com/jim-fleming/loading-a-tensorflow-graph-with-the-c-api-4caaff88463f>`_

1  创建graph并使用
``````````````````   
    :: 
          
            ... // root 添加一系列ops
            tensoflow::GraphDef graph_def;
            TF_RETURN_IF_ERROR(root.ToGraphDef(&graph_def));
            
            std::unique_ptr<tensorflow::Session> session(
                tensorflow::NewSession(tensorflow::SessionOptions()));
            TF_RETURN_IF_ERROR(session->Create(graph_def));
            TF_RETURN_IF_ERROR(session->Run({}, # input 
                                         {output_name} # which node we want get the output from
                                         , {}, out_tensors # where to put the output data
                                         ));


  
2  .pb文件的使用(*re:label_image*)
``````````````````````````````````
  
    ::  

          // 方法1:
          Status LoadGraph(string graph_file_name, Session** session) {
            tensoflow::GraphDef graph_def;
            TF_RETURN_IF_ERROR(
                  ReadBinaryProto(tensoflow::Env::Default(), graph_file_name, &graph_def)
            );
            TF_RETURN_IF_ERROR(NewSession(SessionOptions(), session));
            TF_RETURN_IF_ERROR((*session)->Create(graph_def));
            return Status::OK();
          }

          // 方法2:
          Status LoadGraph(string graph_file_name, std::unique_ptr<tensoflow::Session>* session) {
            tensoflow::GraphDef graph_def;
            TF_RETURN_IF_ERROR(
                  ReadBinaryProto(tensoflow::Env::Default(), graph_file_name, &graph_def)
            );
            session->reset(tensoflow::NewSession(tensoflow::SessionOptions()));
            TF_RETURN_IF_ERROR((*session)->Create(graph_def));
            return Status::OK();
          }
  
  
  编译注意事项：
    1. BUILD 文件中的内容（比如文件name 与 .cc 文件一致）
    2. 整个项目文件夹要放在" tensorflow/tensorflow"以下的文件夹中
    3. LISCENCE 文件？？
    4. 不要和其它无关文件放在一起
    
    5. 单幅图时注意expand dims

3  生成 tensorflow 的动态链接库
````````````````````````````````
  1. Create a new folder in the TensorFlow repo at tensorflow/tensorflow/libtensorflow/.
     ::
          
          tensorflow/tensorflow/libtensorflow/
          tensorflow/tensorflow/libtensorflow/BUILD
  2. Inside this folder we’re going to create a new BUILD file which will contain a single call to cc_binary with the linkshared option set to 1 so that we get a .so from the build. The name of the binary must end in .so or it will not work.

     .. code-block:: makefile

         cc_binary(    
             name = "libtensorflow.so",    
             linkshared = 1,    
             deps = [        
                 "//tensorflow/core:tensorflow",    
             ]
         )
     
  3. From the root of the repository, run ``./configure`` .
  4. Compile the shared library with ``bazel build --config=opt //tensorflow/libtensorflow:libtensorflow.so`` and locate the generated file from the repo’s root: ``bazel-bin/tensorflow/libtensorflow/libtensorflow.so`` 

  .. Note::

          If you’re on OS X and using Node.js you’ll need to rename the shared library from libtensorflow.so to libtensorflow.dylib

          if compile with GPU, cmd is ``bazel build --config=opt --config=cuda //tensorflow/libtensorflow:libtensorflow.so``
     
4 c++ 代码调试
````````````````

   在编译时加入调试信息 ``bazel build -c dbg //<path to src>:<target_name>`` , 然后使用 gdb 调试

   更多调试， 参考 `TENSORFLOW_DEBUG.md <https://gist.github.com/Mistobaan/738e76c3a5bb1f9bcc52e2809a23a7a1>`_

   `在macOS上用LLDB调试TensorFlow源码 <https://pure-earth-7284.herokuapp.com/2016/10/18/debug-tensorflow-using-lldb/>`_

Android实现
~~~~~~~~~~~

android环境搭建
``````````````````
- 若tensorflow尚未下载，最好用以下方法下载::
        
        $ git clone https://github.com/tensorflow/tensorflow.git --recurse-submodules

- 后续参考“/tensorflow/example/android”中的 **Readme.md** 文件（国内sdk 和 ndk 的下载地址: `androiddevtools <http://www.androiddevtools.cn>`_ ）

- 编译生成的.so文件（位于.cache文件夹下）可用于androd studio中开发使用。

  .. Tip::
     可以通过查看BUILD文件查询.so文件的名称，后通过locate命令查询其在ubuntu中的位置。

- tensorflow的mobile实现： `mobile <https://www.tensorflow.org/mobile.html>` _

- 有些错误可能是因为环境变量的设置问题，例如报错 /usr/local/bin/gcc ,将/usr/local/bin 从PATH中删除即可，猜测可能是gcc冲突之类的原因。注意修改环境变量后确认其是否真正生效！！

- 在反复修改编译的过程中建议使用 ``bazel clean`` 来清除前次的编译结果，避免未知错误 
- bazel 版本造成问题，加 --invcompatible_load_argument_is_label=false
- 找不到cuda等的.so文件，build 加 --action_env="LD_LIBRARY_PATH=${LD_LIBRARY_PATH}"

.. Note::

    若之前用于编译生成gpu 版的.whl 文件，那么此时注意重新configure，因为android 代码的编译不需要 gpu。
    the android demo should not need CUDA. Is it possible during configuration you configured TF to build with CUDA? That would add --config=cuda automatically to the build.


开发
``````````````````

bazel build 后生成 .so 文件(在bazel-out|.cache文件夹下)，可将其用于AS中开发。

  .. Tip::
     可以通过查看BUILD文件查询.so文件的名称，后通过locate命令查询其在ubuntu中的位置。

参考 `Android TensorFlow Machine Learning Example <https://blog.mindorks.com/android-tensorflow-machine-learning-example-ff0e9b2654cc>`_

1. 根据 ``tensoflow/contrib/android`` 下的 ``README`` build the **.jar** and **.so** file.
2. create an android sample project in Android Studio.
3. Put **label file** (imagenet_comp_graph_label_strings.txt) and **pre-trained model** (tensorflow_inception_graph.pb) into ``assets`` folder.
4. Put **.jar** file in ``libs`` folder and right click and add as library.
5. Create ``jniLibs`` folder in ``main`` directory and put **.so** in ``jniLibs/armeabi-v7a`` folder.

   ::

                ├── app
                │   ├── build.gradle
                │   ├── libs
                │   │   └── libandroid_tensorflow_inference_java.jar
                │   └── src
                │       ├── androidTest
                │       ├── main
                │       │   ├── AndroidManifest.xml
                │       │   ├── assets
                │       │   │   ├── imagenet_comp_graph_label_strings.txt
                │       │   │   └── tensorflow_inception_graph.pb
                │       │   ├── java
                │       │   │   └── <main java code>
                │       │   ├── jniLibs
                │       │   │   └── armeabi-v7a
                │       │   │       └── libtensorflow_inference.so
                │       │   └── res
                │       └── test
                ├── assets
                │   └──<test image> 
                ├── build.gradle
                ├── gradle
                ├── gradle.properties
                ├── gradlew
                ├── gradlew.bat
                └── settings.gradle


注意事项
````````
  1. .h 和 .cc 文件同步修改
  2. 同步更换 .pb 和 .txt 文件
  
错误集锦
````````
  No OpKernel was registered to Support:
    | 出错的原因是模型中包含的某些运算没有加到 BUILD 文件中，解决办法有两条：
    | 1 将包含相应运算的文件包含到 BUILD 文件中 
    | 2 可能的话，保存不包含该运算的模型



Caffe到tensorflow的转换
--------------------------
转换程序代码： `caffe-tensorflow <https://github.com/ethereon/caffe-tensorflow>`_

Nodejs
-------
  `lodading tensorflow model from Node.js <https://medium.com/jim-fleming/loading-tensorflow-graphs-via-host-languages-be10fd81876f>`_


源代码精读
-----------

基础应用注意
~~~~~~~~~~~~~

tensor A * B -- 元素运算
tf.multiply(A, B) -- 元素运算
tf.matmul(A, B) -- 矩阵运算


笔记
~~~~~~~~~

tf.shape() 返回动态纬度， tensor.shape 返回静态纬度

Seq2Seq
~~~~~~~~

:file:`tensorflow/contrib/legacy_seq2seq/python/ops/seq2seq.py` (version 1.11)

tensorflow版本升级之后(r1.3及之后？)把之前的 :py:mod:`tf.nn.seq2seq` 的代码迁移到了 :py:mod:`tf.contrib.legacy_seq2seq` 下面，这部分API估计以后会被遗弃，因为已经开发出了新的API放在 :py:mod:`tf.contrib.seq2seq` 下面，更加灵活。

文件函数结构如下, 共实现了6个seq2seq函数

- :py:func:`model_with_buckets`

  + seq2seq函数

    1. :py:func:`basic_rnn_seq2seq` ：最简单版本，输入和输出都是embedding的形式；最后一步的state vector作为decoder的initial state；encoder和decoder用相同的RNN cell， 但不共享权值参数；

      - :py:func:`rnn_decoder`

    2. :py:func:`tied_rnn_seq2seq` ：同1，但是encoder和decoder共享权值参数

    3. :py:func:`embedding_rnn_seq2seq` ：同1，但输入和输出改为id的形式，函数会在内部创建分别用于encoder和decoder的embedding matrix

      - :py:func:`embedding_rnn_decoder`

    4. :py:func:`embedding_tied_rnn_seq2seq` ：同2，但输入和输出改为id形式，函数会在内部创建分别用于encoder和decoder的embedding matrix

    5. :py:func:`embedding_attention_seq2seq` ：同3，但多了attention机制

      - :py:func:`embedding_attention_decoder`
      - :py:func:`attention_decoder`
      - :py:func:`attention`

    6. :py:func:`one2many_rnn_seq2seq`

  + loss函数

    * :py:func:`sequence_loss_by_example`
    * :py:func:`sequence_loss`

:py:func:`model_with_buckets` 的目的是为了减少计算量和加快模型计算速度，因为这部分代码比较古老——有些地方还在使用static_rnn()这种函数，其实新版的tf中引入dynamic_rnn之后就不需要这么做了。该方法为每个bucket都构造一个模型(这些模型参数共享)，然后训练时取相应长度的序列进行。其实这一部分可以参考现在的dynamic_rnn来进行理解，dynamic_rnn是对每个batch的数据将其pad至本batch中长度最大的样本，而bucket则是在数据预处理环节先对数据长度进行聚类操作。




:file:`tensorflow/contrib/eager/python/examples/generative_examples` 
:file:`tensorflow/contrib/eager/python/examples/nmt_with_attention` 
:file:`models/research/textsum` 
:file:`nmt` 



skflow(Scikit Flow)
---------------------

  位于 :file:`/tensorflow/contrib/learn/python/learn` 下面, 模仿Scikit Learn （ML & 数据挖掘工具包）。


代码结构
--------

- 代码分成op、module和model三个部分
- 定义op 等时使用修饰器加 scope
- 使用config 文件定义参数
- image summary 的输入用placeholder 而非数据，避免在save 的时候不断累加
- 将网络输入定义在train 的部分而非 init 中，便于reuse
- 保存log信息(logging)


训练记录
--------
  
1 数据准备
~~~~~~~~~~~~
  detection:  .pkl 文件 80,000 (32*32*3), 分批打包
  keypoints:  直接读取（218*178*3）

2 参数分析
~~~~~~~~~~~~
  1 face_detection

    +------+--+--+--+
    | 参数 |  |  |  |
    +------+--+--+--+
    |      |  |  |  |
    +------+--+--+--+
    |      |  |  |  |
    +------+--+--+--+


后续工作
--------

  | 训练(lr,opti)，tensorboard image、graph
  | 代码整理（开始前自动检测log 文件夹并删除、separate model、name）
  | retrain the model(c++)
  | detection 剩余数据
