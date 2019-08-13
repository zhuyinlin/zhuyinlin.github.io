Python
======

安装
-------

- ubuntu :ref:`python-install-ubuntu`
- mac :ref:`python-install-mac`

包安装
-------

- :command:`pip install <pkg>` 和 :command:`pip install <pkg> --user U` 的安装路径不一样, 前者默认安装在 :file:`/usr/local/lib` 下，后者默认安装在 :file:`~/.local/lib` 下
- :command:`pip install -r <requirements file>`
- :command:`pip freeze`, 查看当前包版本(可用于生成requirements文件)
- 当pip 默认将包安装在 :file:`~/.local/lib` 下时，可能是因为系统中安装了多个版本的pip，导致混乱。通过 :command:`which pip` 和 :command:`pip --version` 查看pip 的路径，删除不必要的，仅保留 :file:`/usr/local/bin` 下面的。
- 在mac上，如果提示找不到包而实际上有，查看包的后缀，若是 ``.egg``  ，文件可能是通过easy_install 装的，因此找不到，卸载后用 pip 重装
- 常用包下载地址

    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

    https://repo.continuum.io/pkgs/free/

- 配置镜像源

  可用的镜像源包括::

      清华：https://pypi.tuna.tsinghua.edu.cn/simple
      阿里云：http://mirrors.aliyun.com/pypi/simple/
      中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
      华中理工大学：http://pypi.hustunique.com/
      山东理工大学：http://pypi.sdutlinux.org/
      豆瓣：http://pypi.douban.com/simple/

  - 暂时

    .. code-block:: bash

        pip install -i <镜像>/simple <package name>

  - 永久
     - ubuntu
        在 ``~/.pip/pip.conf`` 中添加内容如下::
            
           [global] 
           trusted-host=mirrors.aliyun.com
           index-url=http://mirrors.aliyun.com/pypi/simple 

     - windows
        在 ``C:\Users\<user name>\`` 下创建 ``pip\pip.ini`` , 添加内容::

           [global] 
           trusted-host=mirrors.aliyun.com
           index-url=http://mirrors.aliyun.com/pypi/simple 



opencv2
^^^^^^^
  .. code-block:: bash
          
      # 1. 系统安装
      sudo apt-get install python-opencv
      # 2. pip 安装 
      pip install opencv-python  # ?

      pkg-config --modversion opencv # 查看版本（是否与安装方式有关？）

+ ubuntu 出现cv2.imshow() 报错的情况, 如果不是提示的问题，可尝试：
  
  1. 将 /usr/local/lib/python2.7/site-packages 或者 dist-packages 下的 cv2.so 文件复制到 /usr/lib/python2.7 的对应文件下(如果解决，步骤2可忽视)
  2. 将 /usr/local/lib/python2.7/site-packages 或者 dist-packages 下的 cv2 文件删除

+ There is no module named cv2
  
  将 ``opencv 安装目录/python/cv2.pyd`` 文件复制到 ``Python 安装目录/lib/site－packages`` 文件夹中

+ mac 上安装好 opencv for python 但无法导入：
  
  .. code-block:: bash

      cd /Library/Python/2.7/site-packages/
      ln -s /usr/local/Cellar/opencv/2.4.9/lib/python2.7/site-packages/cv.py
      ln -s /usr/local/Cellar/opencv/2.4.9/lib/python2.7/site-packages/cv2.so
  
PIL
^^^^^^^
  
  .. code-block:: bash

      # 1. 系统安装
      sudo apt-get install python-PIL

      # 2. pip 安装
      pip install Pillow

其他 
^^^^^^

- matplotlib
- virtualenv
- jupyter


Anaconda
----------

conda 的使用

.. code-block:: bash

   # 查看
   conda list    # 查看安装了哪些包
   conda env list    # 查看有哪些虚拟环境
   conda -V      # 查看conda的版本

   # 创建虚拟环境
   conda create -n <env_name> <package name>  # 必须指定一个或者几个你需要安装的package
                --clone root   # 克隆创建了一个和原系统一样的python环境

   # This will create two environments, one with Python3 and the other with Python2. 
   conda create -n py3 python=3*
   conda create -n py2 python=2*

   # 虚拟环境
   conda activate <env_ name>  # 激活
   conda deactivate    # 退出
   conda remove -n <env_name> --all  # 移除

   # 虚拟环境中的安装包
   conda install -n <env_name> <package_name>
   conda remove --name <env_name> <package_name>
   

