Git 
====

`Documentation <https://git-scm.com/doc>`_

Git 基础命令
------------

新建 Git 项目 
~~~~~~~~~~~~~
.. code-block:: bash
   
   git init

此时文件所在目录会生成 ``.git`` 文件。Git 项目包含3部分: 

+ **Working Directory**: where you'll be doing all the work: creating, editing, deleting and organizing files
+ **Staging Area**: where you'll list changes you make to the working directory
+ **Repository**: where Git permanently stores those changes as different versions of the project

版本控制
~~~~~~~~~~~~~
.. code-block:: bash

   # 查看 Git 项目状态
   git status   # “Untracted files” 下的红色文件是 Git 检测到，但没开始追踪的文件
     
   # 将文件添加到追踪(add to the staging area )
   git add <filename1> <filename2> ...  # 注意：修改完后记得用此命令将文件添加到追踪中
   git add -A  # add all, 会自动忽略.gitignore 文件中指定的文件
   git add -u  # add tracked file 

   # 从 staging area 删除 add 的内容（前提是未 commit ）
   git reset HEAD <filename>  # "Unstaged changes after reset" 下现实的是删除的内容

   # 提交修改(Commit the changes to the repository)
   git commit -m "some describe words"  # 每次commit 必须先 add
   git commit  # 可换行输入信息

   # 查看提交的所有版本
   git log
     
   # 查看最近提交的版本
   git show HEAD  # 会现实最新 commit 的所有内容，除 log 以外的内容，还有diff
     
   # 单一文件恢复到最近一次 commit 的版本
   git checkout HEAD <filename> 
   # 单一文件恢复到指定版本
   git checkout --patch <commit id> <filename>
   # 单一文件改成某一分支的版本
   git checkout --patch <branch name> <filename>
   # 恢复到指定版本
   git reset SHA  # SHA 是相应版本号的前7位。注意，恢复到该版本后，次版本后的版本历史将被彻底删除, HEAD也将变为该版本。
  
.. Tip::
        + :code:`git reset --mixed` ：此为默认方式，它回退到某个版本，只保留源码，回退commit和index信息
        + :code:`git reset --soft` ：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可    
        + :code:`git reset --hard` : 彻底回退到某个版本，本地的源码也会变为上一个版本的内容
 
重写历史
~~~~~~~~

.. code-block:: bash

   # 修改 commit message
   git commit --amend  # 只能修改上一次的

   # 合并 commit
   git rebase -i

.. Warning::
        在commit 信息已被push 的情况下， :code:`git reset` ， :code:`git commit -amend` ， :code:`git reset` 等对 commit 信息做修改的命令最好不要使用， 因为这会影响到其它已拉取历史分支进行开发的伙伴

详细参考 `Rewriting history <https://www.atlassian.com/git/tutorials/rewriting-history>`_

.gitignore 文件
~~~~~~~~~~~~~~~
.. code-block:: bash

   # 本地添加可以直接修改.gitignore, 并将其添加到库
   git config --global core.excludesfile ~/.gitignore_global # 全局设置

   # 有时可能因为 gitignore 的设置导致无法添加文件， 为确认该问题， 可使用
   git check-ignore -v <filename>

   # 若想忽略的文件先前已被添加，先从记录中删除，然后将要忽略的文件添加到ignore，
   # 再commit
   git rm --cached <filename>

查看文件修改前后区别
~~~~~~~~~~~~~~~~~~~~~
.. code-block:: bash

   git diff <filename> 
   git diff version(all)  <filename>  # difference with a specific version 
   git diff <commit id> --stat  # difference file list

白色的代表未修改， ``＋`` 开头的代表其内有增加内容,  ``-`` 开头的表示其内有删除内容
     
个性化 log 输出
~~~~~~~~~~~~~~~~
.. code-block:: bash

   git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit 
   # 或者, 使用以下命令， 就可以在每次输入 git lg 时打印个性输出
   git config --global alias.lg "log --color --graph --pretty=format:'%cred%h%creset -%c(yellow)%d%creset %s %cgreen(%cr) %c(bold blue)<%an>%creset' --abbrev-commit"

   # --graph选项可以显示branch的ascii图例
  
