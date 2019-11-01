mac
========

软件安装
---------

MacPorts
^^^^^^^^^
卸载

.. code-block:: bash

   $ sudo port -f uninstall instalzled
   $ sudo port clean all
   $ sudo rm -rf \
   /opt/local \
   /Applications/DarwinPorts \
   /Applications/MacPorts \
   /Library/LaunchDaemons/org.macports.* \
   /Library/Receipts/DarwinPorts*.pkg \
   /Library/Receipts/MacPorts*.pkg \
   /Library/StartupItems/DarwinPortsStartup \
   /Library/Tcl/darwinports1.0 \
   /Library/Tcl/macports1.0 \
   ~/.macports

Homebrew
^^^^^^^^^
`Homebrew <http://brew.sh/index_zh-cn.html>`_ 类似Ubuntu的 ``apt-get`` ，Fedora的 ``yum`` ，简单来说就是为了方便安装软件而生的。

安装命令(ruby):

.. code-block:: bash

   ruby -e "$(curl -fsSL  https://raw.githubusercontent.com/Homebrew/install/master/install)"
   
安装完后，输入

.. code-block:: bash
    
   brew update
   brew upgrade

.. Note:: 
        安装过程中会先检测系统中是否已经安装了Homebrew，如果已经安装，会有提示命令行让你先删除已安装的Homebrew。同时安装过程中需要按回车键授权同意安装，安装结束之后，最好运行以下命令，用于检测是否有冲突，同时它会提示一些已失效的库链接

.. code-block:: bash

       brew doctor

Homebrew安装在 ``/usr/local`` 目录下，同时它会创建 ``/user/local/Cellar`` 目录用于存放通过Homebrew安装的程序，运行

.. code-block:: bash

   brew -v
   
查看安装版本

Homebrew都是在 ``--prefix=/usr/local/Cellar/NAME/VERSION/ --enable-shared --enable-static`` 配置下编译包， **prefix** 代表安装路径， **shared** 代表创建的 ``./lib`` 下的动态库文件（ ``dylib`` ）， **static** 代表编译的时创建静态库文件（ ``a`` ），二者都会生成二进制（ ``bin`` ）可执行文件（UNIX可执行文件格式为空）。但是值得注意的是 ``/usr/local/Cellar/NAME/VERSION/`` 原本并不会被作为搜索目录，也就是说安装后是无法在终端直接调用，必须输入完成的路径，Homebrew的解决方案是：将安装路径下的 ``lib、bin、header`` 都做一个软链接到 ``/usr/local/`` 中。

Homebrew常用命令
    .. code-block:: bash

        brew search <package name> # 搜索程序
        brew install <package name> # 安装程序
        brew uninstall <package name> # 卸载程序
        brew link <package name> # 配置到环境变量?
        brew list # 列举通过Homebrew安装的程序
        brew update # 更新Homebrewbrew
        brew upgrade [<package name>] # 更新某个具体程序，或者更新所有程序
        # 更新完后根据命令行提示对相应包做更新配置 
        brew cleanup [<package name>] # 删除某个具体程序，或者删除所有老版程序, 不要随便运行，后果可能很严重
        brew outdated # 查看哪些程序需要更新
        
其他命令
    .. code-block:: bash

        brew home <package name> # 用浏览器打开
        brew info <package name> # 显示软件内容信息, 如安装vim时可以查看有哪些编译选项
        brew deps <package name> # 显示包依赖
        brew server <package name> # 启动web服务器，可以通过浏览器访问http://localhost:4567/ 来同网页来管理包
        brew -h # 查看帮助
        
删除Homebrew
    .. code-block:: bash
     
        cd `brew –prefix`
        rm -rf Cellar
        brew prune
        rm -rf Library .git .gitignore bin/brew README.md share/man/man1/brewrm 
        -rf ~/Library/Caches/Homebrew

权限问题
    .. code-block:: bash
        
        # Sudoing homebrew is a bad idea (and doesn't work either) we have to reset the permissions within /usr/local
        sudo chown -R $(whoami) /usr/local

