C++
======

mac下终端调试C程序
------------------

.. figure:: /_static/programing/mac_c_debug.png
   :name: mac_c_debug
   :align: center

ubuntu下运行opencv程序的两种方式
--------------------------------

1. 命令行

   .. code-block:: bash

       g++ <script name> -o <target name> `pkg-config  --cflags --libs opencv` 

   在上面的编译命令中我们其实用到了一个工具“pkg-config”，它主要有以下几个功能：
   
   - 检查库的版本号。如果所需要的库的版本不满足要求，它会打印出错误信息，避免链接错误版本的库文件。
   - 获得编译预处理参数，如宏定义，头文件的位置。
   - 获得链接参数，如库及依赖的其它库的位置，文件名及其它一些连接参数。
   - 自动加入所依赖的其它库的设置
　　
   所有有了这个工具之后我们的编译就很方便了（不过在此之前你要确保你安装的OpenCV的安装链接库文件的目录下有一个pkgconfig文件夹，在该文件夹里面有个opencv.pc的文件，其实这就是pkg-config下OpenCV的配置文件）。

　 使用pkg-config时，选项--cflags 它是用来指定程序在编译时所需要头文件所在的目录,选项 --libs则是指定程序在链接时所需要的动态链接库的目录。例如我下面这张图就显示了我电脑上OpenCV的相关目录。


   安装多版本opencv时，需要修改默认的pkg-config路径:: 

       export PKG_CONFIG_PATH=/usr/local/opencv/3.1.0/lib/pkgconfig 
       export LD_LIBRARY_PATH=/usr/local/opencv/3.1.0/lib

   `Ubuntu 多版本Opencv安装配置教程 <https://blog.csdn.net/qq_34952119/article/details/71501652>`_

2. cmake 方式

   1) 新建一个目录用于存放我们的代码和程序中要处理的相关图片
   2) 添加cmake工具编译时所需的文件CMakeLists.txt (注：这个文件你可以到你的OpenCV源代码解压出来的文件夹下的/samples/c/example_cmake/文件夹下拷过来，然后再做修改)
      
      .. code-block:: cmake

          PROJECT(OpenCV_Example)           //这是建立一个工程项目（类似于我们VS中建立C++项目一样），括号里面时工程名,工程名我们可以任意给，最后程序编译出来的可执行文件就是这个名字
          CMAKE_MINIMUM_REQUIRED(VERSION 2.6)　　　　//这是对CMake工具最低版本要求，这里我们要检查下我们的CMake工具的版本信息，我们可以使用命令“cmake --version”查看
          if(COMMAND cmake_policy)　　　　　　　　　　　　
                cmake_policy(SET CMP0003 NEW)
          endif(COMMAND cmake_policy)
 
          FIND_PACKAGE( OpenCV REQUIRED )   //这是cmake用来查找opencv包用的，不用改

          # Declare the target (an executable)
          ADD_EXECUTABLE(OpenCV_Example  Image_show.c)      //这里括号里面的两个参数分别是工程项目名和我们要编译文件名的意思，记住中间一空格键隔开

          TARGET_LINK_LIBRARIES(OpenCV_Example ${OpenCV_LIBS})  //这是我们链接到OpenCV库的环节，我们只要更改前面第一个参数位我们的工程项目名即可

          #MESSAGE(STATUS "OpenCV_LIBS: ${OpenCV_LIBS}")     //好了，就修改这么点东西，保存，关闭。

      若找不到OpenCV_DIR，添加设置::

          set(OpenCV_DIR "~/opencv-2.4.13/release")  # 这个路径是安装时，自己建的release文件

      若安装了多个版本，在使用时需要在项目的CMakeList.txt中添加OpenCVConfig.cmake的路径::
        
          set(CMAKE_PREFIX_PATH "/usr/local/opencv/opencv2.4.13/share/OpenCV")

   3) 输入命令"cmake ."对当前的工程进行编译 
   4) make 生成可执行文件





gcc生成及使用静态库
-------------------

1. 生成 test.o 目标文件 

   .. code-block:: bash
   
       gcc -c test.c -o test.o 

2. 使用 :code:`ar` 将 test.o 打包成 libtest.a 静态库
   
   .. code-block:: bash
   
       ar rcs -o libtest.a test.o

3. 可以使用 :code:`ar t` 参数查看 libtest.a 文件中包含哪些文件 
   
   .. code-block:: bash
   
       ar t libtest.a 