其它参数参考 `git-show <https://linux.die.net/man/1/git-show>`_ 

保存当前工作
~~~~~~~~~~~~~
.. code-block:: bash

   git stash 
   do some work
   git stash pop
     
可用来暂存当前正在进行的工作， 比如想pull 最新代码，又不想加新commit； 或者另外一种情况，为了fix 一个紧急的bug，先stash，使返回到自己上一个commit，改完bug之后再stash pop，继续原来的工作。

多次使用 :code:`git stash` 命令后，你的栈里将充满了未提交的代码，这时候你会对 '将哪个版本应用回来' 有些困惑，
  
.. code-block:: bash
     
   git stash list  # 可以将当前的Git栈信息打印出来，
   git stash apply stash@{1}  # 你只需要将找到对应的版本号， 就可以将你指定版本号为stash@{1}的工作取出来，
   git stash clear # 将栈清空。
  
分支
~~~~~~~~~~~~~
.. code-block:: bash

   # 查看当前分支
   git branch

   # 新建分支
   git branch <new_branch>
   git branch <new_branch> <from_branch>  # 从特定分支拉取新分支

   # 跳到指定分支
   git checkout <branch_name> 

   # 分支跳转时，如果其它分支track 的文件在本分支untrack， 跳转失败， 解决方法如下:
   git clear -d -fx ""  # -d means remove untracked directories in addition to untracked files
                        # -x means ignored files are also removed as well as file unknown to git
                        # -f is required to force it to run
   # 新建并跳到指定分支
   git checkout -b <branch_name>

   # 分支重命名
   git branch -m <oldname> <newname>  # rename a branch while pointed to any branch
   git branch -m <newname> # rename the current branch

   # 将其它分支的改动融合到当前分支
   git merge <branch_name>  # 注意：融合成功的前提是融合对象和被融合对象没有冲突   
   git checkout --patch <branch_name> <filename>  # 将分支上某文件的改动融合到当前分支, --patch 会显示差异

   # 删除指定分支
   git branch -d <branch_name>

submodule
~~~~~~~~~~~

.. code-block:: bash

   # add 
   git submodule add <submodule repo url> <submodule path in current project>

   # 下载带submodule 的工程
   # 1. 下载工程后
   git submodule update --init --recursive
   # 2.
   git clone --recursive <project repo>
   # 3.
   git clone --recurse-submodules <project repo>

remove submodule

- Delete the relevant section from the ``.gitmodules`` file.
- Stage the .gitmodules changes :command:`git add .gitmodules`
- Delete the relevant section from ``.git/config`` .
- Run :command:`git rm --cached <path_to_submodule>` (no trailing slash).
- Run :command:`rm -rf <.git/modules/path_to_submodule>`
- Commit :command:`git commit -m "Removed submodule <name>"`
- Delete the now untracked submodule files :command:`rm -rf path_to_submodule`

   

复制工程项目
~~~~~~~~~~~~~
.. code-block:: bash

   # 复制
   git clone <remote_location> <local_clone_name>
   git clone -b <new_branch_name> <remote_location>  # clone时创建新的分支替代默认Origin HEAD（master）

   # 查看被复制项目的信息：
   git remote -v
     
   # 同步下载的工程：
   git fetch
   git merge origin/master

:code:`fetch` fetched new commits from the remote into local copy of the Git project, those commits are on the **origin/master**  branch, it will not merge changes from the remote into your local repository. Which means your local master branch has not been updated yet, so you need to integrate **origin/master** into your local **master** branch.    

上传自己的更改
~~~~~~~~~~~~~~~~
.. code-block:: bash

   git push origin <local_branch_name>

基于Git的团队合作 
---------------------
`A successful Git branching model <http://nvie.com/posts/a-successful-git-branching-model/>`_
 
一般流程 
~~~~~~~~
- 构建项目repo 并从 master 分支拉取develop 分支
- Fetch and merge changes from the remote develop branch
- Create a branch(feature branch) to work on a new project feature
- Develop the feature on your branch and commit your work
- Fetch and merge from the remote again (in case new commits were made while you were working)
- Push your branch up to the remote for review