编程学习
--------

基础语法
^^^^^^^^^

python 只有传引用, 但是根据对象可变/不可变可以达到传引用和传值的效果

- mutable: list, dict -> 传引用
- immutable: 数字, string、tuple  -> 传值

::


        a[1:n］是半包围结构，即不包括n在内。下标可取负，从－1（最右边的element）开始
        
        没有花括号，只以缩近表示层次
        逻辑块如循环、判断等用冒号，表达式无需括号
        长语句可用括号括起来，这样可分行书写而不被认为是两个语句，或者在后面用 \\ 表示换行
        
        list: [］
        tuple: (） (，）
        dict:｛:，:，｝
        
        不使用 &&、｜｜ 、－，而是使用 and 、or、nor

        ＃: 单行注释
        ''' '''/"""  """: 多行注释
        
        # list 之间求并集、交集、差集
        list(set(a).intersection(set(b))) 
        list(set(a).union(set(b)))
        list(set(b).difference(set(a))) 或 list(set(b) - set(a))  # b-a

`@classmethod和@staticmethod的区别 <https://stackoverflow.com/questions/12179271/meaning-of-classmethod-and-staticmethod-for-beginner>`_

遍历文件
^^^^^^^^^
  .. code-block:: python

     import os
     os.walk(path) # 返回tuple(dirpath, dirnames, filenames), 包含子级
     
     os.sep # 路径分隔符
     os.path.isdir(path) # 判断是否文件夹
     os.getcwd() # 获取当前路径
     os.listdir(path) # 获取目录下的所有内容

     # 返回所有图片的文件名列表
     [os.path.join(path,f) for f in os.listdir(path) if f.endswith('.jpg')] 

     os.path.dirname(__file__) # 当前文件所在路径，根据调用方式返回绝对／相对路径

     os.mkdir(path) # 创建目录
     os.rmdir(path) # 删除目录  


管理参数配置文件 [#Param]_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Using built-in data structure
2. Using external configuration file

   - `configparser` for Python 3.x, `ConfigParser` for Python 2.x
   - `写一个Param类用于导入参数 <https://cs230-stanford.github.io/logging-hyperparams.html>`_
3. Using environment variables
4. Using dynamic loading

virtualenv
^^^^^^^^^^^^

  .. code-block:: bash

    // 1. create a virtualenv
    cd <project dir>/
    virtualenv --no-site-packages <env name> # 创建一个独立的Python运行环境, 
                                             # 参数--no-site-packages使已经安装到系统Python环境中的所有第三方包都不会复制过来
                                             # 参数-p[--python=] <python dir> 可以指定python版本
    # 或者
    python3/python -m venv <myenvname>

    // 2. active
    source <ven name>/bin/activate  # 进入该环境

    // 3. do your job 
    pip install <package name>
    pip install -r <requirements file>

    // 4. 查看当前包版本(可用于生成requirements文件)
    pip freeze 

    // 5. deactive
    deactivate  #退出当前的ven环境


pipenv
^^^^^^^^

.. code-block:: bash

   pipenv --three     # 会使用当前系统的Python3创建环境
   pipenv --python 3.6    # 指定某一Python版本创建环境

   pipenv shell     # 激活虚拟环境(为存在虚拟环境可自动创建)
   exit    # 退出pipenv　　

   pipenv install <pkg_name>   # 安装相关模块并加入到Pipfile
   pipenv uninstall <pkg_name>    # 卸载安装包　　
   pipenv uninstall --all    # 卸载全部包并从Pipfile中移除
   piplist     # 查找所有安装包　　
   pipenv graph     # 查看目前安装的库及其依赖
   pipenv lock      # 生成lockfile
   pipenv lock -r --dev > requirements.txt  # 生成requirements 文件
   pipenv install -r requirements.txt  # 通过requirements 文件安装包

   pipenv --where    # 显示目录信息
   pipenv --venv    # 查找虚拟环境的路径　　
   pipenv --rm      # 删除虚拟环境
   pipenv --py    # 显示Python解释器信息

   pipenv check    # 检查安全漏洞


pylint
^^^^^^^^^



theano
^^^^^^^
GPU 使用

numpy
^^^^^^^^^^^^
  .. code-block:: python  

     A * B # 元素运算
     np.dot(A, B) # 矩阵运算

     numpy.vstack([arr1,arr2]), numpy.hstack(), numpy.dstack() # 数组的组合1
     numpy.concatenate((arr1,arr2,...),axis=0)  # 数组的组合2
     numpy.append(arr1,arr2)  # 数组的扩大
     arr.reshape(-1, rows/height, cols/width, depth) # 数组结构变化

opencv2
^^^^^^^^^^^^

  `tutorial <http://opencv-python-tutroals.readthedocs.io/en/latest/>`_

  OpenCV for Python是通过NumPy进行绑定的, 图像就是NumPy中的数组！

  对图像的基本操作：

  .. code-block:: python
     
     #:linenos: # 添加行号，此时窗口不自适应
     import cv2  
     import numpy as np    
     img = cv2.imread("./cat.jpg") # OpenCV目前支持读取bmp、jpg、png、tiff等常用格式, 
                                   # 读进来直接是BGR 格式, 通道格式为HWC
     cv2.namedWindow("Image") # 创建一个窗口  
     cv2.imshow("Image", img)  
     cv2.waitKey (0) # 如果不添本句，在IDLE中执行窗口直接无响应;在命令行中执行的话，则是一闪而过。 
     cv2.destroyAllWindows() # 最后释放窗口是个好习惯！  
     
     emptyImage = np.zeros(img.shape, np.uint8)   

     #  获取原图像副本的两种方式 
     emptyImage2 = img.copy()    
     emptyImage3=cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)  
     
     # 保存图片
     # 第三个参数针对特定的格式：
     #     对于JPEG，其表示的是图像的质量，用0-100的整数表示，默认为95。
     #     对于PNG，其表示的是压缩级别，从0到9, 压缩级别越高，图像尺寸越小；默认为3 
     cv2.imwrite("./cat2.jpg", img, [int(cv2.IMWRITE_JPEG_QUALITY), 5]) #cv2.IMWRITE_JPEG_QUALITY类型为Long，必须转换成int 
     cv2.imwrite("./cat2.png", img, [int(cv2.IMWRITE_PNG_COMPRESSION), 9])

     cv2.resize(img, (cols/width, rows/height), interpolation = cv2.INTER_CUBIC) # 注意这里shape参数的不同！！

     # 颜色空间转换
     img1 = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

     # 画点、圆、线
     cv2.circle(img, (x,y), 2, color = (R, G, B), -1)
     cv2.rectangle(img, (left, top), (right, bottom), color = (R,G,B), 2)

PIL
^^^^^^^^
  .. code-block:: python
   
     from PIL import Image
     from PIL import ImageDraw
     Image.fromarray(XXX).show()
     im.show()
     im.save()
     ImageDraw.Draw(img).rectangle((left, top, right, bottom), outline = 'green')

matplotlib
^^^^^^^^^^^^^
- ‘ascii’ codec can’t decode byte
  
  .. code-block:: python
       
       import sys
       reload(sys)     
       sys.setdefaultencoding('uft-8')  

- 图像中文显示乱码

  暂时的方法

  + 查找中文字体
    
    .. code-block:: bash
     
        fc-list :lang=zh 

  + 代码中设置字体

    .. code-block:: python

        from matplotlib.font_manager import FontProperties
        font = FontProperties(fname = "/usr/share/fonts/truetype/arphic/ukai.ttc", size=14)
        plt.title(u"用户数量(Y)关于游戏消费金额(X)的分布图", fontproperties=font)

  一劳永逸的方法

  + 下载字体并拷贝到matplotlib的字体目录

    可通过以下代码查看配置文件位置
    
    .. code-block:: python
    
       import matplotlib
       print (matplotlib.matplotlib_fname())

    字体文件夹与这个文件在同一级目录

  + 修改配置文件

    修改内容如下

    .. code::

       font.family         : sans-serif        

       # 这里其实在最前面加上字体名称即可
       font.sans-serif     : <字体名称>, Bitstream Vera Sans, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif 
       axes.unicode_minus，将True改为False，作用就是解决负号'-'显示为方块的问题

    注意，字体名称可通过以下代码确认

    .. code-block:: python

       from matplotlib.font_manager import FontManager

       fm = FontManager()
       mat_fonts = set(f.name for f in fm.ttflist)

  + 重新构建

    .. code-block:: python

       from matplotlib.font_manager import _rebuild
       _rebuild()

  如果此后出现部分中文无法正常显示，则考虑是否数据本身有问题



dlib
^^^^^^
Distutils
^^^^^^^^^^^^^
用来在Python环境中构建和安装额外的模块。新的模块可以是纯Python的，也可以是用C/C++写的扩展模块，或者可以是Python包，包中包含了由C和Python编写的模块。

作为模块开发者，除了编写源码之外，还需要：

* 编写setup脚本（一般是setup.py）；
* 编写一个setup配置文件（可选）；
* 创建一个源码发布；
* 创建建一个或多个构建（二进制）发布（可选）

编写 setup.py 脚本
""""""""""""""""""
setup函数的参数表示提供给Distutils的信息，这些参数分为两类：包的元数据（name、version，description, author，author_email，url...）以及包的信息(py_modules、packages)；

.. Tip::
        模块由模块名表示，而不是文件名（对于包和扩展而言也是这样）

* package
* module
* `Building an extension module <https://docs.python.org/2/extending/building.html>`_

  在Distutils中描述扩展模块较描述纯python模块要复杂一些。对于纯python模块，仅需要列出模块或包，然后Distutils就会去寻找合适的文件，这对于扩展模块来说是不够的，你还需要指定扩展名、源码文件以及其他编译/链接需要的参数（需要包含的目录，需要连接的库等等）      
  
  描述扩展模块可以由setup函数的关键字参数 ``ext_modules`` 实现。 ``ext_modules`` 是Extension实例的列表，每一个Extension实例描述了一个独立的扩展模块。

  底层的扩展构建机制是由 ``build_ext`` 命令实现的::
          
          python setup.py build_ext --inplace  # The --inplace option creates the shared object file (with .so suffix) in the current directory.

源码发布
""""""""""""""""""
.. code-block:: bash

   python setup.py sdist  # 源码安装包
   python setup.py bdist_wininst  # Windows 下使用
   python setup.py bdist_rpm  # Linux 下使用

对于使用者，源码包的安装是将源码包解压后，运行 :code:`setup.py install` ；而Windows和Linux安装包则直接运行安装文件。这些操作会将相应文件复制到Python环境存放第三方模块的目录中。

spawn 模块
""""""""""""""""""
find_executable("exe_name") 可用于检测可执行程序 exe_name 是否存在，但前提是加入了PATH

logging
^^^^^^^^^
`a good practice <https://fangpenlin.com/posts/2012/08/26/good-logging-practice-in-python/>`_

- Write logging records everywhere with proper level
- Use `__name__` as the logger name
- Capture exceptions and record them with traceback
- Do not get logger at the module level unless disable_existing_loggers is False
- Use JSON or YAML logging configuration
- use `RotatingFileHandler` instead of `FileHandler` in production environment

jupyter
^^^^^^^^^

(mac)When you got Warning : 'Native kernel (python2) is not available'with jupyter notebook... 

.. code-block:: bash

     pip uninstall backports.shutil_get_terminal_size
     pip install backports.shutil_get_terminal_size
     # Installed kernelspec python2 in /usr/local/share/jupyter/kernels/python2
     ipython kernelspec install-self
     ipython kernel install

使用

.. code-block:: bash

       jupyter notebook


打包
----------

`Installing Package Data <https://docs.python.org/3/distutils/setupscript.html#installing-package-data>`_

`Basic Resource Access <http://peak.telecommunity.com/DevCenter/PkgResources#basic-resource-access>`_

项目做成package时，import 部分要做修改：

::

   from <module> import <member> -> from .<module> import <member> # 不推荐
                                 -> from <package>.<module> import <member>  

.. Note::

   第二种方法比较好，因为这种方法导入模块后，若想运行模块下的代码，只需在包的上一级目录运行

   :code:`python <package>/<script>.py` 
   
   或者使用 `-m` 方式
  
   :code:`python -m <package>/<module>`

   而第一种方法只能使用 `-m` 方式运行模块下的代码


用 `setuptools` 打包分发

1. 编辑 `setup.py` 文件
2. :code:`python setup.py install --record <logName>` 安装包，这个命令会讲我们创建的egg安装到python的dist-packages目录下
3. 卸载 
   :code:`cat <logName> | xargs rm -rf`
   

生成文档
-----------

.. code-block:: bash

   pip install Sphinx
   pip install sphinx_rtd_theme

   # 在doc目录下运行
   sphinx-quickstart

配置 :file:`conf.py` 文件:

.. code-block:: python

   import os
   import sys
   import sphinx_rtd_theme

   sys.path.insert(0, os.path.abspath("<projectdir>"))

   html_theme = "sphinx_rtd_theme"  # 换主题
   html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]

配置好后，在 `doc` 下执行

.. code-block:: bash

   sphinx-apidoc -o ./source <projectdir>
   # 若已经执行过该命令，再执行时会跳过已生成的文件，此时可以使用以下命令
   # sphinx-apidoc -f -o docs/source <projectdir>
   make html

在使用 `google-style comment <https://github.com/google/styleguide/blob/gh-pages/pyguide.md>`_ 的时候，需要使用 `napoleon <http://www.sphinx-doc.org/en/master/usage/extensions/napoleon.html#module-sphinx.ext.napoleon>`_ 插件来让sphinx理解这些注释，

.. code-block:: python

   # conf.py

   # Add napoleon to the extensions list
   extensions = ['sphinx.ext.napoleon']

 


一些调试技巧
------------
.. code-block:: python

   print type(XXX)
   print XXX.shape
   print len(XXX) 

当使用 :code:`./xx.py` 运行程序出现 `permission denied` ，即使加了 sudo 也无效时，有两个解决方法：

.. code-block:: bash

   chmod +x xx.py  # 1.赋予文件可执行性
   python xx.py    # 2.换一种运行方式

pdb 调试
--------

+ 命令行

  .. code-block:: bash 
  
      python -m pdb xxx.py  // 相当于断点在第一行之前

+ python 交互环境下

  .. code-block:: bash

          import pdb
          pdb.run("xxx.py") // 或者函数

+ 常用命令

  .. code-block:: bash
      
      h(elp)  // 打印当前版本Pdb可用的命令，如果要查询某个命令，可以输入 h [command]
      l(ist)  // 列出当前将要运行的代码块
      b(reak) // 设置断点，参数可以是行号和函数名，如果只敲b，会显示现有的全部断点 
      condition bpnumber [condition] // 设置条件断点，对第bpnumber个断点加上条件 
      cl(ear) // 如果后面带有参数，就是清除指定的断点;不带参数就是清除所有的断点 
      disable/enable // 禁用/激活断点
      n(ext)，// 运行下一行，如果当前语句有一个函数调用，用n是不会进入被调用的函数体中的 
      s(tep)，// 跟n相似，但是如果当前有一个函数调用，那么s会进入被调用的函数体中 
      c(ont(inue)) // 让程序正常运行，直到遇到断点 
      j(ump)       // 让程序跳转到指定的行数 
      a(rgs)  // 打印当前函数的参数
      p(rint) // 打印某个参数
      q(uit)  // 退出调试



.. [#Param] https://hackernoon.com/4-ways-to-manage-the-configuration-in-python-4623049e841b