4. 编译 main.c ，并使用 libtest.a 静态。链接时 :code:`-l` 参数后不加空格指定所需链接的库， :code:`ld` 会以 libtest 作为库的实际名字（例如 :code:`-lm` 参数，实际表示链接 libm 库，也就是数学库） 
   
   .. code-block:: bash
   
       gcc -o app_static main.c -L. -ltest
       # 或着
       gcc -o app_static main.c libtest.a

5. 运行app_static
   
   .. code-block:: bash
   
       ./app_static

6. 使用 :code:`readelf` 查看app_static的符号表，观察各函数是否处于 app_static 的 **.text** 段中 
   
   .. code-block:: bash
   
       readelf -a app_static
   
.. Note::
    对比 `section headers` 中指出的 **.text** 代码段的编号与 `symbol table` 的 **.symtab** 中各函数所处编号，确认其是否在 **.text** 代码段中。从而确定静态库经过链接后对应的函数代码是否已加入可执行程序代码段中。

gcc生成及使用动态库
-----------------------

1. 生成 test.o 目标文件 
   
   .. code-block:: bash
   
       gcc -c -o test.o -fPIC test.c 
       
   此处需要添加 :code:`-fPIC` 参数，该参数用于生成位置无关代码以供生成动态库使用

2. 使用 :code:`-shared` 参数生成动态库 
   
   .. code-block:: bash
    
       gcc -shared -o libmyshare.so test.o 

上述两个命令可以合在一块 :code:`gcc -shared -fPIC -o libmyshare.so test.c` 

3. 编译main.c，使用 libmyshare.so动态库 

   .. code-block:: bash
   
       gcc -o app_share main.c -L. -lmyshare

4. 使用 :code:`ldd` 命令查看app_share使用的动态库 
   
   .. code-block:: bash
    
       ldd app_share 
       
   若提示 `libXXX无法找到` ，则使用 :code:`export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH` 将当前目录加入 :code:`LD_LIBRARY_PATH` 变量中

5. 执行app_share 
   
   .. code-block:: bash
   
       ./app_share

另一种编译main.c，并链接libXXX.so的方式如下（该方式通过./libmyshare.so直接指定使用当前目录下的libmyshare.so文件) 

.. code-block:: bash
   
   gcc -o app_share main.c ./libmyshare.so

.. topic:: ``-l`` 参数和 ``-L`` 参数
  
   ``-l`` 参数就是用来指定程序要链接的库， ``-l`` 参数紧接着就是 **库名** 。

   .. Note:: 
           那么库名跟真正的 **库文件名** 有什么关系呢？就拿数学库来说，他的库名是m，他的库文件名是libm.so，很容易看出，把库文件名的头lib和尾.so去掉就是库名了。

   当需要用到第三方库libtest.so时，只要把libtest.so拷贝到 ``/usr/lib`` 里，编译时加上 ``-ltest`` 参数，我们就能用上libtest.so库了（当然要用libtest.so库里的函数，我们还需要与 libtest.so配套的头文件）。
  
   放在 ``/lib`` 和 ``/usr/lib`` 和 ``/usr/local/lib`` 里的库直接用 ``-l`` 参数就能链接了，但如果库文件没放在这三个目录里，而是放在其他目录里，这时我们只用-l参数的话，链接还是会出错，出错信息大概是：“/usr/bin/ld: cannot find -lxxx”，也就是链接程序ld在那3个目录里找不到libxxx.so，这时另外一个参数 ``-L`` 就派上用场了，比如常用的X11的库，它在 ``/usr/X11R6/lib`` 目录下，我们编译时就要用 ``-L /usr/X11R6/lib -lX11`` 参数， ``-L`` 参数跟着的是库文件所在的目录名。再比如我们把libtest.so放在/aaa/bbb/ccc目录下，那链接参数就是-L /aaa/bbb/ccc -ltest。
  
   另外，大部分libxxxx.so只是一个链接，以RH9为例，比如libm.so它链接到/lib/libm.so.x，/lib/libm.so.6又链接到/lib/libm-2.3.2.so，如果没有这样的链接，还是会出错，因为ld只会找libxxxx.so，所以如果你要用到xxxx库，而只有libxxxx.so.x或者libxxxx-x.x.x.so，做一个链接就可以了 ``ln -s libxxxx-x.x.x.so libxxxx.so`` 。
  
