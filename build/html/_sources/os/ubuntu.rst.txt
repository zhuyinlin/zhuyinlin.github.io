ubuntu
========

`askubuntu <https://askubuntu.com>`_

软件等安装
-----------

推荐软件:

.. code-block:: bash

    sudo apt-get install --no-install-recommends thunar  # 批量重命名

找不到动态链接库时，设置LD_LIBRARY_PATH

.. _nvidia-install-ubuntu:

nvidia etc..
^^^^^^^^^^^^^^^^^^

NVIDIA driver
""""""""""""""

- ubuntu 16.04

  修改 grub 文件

  .. code-block:: bash

     $ sudo cp -n /etc/default/grub /etc/default/grub.orig  # 备份 
     $ sudo vim /etc/default/grub  # 按后文修改文件
     $ sudo update-grub

  ::

        1. Comment the line GRUB_CMDLINE_LINUX_DEFAULT=”quiet splash”, by adding # at the beginning, which will disable the Ubuntu purple screen. 
        2. Change GRUB_CMDLINE_LINUX="" to GRUB_CMDLINE_LINUX=”text”, this makes Ubuntu boot directly into Text Mode.        
        3. Uncomment this line #GRUB_TERMINAL=console, by removing the # at the beginning, this makes Grub Menu into real black & white Text Mode (without background image)

  重启计算机, 进入字符界面(Ctrl+Alt+F1)

  .. code-block:: bash

     $ sudo shutdown -r now

  安装 driver

  .. code-block:: bash

     sudo service lightdm stop
     sudo ./<DRIVER>.run

     # 卸载
     ./<DRIVER>.run --uninstall
     # 或者
     sudo /usr/bin/nvidia-uninstall  # 未验证

      // 安装完重启，用以下命令查看
      nvidia-smi

  将 grub 文件改回来

  .. code-block:: bash

     sudo mv /etc/default/grub /etc/default/grub.textonly 
     sudo mv /etc/default/grub.orig /etc/default/grub 
     sudo update-grub
     sudo shutdown -r now

- ubuntu 18.04

   .. code-block:: bash

      // 添加源
      sudo add-apt-repository ppa:graphics-drivers/ppa
      sudo apt update

      // 安装
      ubuntu-drivers devices   // 查看本机gpu 及推荐的驱动
      sudo ubuntu-drivers autoinstall  // 自动安装
      sudo apt install nvidia-driver-390  // 或者根据前面推荐使用apt安装

      // 安装完重启，用以下命令查看
      nvidia-smi


CUDA Tookit
""""""""""""""
  到官网下载 cuda_x.x.x_linux.run, 运行

  .. code-block:: bash

      chmod +x cuda_x.x.x_linux.run
      sudo sh cuda_x.x.x._linux.run
      sudo sh cuda_x.x.x._linux.run --override  # cuda 9.0

      # 查看cuda版本
      nvcc --version 

  .. Warning::

          在安装完driver 后进行此项时，此项中的安装driver 选项选 No，以免旧的driver 覆盖新的driver
          
          对于 ubuntu 16.04 每次更新ubuntu内核后，记得重新安装CUDA driver

  若安装时提示错误::
      
      Missing recommended library: libGLU.so
      Missing recommended library: libX11.so
      Missing recommended library: libXi.so
      Missing recommended library: libXmu.so
      Missing recommended library: libGL.so

  安装依赖库即可

  .. code-block:: bash
    
      sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev \
          libxi-dev libgl1-mesa-glx libglu1-mesa libglu1-mesa-dev

  在 .bashrc 中添加环境变量

  .. code-block:: bash

      $ export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
      $ export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

      # 在ubuntu18.04 上，上述直接设置LD_LIBRARY_PATH 对一些第三方库的编译安装貌似不起作用，会找不到库
      # 推荐使用以下设置
      export CUDA_HOME=/usr/local/cuda
      export PATH=$PATH:/usr/local/cuda/bin
      # 另外cuda库要在/etc/ld.so.conf.d/cuda.conf 中设置，内容为：/usr/local/cuda/lib64
      # 设置完在命令行输入sudo ldconfig 使其生效

  查看cuda版本

  .. code-block:: bash

      nvcc --version 

  卸载

  .. code-block:: bash
     
      $ sudo /usr/local/cuda-xx/bin/uninstall_cuda_xx.pl