分支结构
~~~~~~~~
+ **master** - 在git repo下主分支的职责主要就是负责记录stable版本的迭代，当在beta版本的项目或是开发版本的项目得到了充分的验证之后，我才能将分支并入master分支。master分支永远是production-ready的状态，即稳定可产品化发布的状态。

+ **develop** - 平常开发的一个主要分支了，不管是要做新的feature还是需要做bug fix，都是从这个分支分出来做。在这个分支下主要负责记录开发状态下相对稳定的版本，即完成了某个feature或者修复了某个bug后的开发稳定版本。
  
+ **feature branches** - 这是由许多分别负责不同feature开发的分支组成的一个分支系列。new feature主要就在这个分支系列下进行开发。当我在一个大的develop的迭代之下，往往我们会把每一个迭代分成很多个功能点，并将功能点分派给不同人的人员去开发。每一个人员开发的功能点就会形成一个feature分支，当功能点开发测试完毕之后，就会合并到develop分支去。
  
+ **release branches** - 这是由多个分支组成的一个分支系列。这个分支系列  **从develop** 分支出来，也就是预发分支。在预发状态下，我们往往会进行预发环境下的测试，如果出现缺陷，那么就在该release分支下进行修复，修复完毕测试通过后，即分别并入master分支后develop分支，随后master分支做正常发布。 
  
+ **hotfixes** - 这个分支系列也就是我们常说的紧急线上修复，当线上出现bug且特别紧急的时候，就可以从 **master** 拉出分支到这里进行修复，修复完成后分别并入master和develop分支。

.. image:: /_static/tools/git_repo_branch.jpg


mac 上搭建 git server
---------------------

.. Note::
        server 和 client 必须在同一个局域网

1. 创建名为 git 的普通用户（系统偏好与设置-> 用户和群组）
2. 开启ssh服务 （系统偏好与设置 -> 共享 -> 远程登录 & 仅git ）
3. 将 git 用户添加到sudoers

   .. code-block:: bash
   
      sudo chmod u+w /etc/sudoers
      sudo vim /etc/sudoers
      # 找到 "root ALL=(ALL) ALL", 在其下添加 “git ALL=(ALL) ALL” , 然后保存退出。   
      # 第一个ALL是指网络中的主机，我们后面把它改成了主机名，它指明git可以在此主机上执行后面的命令。  
      # 第二个括号里的ALL是指目标用户，也就是以谁的身份去执行命令。   
      # 最后一个ALL指命令名。  
      sudo chmod 440 /etc/sudoers # 把/etc/sudoers权限改回440 
     
      # 此时可以在客户端测试是否能连接：ssh git@servername ，这里的servername 即服务端terminal显示的名称.local

4. 增加安全性，使用ssh rsa 公钥

   1) 在client 创建 ssh rsa 公钥 （若已创建，则跳过此步）
      
      .. code-block:: bash
      
          cd ~
          ssh-keygen -t rsa #默认存放路径 ~/.ssh/id_rsa.pub

   2) 把ssh rsa 公钥复制到server端

      .. code-block:: bash

          ssh git@servername mkdir .ssh #登录 server 并创建 .ssh 文件夹
          scp ~/.ssh/id_rsa.pub git@servername:.ssh/authorized_keys
      
   3) 修改server 端的sshd_config

      .. code-block:: bash

          ssh git@servername
          cd /etc/ssh
          sudo chmod 666 sshd_config
          vim sshd_config
          # PermitRootLogin yes 改为 no
          # 去掉下面几项前的注释
          # RSAAuthentication yes
          # PubKeyAuthentication yes
          # AuthorizedKeyFile  .ssh/authorized_keys
          # PasswordAuthentication no
          # PermitEmptyPasswords no
          # UsePAM yes 改为 no