vim
^^^^^^^^^
系统自带的vim 不要去动它，通过homebrew 装自己想要的版本

.. code-block:: bash

   brew install vim --with-cscope --with-python --with-lua --override-system-vim
   # 参数自己选择


.. _cmake-install-mac:


Latex 
^^^^^

- `MacTex <https://tug.org/mactex/>`_
- `BasicTex <http://www.tug.org/mactex/morepackages.html>`_
- `TinyTex <https://yihui.name/tinytex/>`_


CMake
^^^^^^^^^

安装Homebrew之后输入

.. code-block:: bash
   
    brew install cmake 
   
这样等待一会cmake就安装好了。安装路径为 ``/usr/local/Cellar/cmake/3.4.1/bin/`` 


.. _python-install-mac:

python
^^^^^^^^^

Mac系统自带的python路径： :file:`/System/Library/Frameworks/Python.framework/Version/x.x`

自己安装的python路径： :file:`/Library/Frameworks/Python.framework/Version/x.x`

homebrew 安装:

    - 路径：  :file:`/usr/local/Cellar/pythonx`
    - Frameworks路径： :file:`/usr/local/Frameworks/Python.framework/Versions`


.. _opencv-install-mac:

OpenCV
^^^^^^^^^

.. code-block:: bash

    brew tap homebrew/science
    brew install opencv
    brew install opencv --with-contrib  // with contrib


matlab
^^^^^^^^^
mac中的路径分隔符与win中不一样，为“／”

Eclipse
^^^^^^^^^

**错误解决**

1. dyld: Library not loaded: XXX.dylib

   ::
           
           dyld: Library not loaded: XXX.dylib
           Referenced from: 
           /Users/pdl/Development/HelloWorld/Hello/Debug/Hello
           Reason: image not found
          
   This means that your program is using a dynamic library called libWorld.dylib, although you linked your dynamic library to your program during compiling. But you have to tell your program where is the dylib in run-time. There are two solutions:
   
   **Solution 1: set up dynamic library environment variables for your project in Eclipse** :

   a. Right click your ``project name`` , select ``Run As->Run Configuration``  
   b. Under ``Environment`` tab click ``New``
   c. Put ``DYLD_LIBRARY_PATH`` or ``DYLD_FALLBACK_LIBRARY_PATH`` in ``Name`` box
   d. .Put the path where your ``libWorld.dylib`` file is in ``Value`` box
      
   For example: if ``libWorld.dylib`` file is in ``/opt/local/lib/my_dylib`` folder, than put the path in the ``Name`` box
   
   **Solution 2: set DYLD_LIBRARY_PATH in your bash configuration file** :

   a. Normally in Mac OS, configuration file is ``.profile`` under ``~/`` folder, if you don't have this file than create a new file with the same name
   b. Edit that file: 
      Add this line in your ``.profile`` file::
              
              export DYLD_LIBRARY_PATH=PATH_TO_YOUR_DYLIB:$DYLD_LIBRARY_PATH

      in my example the ``PATH_TO_YOUR_DYLIB`` is ``opt/local/lib/my_dylib``, so you just add::
              
              export DYLD_LIBRARY_PATH=/opt/local/lib/my_dylib:$DYLD_LIBRARY_PATH 

      in ``.profile`` file
   c. Problem solved, in this solution, you don't have to set up dylib path for all eclipse project
      
   PS. ``DYLD_LIBRARY_PATH`` is an environment variable to specify the path of your dynamic library

   Difference between ``DYLD_LIBRARY_PATH`` and ``DYLD_FALLBACK_LIBRARY_PATH`` please refer to `this <http://stackoverflow.com/questions/3146274/is-it-ok-to-use-dyld-library-path-on-mac-os-x-and-whats-the-dynamic-library-s>`_ post

2. 可执行文件运行出错 dyld: Library not loaded:
   
   在eclipse 上运行成功，但运行生成的可执行文件依旧提示找不到动态链接库
   
   解决方法：将库文件放到目录 ``/usr/local/lib`` 下


.. _AS-install-mac:

Android Studio
^^^^^^^^^^^^^^^

完全删除android studio
""""""""""""""""""""""""""""""