cuDNN
""""""
  下载并解压 cudnn，将其复制到cudn 安装目录（/usr/local/cuda）下

  [直接复制可能会使得lib64中的文件链接失效(可自己重新链接)，直接解压到/usr/local/cuda更合适]

  .. code-block:: bash

      $ tar xvzf cudnn-8.0-linux-x64-v5.1.tgz # 注意此处解压命令可能出错，改为xvf
      $ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
      $ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
      $ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

  测试 cuda 的例子，看是否安装成功

  .. code-block:: bash

      $ cd NVIDIA_CUDA-8.0_Samples/5_Simulations/nbody
      $ make
      $ ./nbody -benchmark -numbodies=256000 -device=0

.. _python-install-ubuntu:

python
^^^^^^^
  .. code-block:: bash

      # 安装
      sudo apt-get install python-dev  // for python
      sudo apt-get install python3-dev  // for python3

      # 1.基于用户修改 Python 版本：
      alias python='/usr/bin/python3.4'
      source ~/.bashrc

      # 2.系统级修改 Python 版本
      update-alternatives --list python # 查看
      update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1 
      update-alternatives --install /usr/bin/python python /usr/bin/python3.4 2 # 如果查看发现没有，可人工添加
      update-alternatives --config python # 替换

  找不到python库时，设置PYTHONPATH

.. _tf-install-ubuntu:

Tensorflow
^^^^^^^^^^^^^^^^^

若要安装GPU版本，需先安装 :ref:`nvidia-install-ubuntu` 

Requirements
""""""""""""""

- NVDIA driver 390
- gcc 6
- g++ 6
- cuda 9.0 (cuda_9.0.176_384.81_linux.run)
- cudnn v7.1 (cudnn-9.0-linux-x64-v7.1.tgz)

- python
- pip
- tensorflow 1.8/1.9