.. topic:: ``-include`` 和 ``-I`` 参数
   
   ``-include`` 用来包含 **头文件** ，但一般情况下包含头文件都在源码里用 ``#include xxxxxx`` 实现， ``-include`` 参数很少用。
  
   ``-I`` 参数是用来指定 **头文件目录** ， ``/usr/include`` 目录一般是不用指定的，gcc知道去那里找，但是如果头文件不在/usr/include里我们就要用 ``-I`` 参数指定了，比如头文件放在 ``/myinclude`` 目录里，那编译命令行就要加上 ``-I /myinclude`` 参数了，如果不加你会得到一个 `"xxxx.h: No such file or directory"` 的错误。 ``-I`` 参数可以用相对路径，比如头文件在当前目录，可以用 ``-I.`` 来指定。

lldb 调试
---------
`使用LLDB调试程序 <http://www.cocoachina.com/ios/20150819/11558.html>`_

+ 常用命令
  
  ``std::vector`` 查看单个元素不能直接用 ``[i]`` --> `原因 <http://stackoverflow.com/questions/39680320/printing-debugging-libc-stl-with-xcode-lldb>`_

  .. code-block:: bash

         expr <vec_name> 
         frame var <vec_name>[i]    
         frame var -L <vec_name>[i]


gdb调试
-------
+ 调试步骤
  
  1. 生成可执行文件 
     
     .. code-block:: bash
     
         g++ -g -o <exe_name> <filename.cc> 
         
     .. Note:: 
             必须使用 ``-g`` 参数，编译会加入调试信息，否则无法调试执行文件, 因为你将看不见程序的函数名、变量名，所代替的全是运行时的内存地址。

  2. 启动调试

     .. code-block:: bash

         gdb <exe_name>          
         gdb <exe_name> core      // 用gdb同时调试一个运行程序和core文件，core是程序非法执行后core dump后产生的文件。   
         gdb <exe_name> <PID>     // 如果程序是一个服务程序，那么可以指定这个服务程序运行时的进程ID。gdb会自动attach上去，并调试他。exe_name应该在PATH环境变量中搜索得到。
                                  // 可用于调试已运行的程序。先用gdb <program>关联上源代码，并进行gdb，在gdb中用attach命令来挂接进程的PID。并用detach来取消挂接的进程。

+ 常用调试命令

  可参考 `本篇博客 <http://blog.csdn.net/haoel/article/details/2880>`_

  .. code-block:: bash

      set args help  //加上可执行文件需要的参数
      show args  //看argc[1]到argc[N]的参数

      // 直接回车表示，重复上一次命令
      l(ist) # 查看源文件

      b(reak) <line number>  <condition> # 设置断点
      i(nfo) b(reak)  # 查看断点信息
      disable <breakpoint num>   # 使断点无效
      enable <breakpoint num>  # 使断点有效
      clear <breakpoint num>  # 取消断点

      watch <var_name>  # 对变量设置断点，当变量变化时暂停运行
      watch <var_name> <condition>

      r(un)  # 运行
      s(tep)  # 单步调试

      p(rint) <var_name>   # 查看变量
      whatis <var_name>  # 查看变量数据类型
      set variable <var_name> = <value> # 设置变量值

      bt  # 查看函数堆栈
      finish  # 退出函数
      c(ontinue)  # 继续运行到下一个断点或主函数结束
      q(uit)  # 退出调试



      shell <cmd>  // 调用UNIX的shell来执行<cmd>
      make <make-args>     // 重新build自己的程序。

      // 程序运行参数 
      set args     // 指定运行时参数
      show args   // 查看设置好的运行参数
      
      // 运行环境
      path <dir>  // 设定程序的运行路径
      show paths  // 查看程序的运行路径
      
      set environment varname [=value]  // 设置环境变量
      show environment [varname]        // 查看环境变量
      
      // 工作目录
      cd <dir>    // 相当于shell的cd命令
      pwd 显示当前的所在目录
      
      // 程序的输入输出
      info terminal 显示你程序用到的终端的模式
      tty // 指写输入输出的终端设备

