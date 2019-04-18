React_Native
=============

安装
-----

.. code-block:: bash

   // 1.安装Node.js
   // 1.1 mac
   brew install node
   //   设置npm镜像
   npm config set registry https://registry.npm.taobao.org --global
   npm config set disturl https://npm.taobao.org/dist --global
   //   npm 降级
   npm install npm@4 -g  # @后面是版本号

   // 1.2 ubuntu
   // 参照网址 https://github.com/nodesource/distributions#debinstall 安装 nodejs
   npm install npm@latest -g
   // 如果安装完后有权限问题, 更改npm相关路径权限(其它方法参考https://docs.npmjs.com/getting-started/fixing-npm-permissions)
   sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}

   // 2. 
   npm install -g create-react-native-app
   npm install -g yarn react-native-cli
   // 设置yarn 的镜像源
   yarn config set registry https://registry.npm.taobao.org --global
   yarn config set disturl https://npm.taobao.org/dist --global

   // 3.辅助工具
   brew install watchman  # 监视文件系统变更
   brew install flow  # 静态的JS类型检查工具

   // 4.语法检查
   npm install -g eslint
   npm install -g babel-eslint
   npm install -g eslint-plugin-react

安装完成后在根目录下新建~/.eslintrc::

    {
        "parser": "babel-eslint",
        "env": {
            "browser": true,
            "node": true
    
        },
        "settings": {
            "ecmascript": 6,
            "jsx": true
    
        },
        "plugins": [
            "react"
    
        ],
        "rules": {
            "strict": 0,
            "quotes": 0,
            "no-unused-vars": 0,
            "camelcase": 0,
            "no-underscore-dangle": 0
    
        }
    }

最后，设置Syntastic使用eslint::

    let g:syntastic_javascript_checkers = ['eslint']


开发
----

.. code-block:: bash

   react-native init <app_name> --version 0.44.3  #
   cd <project_dir>
   react-native [run-ios | run-android]


   // 实时调试
   adb reverse tcp:8081 tcp:8081
   adb shell input keyevent 82

   // node 版本管理
   n

打包
-----

出错的可能：

1. assets/res 下的资源文件重复
2. __DEV__ 判断的问题, 可能导致白屏
3. 控件代码有问题

调试工具 reactotron
-------------------

1. 安装

.. code-block:: bash

   brew update
   brew cask install reactotron

   # 如果想要终端版，运行
   npm install -g reactotron-cli

2. 使用

   - 开启 reactotron
   - 为项目文件加包 
     
     .. code-block:: bash
     
         npm i --save-dev reactotron-react-native
         npm i --save-dev reactotron-redux  // for redux

   - 加配置文件 *ReactotronConfig.js* 并在入口处导入

     .. code-block:: js

         // ReactotronConfig.js
         import { reactotronRedux } from 'reactotron-redux'
         
         Reactotron
           .configure({ name: 'React Native Demo' }) // controls connection & communication settings
           .useReactNative() // add all built-in react native plugins
           .use(reactotronRedux()) // then add it to the plugin list
           .connect() // let's connect!
         // 更具体的写法参考官网

     .. code-block:: js

         // App.js (Create React Native App) or index.ios.js and index.android.js
         import './ReactotronConfig'

     同时将redux 的createStore 替换为 Reactotron 的createStore

     .. code-block:: js

         const store = Reactotron.createStore(rootReducer, compose(middleware))

   - 运行代码




   



脚手架 ignite
--------------

1. 安装及基本命令

   .. code-block:: bash

      # 安装
      npm install -g ignite-cli

      # 初始化新项目
      ignite new <AppName>

      # 添加和移除plugins
      ignite add <PackageName>
      ignite remove <PackageName>

      # 自动构建代码
      ignite generate [screen| component| map| redux| saga] <name>

2. 配置

   .. code-block:: javascript

     /*file:Config/DebugConfig.js*/
    useFixtures: true, // 使用本地数据，这里设为true让我们可以在没有后端支持的情况实现数据mock
    ezLogin: false, // 暂时还不知道这个ezlogin干嘛的
    yellowBox: __DEV__, // 黄屏警告 开发模式默认打开
    reduxLogging: __DEV__, // redux-logging当redux变更时打log 开发模式默认打开
    includeExamples: __DEV__, // 载入初始的includeExamples 开发模式默认打开
    useReactotron: __DEV__ // 使用useReactotron 开发模式默认打开


   .. code-block:: javascript

       // Sagas/index.js
       ...
       const api = DebugConfig.useFixtures ? FixtureAPI : API.create()
       ...
       takeLatest(StartupTypes.STARTUP, startup)
       takeLatest(GithubTypes.USER_REQUEST, getUserAvatar, api)
       ...