Install
""""""""""
直接从 pip 安装 安装会缺少部分功能，推荐从源码安装

从源码安装时，configure 过程中，如果gcc，python 等的版本是通过update-alternatives 管理的，那么配置时应该输入相应版本所在位置而不是默认路径；另外在bazel build时，可能需要添加参数 --action_env=LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/lib64/stubs:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH

18.04 上 r1.8 版:
gcc=4.8
cuda库要在/etc/ld.so.conf.d/cuda.conf 中设置，直接设置LD_LIBRARY_PATH 会找不到

装gpu版本时，可通过以下代码验证后文相关库等是否正确安装(ubuntu18.04版)

.. code-block:: python

   from tensorflow.python.client import device_lib
   device_lib.list_local_devices()

当安装了 pip 版和源码版时，系统首先检测到的是 pip 版， 若需将其卸载，做以下步骤

.. code-block:: bash

   sudo pip show tensorflow 
   sudo pip uninstall tensorflow
   

若要卸载从源码安装的版本，则还要

.. code-block:: bash

   cd TENSORFLOW_ROOT/_python_build
   python setup.py develop --uninstall


下载建议使用

.. code-block:: bash

   git clone --recursive 
    

后期可能出现的问题
""""""""""""""""""
- 驱动实效
- tensorflow 更新造成的一些不匹配
- bazel版本不匹配
- pip 多版本造成混乱 

.. _caffe-install-ubuntu:

caffe
^^^^^^^

安装依赖库(一):
    .. code-block:: bash

        sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
        sudo apt-get install --no-install-recommends libboost-all-dev

安装BLAS:

    .. code-block:: bash

        sudo apt-get install libatlas-base-dev
      
    可以安装OpenBLAS 或 MKL，以提升CPU性能，但是要修改caffe中Makefile文件…

安装python
    
安装matlab
    详见： `Ubuntu14.04安装Matlab2014a <http://blog.csdn.net/u011762313/article/details/47211393>`_
    
    如果不使用matcaffe接口，可以不装    

安装opencv    
    详见： `Ubuntu14.04安装OpenCV3.0 <http://blog.csdn.net/u011762313/article/details/47263845>`_ ，这里我参考官网,下载的是2.4的最新版
    
    注：opencv必须安装，且版本为>=2.4或3.0      
    
    a. 安装依赖库

       .. code-block:: bash
        
           sudo apt-get install build-essential
           sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
           sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
    b. 下载opencv

       .. code-block:: bash
 
           mkdir opencv
           cd opencv
           #上官网直接下载文件，或
           git clone https://github.com/Itseez/opencv.git
    c. 安装

       .. code-block:: bash

           unzip 下载的zip文件
           cd 解压的文件夹
           mkdir release
           cd release
           cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local .. 
           make
           sudo make install
    d. 编译sample程序     

       .. code-block:: bash

           cd ~/opencv/samples 
           sudo cmake .  
           sudo make -j $(nproc)
           ＃可运行samples／cpp下的程序测试

           # 若出现错误：OpenCV is considered to be NOT FOUND
           # 1) clean cmake cache  2) cmake 时加 -D OpenCV_DIR=/path/to/opencv/build_dir 

           # 若出现错误：/usr/bin/ld: 找不到 -lopencv_dep_cudart
           # 在cmake 时加 -DCUDA_USE_STATIC_CUDA_RUNTIME=OFF 或者 
           # 创建一个软链接 sudo ln -s /path/to/cuda/lib64/libcudart.so /usr/lib/libopencv_dep_cudart.so

安装依赖库（二）

  .. code-block:: bash

      sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev

下载并编译Caffe
    1. 下载

       .. code-block:: bash
           
           $ git clone git://github.com/BVLC/caffe.git

       如果安装的是opencv3.0

           a. 修改Makefile，在             
              
              LIBRARIES += glog gflags protobuf leveldb snappy \
              lmdb boost_system hdf5_hl hdf5 m \
              opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs            
              
              处加入后面的opencv_imgcodecs，因为opencv3.0.0把imread相关函数放到imgcodecs.lib中了（原来是imgproc.lib）          
           b. 修改caffe/examples/cpp_classification/classification.cpp文件，加入 ::          
              
              #include <opencv2/imgproc/types_c.h>
              #include <opencv2/objdetect/objdetect_c.h>

       否则会出现”CV_BGR2GRAY”的错误

    2. 编译Caffe
       
       .. code-block:: bash

           cd ~/caffe
           cp Makefile.config.example Makefile.config
           sudo gedit Makefile.config
        
       修改Makefile.config文件::

                 //如果你不使用GPU的话，就将 
                 # CPU_ONLY := 1 
                 修改成： 
                 CPU_ONLY := 1 
               
                 //若使用cudnn，则将 
                 # USE_CUDNN := 1 
                 修改成：
                 USE_CUDNN := 1 
               
                 //若使用的opencv版本是3的，则将 
                 # OPENCV_VERSION := 3 
                 修改为： 
                 OPENCV_VERSION := 3 
               
                 //若要使用python来编写layer，则需要将 
                 # WITH_PYTHON_LAYER := 1 
                 修改为 
                 WITH_PYTHON_LAYER := 1 
               
                 //重要的一项 
                 将# Whatever else you find you need goes here.下面的 
                 INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include 
                 LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib
                 修改为：
                 INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial 
                 LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial 
                 //这是因为ubuntu16.04的文件包含位置发生了变化，尤其是需要用到的hdf5的位置，所以需要更改这一路径

      .. code-block:: bash

          cd python 
          for req in $(cat requirements.txt); do pip install $req; done
          \\如果发现执行上述代码后，终端中有很多红字，一堆的错误之类的，那不管是什么错误都执行下面一句话： 
          for req in $(cat requirements.txt); do sudo -H pip install $req --upgrade; done 
          \\执行完上面这句话后应该就不会有很多红字错误了
          cd ..
          make all
          make test
          make runtest

配置python:
    a. 安装依赖库

       .. code-block:: bash

           $ sudo apt-get install python-numpy python-scipy python-matplotlib python-sklearn python-skimage python-h5py python-protobuf python-leveldb python-networkx python-nose python-pandas python-gflags cython ipython
           $ sudo apt-get install protobuf-c-compiler protobuf-compiler
    b. 编译

       .. code-block:: bash

           cd ~/caffe
           make pycaffe
           make distribute
    c. 添加~/caffe/python到$PYTHONPATH

       .. code-block:: bash
               
           $ sudo gedit /etc/profile
           # 末尾添加： export PYTHONPATH=/path/to/caffe/python: $PYTHONPATH
           # 用完整路径，不要用~
           $ source /etc/profile # 使之生效
    d. 测试是否可以引用

       .. code-block:: bash

           $ python
           Python 2.7.6 (default, Jun 22 2015, 17:58:13) 
           [GCC 4.8.2] on linux2
           Type "help", "copyright", "credits" or "license" for more information.
           >>> import caffe
           >>> 

配置matcaffe  
    a. gcc降级（Ubuntu14.04自带的gcc版本是4.8，MATLAB2014a支持的最高版本为4.7x。因此，需要安装gcc4.7，并给gcc降级）        
       
       详见: `Ubuntu中update-alternatives命令（版本切换） <http://blog.csdn.net/u011762313/article/details/47324839>`_

       .. code-block:: bash

           sudo apt-get install gcc-4.7 g++-4.7 g++-4.7-multilib gcc-4.7-multilib
           sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.7 100 
           sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 50 
           sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.7 100 
           sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 50
           sudo update-alternatives --install /usr/bin/cpp cpp-bin /usr/bin/cpp-4.7 100
           sudo update-alternatives --install /usr/bin/cpp cpp-bin /usr/bin/cpp-4.8 50
           # 验证gcc默认版本
           $ gcc -v 

    b. 编译

       .. code-block:: bash
 
           $ cd ~/caffe
           # 修改Makefile.config文件，MATLAB_DIR :=/usr/local/MATLAB/R2014a
           $ make matcaffe

    c. 添加工作空间

       .. code-block:: bash
 
           $ sudo matlab -nodesktop -nosplash
           >>> addpath ~/caffe/matlab
           >>> savepath

错误集锦      
    make all 发生如下错误

    .. code-block:: bash

        /usr/bin/ld: cannot find -lcblas
        /usr/bin/ld: cannot find -latlas
        collect2: error:ld returned 1 exit status
        make: *** [.build_release/lib/libcaffe.so.1.0.0-rc3] 
        Error 1     
    
    安装如下库

    .. code-block:: bash  

        sudo apt-get install libatlas-dev
        sudo apt-get install liblapack-dev
        sudo apt-get install  libatlas-base-dev

bazel 
^^^^^^

  卸载
  
  .. code-block:: bash

      sudo  ./bazel-xxx-installer-linux-x86_64.sh --uninstall

java 
^^^^^^^

  .. code-block:: bash

      # search
      apt-cache search jdk
      
      # install 
      sudo apt-get install openjdk-8-jdk
      sudo apt-get install openjdk-8-source #this is optional, the jdk source code

      # 设置环境变量(二选其一，注意修改到自己的路径)
      export JAVA_HOME=/usr/lib/jvm/java-8-openjdk
      export PATH=$PATH:/usr/lib/jvm/java-8-openjdk/bin

.. _cmake-install-ubuntu:

cmake
^^^^^^^

  .. code-block:: bash

      # ubuntu16.04
      sudo add-apt-repository ppa:george-edison55/cmake-3.x
      sudo apt-get update
      sudo apt-get upgrade 
      cmake --version

      # ccmake
      sudo apt-get install cmake-curses-gui

opengl
^^^^^^^

  .. code-block:: bash

      sudo apt-get install build-essential libgl1-mesa-dev
      sudo apt-get install freeglut3-dev
      sudo apt-get install libglew-dev libsdl2-dev libsdl2-image-dev libglm-dev libfreetype6-dev

vtk
^^^^^^^

  .. code-block:: bash

      # 安装VTK：
      # 1.安装依赖包：cmake, opengl, ccmake
      sudo apt-get install cmake-curses-gui
      # 2.下载vtk并配置
      mkdir VTK-build
      cd VTK-build
      ccmake ..  # 按c配置，按g生成, 
      # 注意设置python wrapper，python版本->这跟安装路径有关
      # 3.编译安装
      make
      sudo make install
      # 安装python wrapper (配置时要勾选python wrapper)
      cd Wrappers/Python
      make
      sudo make install
      # 4.环境变量设置
      export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
      export PYTHONPATH=/usr/local/lib/pythonXXX/site-packages:$PYTHONPATH

      # 验证
      cd <VTK path>/Examples/Tutorial/Step1/CXX
      cmake .
      make
      ./Cone

      # 卸载VTK：
      cd /usr/local/include
      cd /usr/local/lib
      sudo rm -r libvtk*//删掉以libvtk开头的所有文件


itk
^^^^^^^
  
  安装ITK同VTK类似。注意添加相关设置
  
  先设置::

     ITK_WRAP_PYTHON             ON
     ITK_LEGACY_SILENT           ON
     Module_BridgeNumPy          ON
     PYTHON_EXECUTABLE           设置好python路径，这决定了python package 的安装路径, 先查看/usr/local/lib下python目录名，选取相关的bin目录(好像不起作用)
     ITKVtkGLUE                  ON  

  然后设置::

      ITK_WRAP_DIMS               2;3;4
      ITK_WRAP_VECTOR_COMPONENTS  2;3;4

      ITK_WRAP_float              ON
      ITK_WRAP_double             ON
      ITK_WRAP_signed_char        ON
      ITK_WRAP_signed_long        ON
      ITK_WRAP_signed_short       ON
      ITK_WRAP_unsigned_char      ON
      ITK_WRAP_unsigned_long      ON
      ITK_WRAP_unsigned_short     ON
      ITK_WRAP_vector_float       ON
          WRAP_<data-type>                Select yourself which more to activate.


  .. code-block:: bash

      # 验证
      cd <ITK path>/Examples/Installation
      cmake .
      make
      ./HelloWorld

  记得设置python路径和ld路径

fltk
^^^^^^^

  .. code-block:: bash

      sudo apt-get install build-essential xorg-dev libx11-dev libcairo2-dev
      # 解压文件并进入目录
      ./configure [options]
      make 
      sudo make install

      # 测试
      # 到官网复制tutorial中的代码，新建文件
      # gcc hello.cxx `fltk-config --ldflags`  # 可在命令行查询 fltk-config 的相关命令

.. _opencv-install-ubuntu:

opencv 
^^^^^^^

  先参考 `官网 <https://docs.opencv.org/master/d9/df8/tutorial_root.html>`_ 安装需要的库

  下载zip 文件解压

  .. code-block:: bash

          cd ~/opencv
          mkdir release
          cd release
          cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..
          # -D BUILD_TIFF=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=ON -D WITH_QT=ON -D WITH_GTK=ON -D WITH_OPENGL=ON -D BUILD_SHARED_LIBS=ON 等参数待理解 
          # 1.要编译java库，首先要配好java 环境及ant，然后加上 -D BUILD_SHARED_LIBS=OFF 或者 -DBUILD_TESTS=OFF; 注意查看 To be built 中是否有java
          # 2.如果电脑中有CUDA，默认会安装CUDA版本，如果不想要，就加 -DWITH_CUDA=OFF
          # 3.如果要加入contribe 模块，-D OPENCV_EXTRA_MODULES_PATH=<path/to/opencv_contrib-xxx/modules>
          # 4.如果要加vtk，WITH_VTK=ON, BUILD_opencv_viz=ON, VTK_DIR设置到vtk build的文件夹,

          # -j4 代表运行逻辑核数
          make -j4 
          sudo make install -j4 

          # 卸载
          make uninstall
          cd ..
          sudo rm -r release
          sudo rm -r /usr/local/include/opencv2 /usr/local/include/opencv /usr/include/opencv /usr/include/opencv2 /usr/local/share/opencv /usr/local/share/OpenCV /usr/share/opencv /usr/share/OpenCV /usr/local/bin/opencv* /usr/local/lib/libopencv

  安装多版本时， 将 CMAKE_INSTALL_PREFIX 设为不同值即可, 在使用时记得设置

  .. code-block:: bash

          export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/<opencv_path>/lib
          export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/<opencv_path>/share/OpenCV/Java // 如果编译了java的话
          export PKG_CONFIG_PATH=/usr/local/<opencv_path>/lib/pkgconfig
          // 注意编译过程中使用的release文件夹不能改名，因为安装目录下的很多文件是链接到这里的

jsoncpp
^^^^^^^^^

`编译安装jsoncpp <https://github.com/open-source-parsers/jsoncpp/wiki/Building)>`_

.. code-block:: bash
   
   sudo apt-get install libjsoncpp-dev
   # 或者
   git clone https://github.com/open-source-parsers/jsoncpp.git
   cd jsoncpp
   mkdir build && cd build
   cmake -DCMAKE_BUILD_TYPE=release -DBUILD_STATIC_LIBS=OFF -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_INCLUDEDIR=include/jsoncpp -DARCHIVE_INSTALL_DIR=. -G "Unix Makefiles" ../..
   make 
   make install

记得添加路径到 .bashrc 文件

genymotion
^^^^^^^^^^

  先 `安装 virtualbox <https://www.virtualbox.org/wiki/Linux_Downloads>`_

  .. code-block:: bash

    sudo vim /etc/apt/sources.list  # 在文件末尾加入 deb http://download.virtualbox.org/virtualbox/debian xenial contrib
    wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
    sudo apt-get update
    sudo apt-get install virtualbox-5.2
    sudo apt-get install dkms

  再 `安装genymotion <https://docs.genymotion.com/Content/01_Get_Started/Installation.htm>`_

.. _jenkins-install-ubuntu:

jenkins
^^^^^^^^

`官方安装文档 <https://jenkins.io/doc/book/installing/>`_

Requirement
""""""""""""""

- java
- docker

Install 
""""""""""
可以参考官网使用离线方式安装，或者采用离线安装的方式
安装 docker， jenkins

