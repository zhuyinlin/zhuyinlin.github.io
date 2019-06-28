Cmake
=====

`Tutorial <https://cmake.org/cmake-tutorial>`_

`Documentation <https://cmake.org/documentation>`_

This tutorial can be found in the `Tests/Tutorial <http://public.kitware.com/cgi-bin/viewcvs.cgi/Tests/Tutorial/?root=CMake>`_  directory of the CMake source code tree.

:download:`CMake Practice <../_doc/platform/CMake Practice.pdf>`

Install
--------

- ubuntu :ref:`cmake-install-ubuntu`
- mac  :ref:`cmake-install-mac`

内部编译
---------
使用Cmake时，在CmakeLists.txt 中配置相关信息，例如：

::

        //cmake 的构建定义文件，文件名是大小写相关的，如果工程存在多个目录，需要确保每个要管理的目录都存在一个CMakeLists.txt
        
        PROJECT (HELLO)//定义工程名称
        SET(SRC_LIST main.c)//显式的定义变量
        MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR})//向终端输出用户定义的信息
        MESSAGE(STATUS "This is SOURCE dir "${HELLO_SOURCE_DIR})
        //会生成一个文件名为 hello 的可执行文件，相关的源文件是 SRC_LIST 中定义的源文件列表
        ADD_EXECUTABLE(hello SRC_LIST)

结束后

.. code-block:: bash

   cmake . # 命令后面的点号，代表本目录

此时系统自动生成了： ``CMakeFiles`` , ``CMakeCache.txt`` , ``cmake_install.cmake`` 等文件，并且生成了 ``Makefile`` 。继续

.. code-block:: bash

   make  # 如果想了解 make 构建的详细过程，
         # 可以使用 make VERBOSE=1 或者 VERBOSE=1make 命令来进行构建。

可生成可执行文件，通过 :code:`./XXX Args` 运行

若要清理工程，可执行

.. code-block:: bash

   make clean

外部编译
--------
以上是 **内部编译** 的例子，它生成了一些无法自动删除的中间文件，所以，引出了我们对 **外部编译** 的探讨（实际上cmake 强烈推荐的是外部构建），外部编译的过程如下：

1. 清除工程目录中除源代码文件和 ``CmakeLists.txt`` 之外的所有中间文件；
2. 在工程目录中建立 ``build`` 目录，当然你也可以在任何地方建立 build 目录，不一定必须在工程目录中；
3. 进入 ``build`` 目录，运行 :code:`cmake ..`
   
   .. Note::
           ..代表父目录，因为父目录存在我们需要的CMakeLists.txt，如果你在其他地方建立了 build 目录，需要运行 cmake <工程的全路径>
           
   查看一下 build 目录，可以发现生成了编译需要的 Makefile 以及其他的中间文件；
4. 运行 make 构建工程，就会在当前目录(build)中获得目标文件 

外部编译一个最大的好处是，对于原有的工程没有任何影响，所有动作全部发生在编译目录。

.. Warning::
        
        通过外部编译进行工程构建，HELLO_SOURCE_DIR 仍然指代工程路径，而 HELLO_BINARY_DIR 则指代编译路径



调试
----

在 CMakeLists.txt文件中添加如下内容::

    SET(CMAKE_BUILD_TYPE "Debug")
    SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb")
    SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")

在cmake中有一个全局的环境变量，CMAKE_BUILD_TYPE，可以取Release或者Debug等值。然后可以通过设置CMAKE_CXX_FLAGS_DEBUG来设置在debug时的CXXFLAGS，这个值大家肯定都熟悉的哈。如果不需要添加调试信息，就直接修改CMAKE_BUILD_TYPE的值。