+ 一些技巧

  - gdb的命令可以使用help命令来查看，gdb的命令很多，gdb把之分成许多个种类。help命令只是例出gdb的命令种类，如果要看种类中的命令，可以使用help <class> 命令，如：help breakpoints，查看设置断点的所有命令。也可以直接help <command>来查看命令的帮助。
  - 在Linux下，你可以敲击两次TAB键来补齐命令的全称，如果有重复的，那么gdb会把其例出来。
  - 设置显示选项

    .. code:: 

            set print address <on/off>   // 打开地址输出, 当程序显示函数信息时，GDB会显出函数的参数地址。默认on
            set print array <on/off>     // 打开数组显示，打开后当数组显示时，每个元素占一行;如果不打开的话，每个元素则以逗号分隔。默认off
            set print elements <number-of-elements>   // 指定一个<number-of-elements>来指定数据（数组）显示的最大长度，当到达这个长度时，GDB就不再往下显示了。如果设置为0，则表示不限制。
            set print null-stop <on/off>  //显示字符串时，遇到结束符则停止显示。默认off
            set print pretty <on/off>      // 如果打开printf pretty这个选项，那么当GDB显示结构体时会比较漂亮
            set print sevenbit-strings <on/off>    // 设置字符显示，是否按“/nnn”的格式显示，如果打开，则字符串或字符数据按/nnn显示，如“/065”。       
            set print union <on/off>       // 设置显示构体时，是否显式其内的联合体数据。
            set print object <on/off>      // 在C++中，如果一个对象指针指向其派生类，如果打开这个选项，GDB会自动按照虚方法调用的规则显示输出，如果关闭这个选项的话，GDB就不管虚函数表了。默认off
            set print static-members <on/off>   // 当显示一个C++对象中的内容时，是否显示其中的静态数据成员。默认on
            set print vtbl <on/off>        // 当此选项打开时，GDB将用比较规整的格式来显示虚函数表。默认off

            show print <cmd>         // 查看<cmd>显示选项是否打开, 可用于查看以上选项


+ 遇到问题

  - Unable to find Mach task port for process-id 77584: (os/kern) failure (0x5). (please check g
    db is codesigned - see taskgated(8))

    这个问题是由于Mac OS X在使用gdb的时候必须要签名，而且我们安装的GDB是没有签名的，所以才会出现这个问题。要解决这个问题，我们第一步就是要创建一个签名证书。
    
    1. 在Mac OS X中有个『钥匙串』应用，选择『证书助理』-『创建证书』，创建一个签名证书
    2. 在这里，我们填入自己想要的名称，在这里我就使用gdb-cert，然后在『身份类型』中选择『自签名根证书』，在『证书类型』中选择『代码签名』，并勾选『让我覆盖这些默认值』
    3. 接下来，我们可以一直点击继续，直到最后一步，在『请指定钥匙串以便储存证书』选项中，一定要选择『系统』，不可选择『登陆』。然后点击创建证书，即可

    点击完成之后，我们就成功创建证书了，并可以在『钥匙串』-『系统』-『我的证书』下看到我们创建的证书。

    4. 证书创建之后，我们需要将证书设置为始终信任，在我们创建的签名证书上，点击右键，选择『显示简介』，展开『信任』节点，在『使用此证书时』选项下，选择『始终信任』。

    信任之后，可以在钥匙串中看到，证书图标下有个小+号，在信息中可以看到『此证书已标记为受所有用户信任』。

    5. 证书创建完成之后，我们使用如下命令，给GDB签名
       
       .. code-block:: bash

           sudo codesign <path/to/gdb> -s gdb-cert

    6. 若还是出错，请先使用如下命令查看，是否签名是否成功

       .. code-block:: bash

           codesign -v <path/to/gdb>

       若无输出，表示成功。那么可重启 taskgated
       
       .. code-block:: bash
           
           ps -e | grep task  # 先查看该进程的PID
           kill -<PIB> PID  # 杀死进程

       还不行的话，尝试修改GDB 所在用户组和权限

       .. code-block:: bash

           sudo chmod 755 gdb
           sudo chgrp admin gdb
          
       再不行重启
           
  - During startup program terminated with signal ?, Unknown signal.

    先用brew升级gdb 然后新建 ``~/.gdbinit`` 文件，并在其中添加 ``set startup-with-shell off``



    
编译说明
------------

-fPIC 参数： 使代码位置无关
-std=c++11 : 按照C++2011 标准来编译


代码习惯
--------

- 在 cout 时加 endl，避免程序中断时，string留在buffer中无法输出。
- Don’t Mix Signed and Unsigned Types