.. code-block:: bash

   wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
   # FOR STABLE JENKINS VERSION RUN:
   sudo apt-add-repository "deb https://pkg.jenkins.io/debian-stable binary/"
   # FOR LATEST JENKINS VERSION RUN:
   sudo apt-add-repository "deb http://pkg.jenkins-ci.org/debian binary/"
   sudo apt-get update
   sudo apt-get install jenkins

Uninstall
""""""""""

.. code-block:: bash

   // 卸载jenkins 并移除相关依赖
   sudo apt-get remove jenkins
   sudo apt-get remove --auto-remove jenkins
   // 清除各种相关配置和数据
   sudo apt-get purge jenkins
   sudo apt-get purge --auto-remove jenkins

      
安装mac主题
-----------------

`打造完美的 Ubuntu16.04 开发环境 <https://laravel-china.org/topics/2723/create-a-perfect-ubuntu1604-development-environment-continuous-update>`_

 `mac壁纸 <http://pan.baidu.com/s/1skQCq2T>`_


终端
-----
1. 安装完首先要更新源：

   .. code-block:: bash
    
        sudo apt-get  update #更新
        sudo apt-get upgrade #升级
   
2. 添加用户，并使其有 root 权限

   .. code-block:: bash
        
        sudo useradd -r <name>   #  -r 参数建立系统用户
        sudo usermod -g root <name>  # 将用户划分到 root 权限组下
        # 同时修改 /etc/sudoer 文件， 添加 <name>  ALL=(ALL:ALL)ALL  # 添加ROOT权限
        passwd <name>       # 设置密码

        # 更改文件夹的用户及组
        sudo chown -R <username/groupname> <folder>


3. 修改更新源：

   .. code-block:: bash
    
        sudo  cp /etc/apt/sources.list /etc/apt/sources.list.backup  #备份
        sudo  gedit /etc/apt/sources.list  #用编辑器打开文件
        接下来就是删除文件中的内容，加入新的更新源

4. 开启自动补全功能
   
   终端输入

   .. code-block:: bash

       $ nano .inputrc

   然后在打开的文件中粘贴::
           
           set completion-ignore-case on
           set show-all-if-ambiguous on
           TAB: menu-complete


5. 压缩／解压

   .. code-block:: bash
    
       # ZIP 
       # 优点: 不同的操作系统平台上使用;
       # 缺点: 支持的压缩率不是很高
       zip -r <archive_name>.zip <directory_to_compress>   # 压缩
       unzip <archive_name>.zip   # 解压

       # tar
       # 只消耗非常少的CPU以及时间去打包文件，仅仅只是一个打包工具，并不负责压缩
       tar -cvf <archive_name>.tar <directory_to_compress>  # 压缩
       tar -xvf <archive_name>.tar -C <target_dir>  # 解压

       # tar.gz
       # 压缩时不会占用太多CPU，而且可以得到一个非常理想的压缩率
       tar -zcvf <archive_name>.tar.gz <directory_to_compress>  # 压缩
       tar -zxvf <archive_name>.tar.gz -C <target_dir>  # 解压

       # tar.bz2
       # 所有方式中压缩率最好的, 占用更多的CPU与时间
       tar -jcvf <archive_name>.tar.bz2 <directory_to_compress>  # 压缩
       tar -jxvf <archive_name>.tar.bz2 -C <target_dir>  # 解压




6. zsh 和 oh-my-zsh

   .. code-block:: bash

      # 可先查看本机的shell
      $ cat /etc/shells
      $ echo $SHELL

      $ sudo apt-get install zsh
      $ sudo chsh -s /bin/zsh # 重新启动生效
      如果是在非主用户时，需要采用如下命令修改shell
      $ sudo usermod -s /bin/zsh <user name>
      
      # 安装 oh-my-zsh, 配置 .zshrc

   若安装 agnoster 主题，需修改匹配 `字体 <https://github.com/powerline/fonts.git>`_ , 安装好后在 terminal 的preferences 中设置
 

ssh 连接
-----------
connet to host [hostname] port 22: No route to host 出现的可能原因::

        1. 网络连接方式 wifi ／wan
        2. 防火墙设置


端口映射到本地：

.. code-block:: bash

   ssh -N -f -L localhost:8888:localhost:8889 remote_user@remote_host
   # or
   ssh -NfL localhost:8888:localhost:8889 remote_user@remote_host

   #  -N 告诉SSH没有命令要被远程执行；
   #  -f 告诉SSH在后台执行；
   #  -L 是指定port forwarding的配置，远端端口是8889，本地的端口号的8888。
   # remote_user@remote_host 用实际的远程帐户和远程地址替换

   # 当有多个端口需要映射时，加多个 -L 参数即可，如
   ssh -NfL localhost:8888:localhost:8889 -L localhost:<port>:localhost:<port> remote_user@remote_host


sshfs 连接
-----------
.. code-block:: bash

   sudo apt-get install sshfs  # 安装
   sudo sshfs -o allow_other <user@hostname:folder> <local mount folder>  # 挂载
   sudo fusermount -u <local mount folder>  # 卸载

   # sshfs 连接典型例子（断网后自动连接）
   sudo sshfs -o allow_other xshine@192.168.1.102:Documents/projects_tf ~/remote_home/alienware -o reconnect

快捷键
-----------
::

        Ctrl + Alt + T // 打开终端
        Ctrl + Shift + T //新建终端标签


一些技巧
-----------

软链接
  .. code-block:: bash

    ln -s 原始文件夹 目标文件夹 # 对链接后文件的修改是否会影响到原始文件

查看文件路径
  .. code-block:: bash

    which appname
        
错误解决办法
--------------

卷boot仅剩余0字节的磁盘空间:
  Ubuntu系统经过升级之后，之前的Linux内核依然会存在boot分区中，造成boot分区提示硬盘不足，此时我们可以通过删除之前的linux内核，仅仅保留当前正在使用的内核解决，步骤如下：

  1. 查看已安装的 linux-image 各版本
   
     .. code-block:: bash

        $ dpkg --get-selections |grep linux-image

  2. 查看当前使用的版本
  
     .. code-block:: bash

        $ uname -a

  3. 卸载掉其他不用的版本
   
     .. code-block:: bash

        $ sudo apt-get purge linux-image-#.#.#-##-generic 

  4. 卸载完后查看boot分区的空间使用情况

     .. code-block:: bash

        $ df

无法上网的问题：
  更新驱动

/bin/bash^M:bad interpreter
  用vim 打开脚本文件，查看文件格式： ``:set ff?`` , 如果是 ``doc`` ,通过 ``set fileformat=unix``, 再运行 




