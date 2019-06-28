XCode
=======

为XCode 配置OpenCV
-------------------

参考 :ref:`opencv-with-xcode`


SDK 制作
---------

`iOS 静态库制作 <https://blog.csdn.net/wanna_dance/article/details/78687676>`_

基础概念
^^^^^^^^^^

库从本质上来说是一种可执行代码的二进制格式，可以被载入内存中执行。

库类别
""""""
 - 静态库（.a 和.framework）
 - 动态库（.dylib/.tbd 和.framework）

.a与.framework的区别: 
    - .a是一个纯二进制文件，.framework中除了有二进制文件之外还有资源文件和.h文件。
    - .a文件不能直接使用，至少要有.h文件配合，.framework文件可以直接使用。
    
.a + .h + sourceFile = .framework。个人建议用.framework,看个人习惯。

静态库和动态库的区别
""""""""""""""""""""""

静态库和动态库是相对编译期和运行期的：静态库在程序 **编译** 时会被链接到目标代码中，程序运行时将不再需要改静态库；而动态库在程序编译时并不会被链接到目标代码中，只是在程序 **运行** 时才被载入，因为在程序运行期间还需要动态库的存在。

静态库：
   - 优点:

     + 编译后的执行程序不需要外部的函数库支持
     + 模块化，分工合作，提高了代码的复用及核心技术的保密程度(减少耦合性，因为静态库中是不可以包含其他静态库的，使用的时候要另外导入它的依赖库，最大限度的保证了每一个静态库都是独立的，不会重复引用。)
     + 也可以重用，注意不是共享使用
   - 缺点：

     + 编译成的文件比较大
     + 静态库在项目编译时完整地拷贝至可执行文件中，被多次使用就有多份冗余拷贝。
     + 如果静态函数库改变了，那么你的程序必须重新编译
   - 平时我们用的第三方SDK基本上都是静态库。

动态库：
   - 优点:

     + 产生的可执行文件比较小。
     + 动态库在程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存。
     + 动态函数库的改变并不影响你的程序，所以动态函数库的升级比较方便
   - 缺点: 

     + 程序的运行环境中必须提供相应的库
   - iOS平时使用的系统库基本是动态库，比如使用频率最高的UIKit.framework和Fundation.framework。
   - 动态库在制作的时候可以直接包含静态库，也能自动link所需要的依赖库。

   .. Warning::
      苹果禁止iOS开发中使用动态库, iOS上的动态库只能是私有的，因为我们仍然不能将动态库文件放置在除了自身沙盒以外的其它任何地方

创建.a 静态库(XCode 9.4.1)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. 新建工程

打开Xcode 新建工程, 选择iOS -> Framework & Library ---> Cocoa Touch Static Library, 创建工程。

.. figure:: /_static/platform/xcode_staticLib1.png 

删除项目文件夹下自动生成的 ``.m/.h`` 文件，将要打包的c++文件拷贝进去

.. figure:: /_static/platform/xcode_staticLib2.png

注意在Compile Sources 中添加cpp 文件

.. figure:: /_static/platform/xcode_staticLib7.png

.. Important::
   嵌套文件夹，则文件夹中的文件不会自动添加（蓝色）到compile sources，需手动添加（黄色）

`关于导出头文件 <https://blog.csdn.net/zzzzzdddddxxxxx/article/details/50408732>`_

2. 配置工程环境

配置最低支持版本

.. figure:: /_static/platform/xcode_staticLib3.png

设置适配所有模拟器架构

.. figure:: /_static/platform/xcode_staticLib4.png

设置依赖库

.. figure:: /_static/platform/xcode_staticLib8.png


3. 生成 .a 文件

此处注意需要生成4个 

- 真机-Debug版本
- 真机-Release版本
- 模拟器-Debug版本
- 模拟器-Release版本

Debug: 
  + 含完整的符号信息，以方便调试; 
  + 不会对代码进行优化

Release: 
  + 不会包含完整的符号信息
  + 执行代码是进行过优化的
  + 大小会比Debug版本的略小
  + 在执行速度方面，Release版本会更快些（但不意味着会有显著的提升）

通过 EditSchemae... 选择 Debug 和 Release 环境

.. figure:: /_static/platform/xcode_staticLib5.png

选择真机和模拟器

.. figure:: /_static/platform/xcode_staticLib6.png

``command+R`` 生成.a

4. 合并 debug 两个包和 release 两个包

.. Note::
   这里的合并指的是 
   1. debug 下真机+模拟器合并 
   2. release 下真机+模拟器合并

.. code-block:: bash
   
   lipo -create <Debug版.a路径> <Release版.a路径> -output <输出路径>

   //example
   lipo -create /Users/Emily/Library/Developer/Xcode/DerivedData/xocr-ccbtpzpnrtsoymgqmvjuwapryswm/Build/Products/Release-iphonesimulator/libxocr.a /Users/Emily/Library/Developer/Xcode/DerivedData/xocr-ccbtpzpnrtsoymgqmvjuwapryswm/Build/Products/Release-iphoneos/libxocr.a -output tmp/libxocr.a

查看静态库支持的架构
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   lipo -info <.a file>