.. code-block:: bash

   // 卸载
   rm -Rf /Applications/Android\ Studio.app
   rm -Rf ~/Library/Preferences/AndroidStudio*   // 配置文件
   rm ~/Library/Preferences/com.google.android.studio.plist
   rm -Rf ~/Library/Application\ Support/AndroidStudio*
   rm -Rf ~/Library/Logs/AndroidStudio*
   rm -Rf ~/Library/Caches/AndroidStudio*

   //删除全部项目
   rm -Rf ~/AndroidStudioProjects

   //删除gradle关联文件 (caches & wrapper)
   rm -Rf ~/.gradle

   //删除模拟器
   rm -Rf ~/.android

   //删除android 工具
   rm -Rf ~/Library/Android*


小技巧
---------

快捷键
^^^^^^^

系统::

        CMD+Option+Esc      //任务管理器
        fn＋Ctrl＋f3        //快速锁定到dock
        Ctrl ＋backspace    //打开Spotlight
        Option ＋backspace  //打开Alfred

终端::

        Ctrl + d  //删除一个字符，相当于通常的Delete键（命令行若无所有字符，则相当于exit；处理多行标准输入时也表示eof）
        Ctrl + h  //退格删除一个字符，相当于通常的Back space键
        Ctrl + u  //删除整行
        Ctrl + k  //删除光标后到行尾的字符
        Ctrl + w  //删除光标前到当前所处单词（Word）的开头

        Ctrl + p  //调出命令历史中的前一条（Previous）命令，相当于通常的上箭头
        Ctrl + n  //调出命令历史中的下一条（Next）命令，相当于通常的上箭头
        Ctrl + c  //取消当前行输入的命令, 相当于Ctrl + Break
        Ctrl + l  //清屏，相当于执行clear命令

        Ctrl + a  //光标移动到行首（Ahead of line），相当于通常的Home键
        Ctrl + e  //光标移动到行尾（End of line）
        Alt + left   //光标向前(Forward）移动到下一个单词
        Alt + right   //光标往回（Backward)移动到前一个单词
        Ctrl + f  //光标向前(Forward)移动一个字符位置
        Ctrl + b  //光标往回(Backward)移动一个字符位置
        Ctrl + r  //显示：号提示，根据用户输入查找相关历史命令（reverse-i-search）</p> <p>次常用快捷键：
        Ctrl + y 粘贴最后一次被删除的单词
        comman+d 上下分屏

Finder::

        CMD＋Z        //撤销前次的操作
        CMD＋up/down    //上下级文件夹跳转
        CMD＋ ［ ／］ //Finder 回退／前进
        option＋［／］//前后打开的文件夹间切换

OneNote::

        Ctrl ＋ ＝  //插入公式

浏览文档::

        fn ＋ up/down   //表示page up／down

字典::

        Ctrl + CMD + d  //划词翻译
        选词后按下option + Esc 可以朗读

终端
--------

iTerm2 + Zsh + Oh-my-zsh + Solarized + Monaco for Powerline
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
1. `iTerm2 <http://www.iterm2.com/>`_
2. zsh mac 自带，但我们可以用 brew install 安装最新的

   .. code-block:: bash

      $ brew install zsh
      $ sudo rm /bin/zsh    # 替换系统自带 zsh
      $ sudo ln -s `brew --prefix zsh`/bin/zsh /bin/zsh
      $ chsh -s $(which zsh)  # 切换系统当前用户的默认 shell 为 zsh, 重启生效

   .. Important::
       chsh 命令加sudo 会对root用户的shell做修改，因此此处不可加root

3. `Solarized Dark <http://ethanschoonover.com/solarized>`_ ，配色方案集合，下载后找到iTerm的配色方案，按照该文件夹下的readme操作，Vim 的配色最好和终端的配色保持一致(直接使用vundle安装)，不然在 Terminal/iTerm2 里使用命令行 Vim 会很别扭:

    .. code-block:: bash 
            
      syntax enable
      set background=dark
      let g:solarized_termcolors=256
      colorscheme solarized