一些包
-------

`react-native link` 的作用就是设置好android 和 ios 中相应的配置文件内容

android

1. app/build.gradle

   .. code-block:: groovy

       dependencies {
           ...
           compile project(':<package name>')
           ...
       }

2. settings.gradle
   
   .. code-block:: groovy 

       include ':<package-name>'
       project(':<package-name>').projectDir = new File(rootProject.projectDir, '../node_modules/<package-name>/android')
       // 最后的路径一般如上，但具体视包而定 (如 react-native-maps 不同版本，此处不一致)

3. app/src/main/Java/[...]/MainApplication.java (部分包需要)

   .. code-block:: java

       import <java package name>.<package func>;  // <java package name> 对应包的 AndroidManifest.xml 中的 package 参数

       @Override
       protected List<ReactPackage> getPackages() {
         return Arrays.<ReactPackage>asList(
             ...                      // 注意逗号
             new <package func>()     // append this line
         );
       }

4. app/src/main/AndroidManifest.xml (部分包需要)

   .. code-block:: xml

       // 一些权限
       <uses-permission .../> 

       // api 密钥
       <application 
        ...
        <meta-date
         android:name=
         android:value= />
       </application>

ios



realm
~~~~~~~

.. code-block:: bash

   npm install --save realm
   rnpm link realm   //关联realm库

   // 有可能链接不上，也可能链接上了没有自动添加代码，需要手动处理：
   // 1. settings.gradle中是否存在下面的代码
   include ':realm'
   project(':realm').projectDir = new File(rootProject.projectDir, '../node_modules/realm/android')

   // 2. app/build.gradle中是否存在如下代码
   dependencies {    
     compile project(':realm') 
     ...
   }

react navigation
~~~~~~~~~~~~~~~~~

.. code-block:: javascript
   
   // 注意这里二者的差别
   this.props.navigation.navigate(<routeName>, {<para name>: <para>, ...}) // 不能有参数名
                                                                           // {} 内传入的参数保存在navigation.state.params.<para name>
   this.props.navigate({routeName: <routeName>}) // 必须有参数名

   // 下面两种做法的结果有时会不同, why?
   this.props.navigation.dispatch(NavigationActions.navigate())
   this.props.navigation.navigate()

react-native-maps
~~~~~~~~~~~~~~~~~

.. note:: 

    不同版本的配置不一样，注意区分


UI
----

1 inch = 2.54cm
 
逻辑分辨率(pt) = D_w x D_h dot/point

设备分辨率(px) = P_w x P_h pixel 

设备像素比(pixel ratio) = P_w / D_w = P_h / D_h 

独立像素(dp) = 屏幕点密度为160ppi时1px长度
 
点密度(dpi, dot per inch) =  :math:`\sqrt(D_w^2 + D_h^2)/diagonal inch`

像素密度(ppi, pixel per inch) = :math:`\sqrt(P_w^2 + P_h^2)/diagonal inch`  

react native 的 Dimensions 获取的是 pt 为单位的尺寸


坑
---

1. 编译运行

    Android端: 安装好环境后，直接运行 `react-native run-android` , 该命令实际是直接使用了android目录下的gradlew命令，运行后这里报错了，主要是一些依赖包的问题。这里使用Android studio导入工程目录下的android，然后运行安装，ok 了。
   
    ios 端: 注意修改AppDelegate.m中的server地址::

        jsCodeLocation = [NSURL URLWithString:@"http://电脑ip:8081/index.ios.bundle?platform=ios&dev=true"];

2. unable to load script from assets index.android.bundle. Make sure...

    - 可能是代理问题
    - adb revere tcp:8081 tcp:8081



3. Android 调试菜单不出现
   
   正常摇一摇手机可以出调试菜单的，然而并没有出来，这里需要设置该应用的悬浮窗权限，设置后可以使用。

4. 有时组件不能正常显示是因为导入有问题，例如 

   - index.js 形式直接导入，无需default, import 到文件夹级， 加{}
   - xxx.js 形式导入，未加default，import 到文件级/文件夹级，加{}
   - xxx.js 形式导入，加了default, import 到文件级，不加{}
   - 使用UI库时注意是否相关位置没有配置好
5. 文件名中不能有空格
6. 注意看断点处的this 状态，有的时候没有 bind(this) 函数的this 与预期不同
7. redux 处的 ...state 在函数与reducer 中不一样