5. 使用例子

   1) 在 server 端创建空的 repository
     
      .. code-block:: bash
      
          cd path/to/repo
          mkdir <newreponame.git>  # 注意这里新建的项目所属用户得是上面创建的用户（这里是git), 所以最好是ssh 到server端自行创建，否则会出现无法上传
          cd <newreponame.git>
          git init --bare  # --bare 只是用来存储 pushes，不会当作本地repository 来使用

          # 可以增加 --shared 参数来限制用户权限
          # 1.bare repo
          git init --bare --shared=group <repo_dir.git>  # sets some important variables in repodir/config 
                                                         # "core.sharedRepository=group" and "receive.denyNonFastforwards=true")
                                                         # 若已创建: cd <repo_dir.git>/
                                                         #           git config core.sharedRepository group
          chgrp -R <group_name> <repo_dir.git>      # Change files and directories' group
          chmod -R g+rw <repo_dir>                  # Change permissions
          chmod g-w  <repo_dir.git>/objects/pack/*  # Git pack files should be immutable
          chmod g+s `find <repo_dir.git> -type d`   # New files get group id of directory
          # 2.non-bare repo  
          cd <project_dir>/                         # Enter inside the project directory
          git config core.sharedRepository group    # Update the git's config
          chgrp -R <group_name> <repo_dir>          # Change files and directories' group
          chmod -R g+rw <repo_dir>                  # Change permissions
          chmod g-w  <repo_dir.git>/.git/objects/pack/*             # Git pack files should be immutable
          chmod g+s `find <repo_dir.git> -type d`   # New files get group id of directory

          # 或者把client端的现有仓库导出为裸仓库(即一个不包含当前工作目录的仓库),
          # 再移到服务器上 (不推荐!!： remote 错误)
          git clone --bare <my_project>/.git <save_project.git> 
          scp -r <save_project.git> user@server_name:path/to/parent_dir

   2）在client 端，创建local repository 并 push
 
      .. code-block:: bash
      
          cd path/to/project
          git init 
          git add .
          git commit -m "init commit"
          git remote add origin git@servername:path/to/newreponame.git
          git push origin master

          # 创建远程分支
          git push origin <local_branch_name>         # 提交本地分支到远程的默认分支
          git push origin <local_branch_name>:<remote_branch_name>    # 提交本地分支到远程的特定分支
        
          # 删除远程的分支
          git push origin :<remote_branch_name>               # 1.刚提交的远程分支将被删除，但是本地还会保留
          git branch -r -d origin/<remote_branch_name>        # 或者这样 # 2.
        
          # 若要删除的远程为默认分支，则需将修改参考的当前默认分支, 在远程输入：
          git symbolic-ref HEAD refs/heads/<branch_name>

   3) 在client 端，获取server端的更改

      .. code-block:: bash

          git pull origin master  # 注意：pull相当于是fetch 和 merge 的结合，实际使用时，fetch 更安全  

          # 拉取远程分支
          git fetch origin <远程分支名> # 1.直接拉取
          git checkout -b <本地分支名> origin/<远程分支名>  # 2.本地新建分支, 把此分支放入其中
          git checkout -t <remote_name/branch_name> #clone 指定分支（= git checkout -b <branch_name> <remote_name/branch_name>）
        
          # 本地修改的情况下拉取远程分支会报错，此时先stash，再pull， 根据冲突做修改， 
          # 然后stash pop 以重新获取之前的修改
          git stash
          git pull origin master
          git stash pop

6. `GitRepo acess control <https://wincent.com/wiki/Git_repository_access_control>`_

ubuntu 上搭建git server
-----------------------

参考 `Git on the Server - Setting Up the Server <https://git-scm.com/book/en/Git-on-the-Server-Setting-Up-the-Server>`_

错误集锦
---------

+ 如果拉取远程分支失败，‘cannot update paths and switch to branch’, 原因可能是remote 没有 update
  
  .. code-block:: bash

      git remote show <remote_name>  # 查看想拉取的分支是否在 new remote branch
      git remote update              # 若是， 更新  remote
      git fetch

+ the ECDSA host key for 'lxh-mac.local' differs from the key for the IP address '192.168.1.101'  

  .. code-block:: bash 

     ssh-keygen -R 192.168.1.101 