4. `oh-my-zsh <https://github.com/robbyrussell/oh-my-zsh>`_ Zsh 的现成配置方案，方便你管理自己的zsh，可使用以下命令安装：

   .. code-block:: bash

      $ curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh

   编辑 .zshrc 文件:

   .. code-block:: bash

           ZSH_THEME="agnoster" # 使用 agnoster 主题，很好很强大
           DEFAULT_USER="你的用户名"     # 增加这一项，可以隐藏掉路径前面那串用户名
           plugins=(git brew node npm)   # 自己按需把要用的 plugin 写上

           # 关于 ls, Mac OS X 是基于 FreeBSD 的，所以一些工具 ls, top 等都是 BSD 那一套，ls 不是 GNU ls，
           # 所以即使 Terminal/iTerm2 配置了颜色，但是在 Mac 上敲入 ls 命令也不会显示高亮，
           # 可以通过安装 coreutils 来解决( brew install coreutils), 
           #不过如果对 ls 颜色不挑剔的话有个简单办法就是在 .bash_profile 里输出 CLICOLOR=1：
           export CLICOLOR=1

           #另外，建议把末尾的 export PATH 稍微调整一下，
           #比如 Homebrew 就建议 /usr/local/bin 应该优先于 /usr/bin；
           #另外也可以自己加上比如 Ruby Gems 目录 /usr/local/opt/ruby/bin、Node.js NPM 目录 ~/bin 等。
           export PATH=/usr/local/bin:/usr/local/sbin/:$HOME/bin:$PATH

           #关于 Homebrew 的路径，比如 zsh 这个包可以通过 brew --prefix zsh 知道它的目录是 /usr/local/opt/zsh，
           #关于这些链接：
           #/usr/local/opt/zsh 目录 -> /usr/local/Cellar/zsh/版本号 目录
           #/usr/local/bin/zsh 文件 -> /usr/local/Cellar/zsh/版本号/bin/zsh 文件
           #所以就有了上面那条 chsh -s 命令的写法。

   .. Note::
           注意：vim的补全时可能会报错_arguments:450: _vim_files: function definition file not found，此时只需要删掉 ~/.zcompdump，关闭shell窗口，重新进入即可。
5. `Monaco-Powerline <https://github.com/mneorr/powerline-fonts/blob/bfcb152306902c09b62be6e4a5eec7763e46d62d/Monaco/Monaco%20for%20Powerline.otf>`_ 字体补丁，下载后双击安装，在 iTerm2 的 Preference/Profiles/Text 中将 Regular Font 和 Non-ASCII Font 都换成此字体，否则出现乱码


错误解决办法
-------------

解决桌面上方任务栏消失的问题::
        
        //杀死任务栏（控制面板）进程，系统会自动重新启动任务栏  
        $ pkill gnome-panel  
        //如果上一条命令未能解决问题，那就执行下一条命令杀死图形界面吧，  
        //系统会帮我们重新启动图形界面，其实就是注销按钮调用的功能  
        $ pkill gnome-session  

通过 sshfs 挂载的目录断开后，出现 `Input/Outout error`::
    
    原因是连接失效，但sshfs仍在运行并占用该目录
    pgrep -lf sshf  //Find the culprit sshfs process
    sudo kill -9 <PID of sshfs>
    sudo umount -f <mounted_dir> //sudo force unmount the "unavailable" directory


清理
---------
慎用

1. Remove the Python 2.7 framework

   .. code::
       
       sudo rm -rf /Library/Frameworks/Python.framework/Versions/2.7
       sudo rm -rf /System/Library/Frameworks/Python.framework/Versions/2.7

2. Remove the Python 2.7 applications directory

   .. code:: 
        
       sudo rm -rf "/Applications/Python 2.7"

3. Remove the symbolic links 
   
   .. code::
       
       cd /usr/local/bin/  # 这个路径根据 which <command name> 查找
       ls -l /usr/local/bin | grep '../Library/Frameworks/Python.framework/Versions/2.7' | awk '{print $9}' | tr -d @ | xargs rm

4. If necessary, edit your shell profile file(s) to remove adding PATH

