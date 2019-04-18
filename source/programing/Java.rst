Java
======

一些概念
----------

关于gradle
~~~~~~~~~~~~

Gradle的一些基本依赖配置方式如下:

  .. code-block:: groovy
  
      compile fileTree(dir:'xxx',include:['*.jar','*.xxx'])  // 将某个目录下所有符合扩展名的文件作为依赖
	  compile 'com.xx.xx:ProjectName:Version'  // 配置Maven库为依赖
	  compile project(':otherModule')  // 配置另一个module作为本Module的依赖，被依赖的Module必须被导入到当前工程中
	  compile file('xxx.jar')  // 配置某个jar包作为依赖

什么是AAR包？ AAR包相比于jar包，区别在哪儿？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

假如我们希望提供一个带有资源文件的第三方库给别人使用，总不能直接把源代码给别人，但是我们知道eclipse打包的时候不能包含res的资源文件，于是Android在发布Android studio的时候就发布了一种独有的格式AAR，专门用于打包UI组件库。与jar相比其多了一些UI组件用到的属性、图片等一系列文件，它的好处在于你不需要再多创建一个Library Module，只需引用这个AAR文件即可，Android Sudio会自动把AAR包里的文件跟你的项目融合。

aar包含所有资源，class，xml布局文件以及res资源文件全部包含。注意是全部。jar只包含了class文件与清单文件，不包含资源文件，如图片等所有res中的文件。

什么是so库？什么是ABI？相关的处理器型号在构建APP时有什么区别？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

android系统目前支持以下七种不同的CPU架构：ARMv5，ARMv7 (从2010年起)，x86 (从2011年起)，MIPS (从2012年起)，ARMv8，MIPS64和x86_64 (从2014年起)，每一种都关联着一个相应的ABI。
应用程序二进制接口（Application Binary Interface, ABI）定义了二进制文件（尤其是.so文件）如何运行在相应的系统平台上，从使用的指令集，内存对齐到可用的系统函数库。

so库的好处::

	- so机制让开发者最大化利用已有的C和C++代码，达到重用的效果，利用软件世界积累了几十年的优秀代码；
	- so是二进制，没有解释编译的开消，用so实现的功能比纯java实现的功能要快；
	- so内存分配不受Dalivik/ART的单个应用限制，减少OOM；
	- 相对于java代码，二进制代码的反编译难度更大，一些核心代码可以考虑放在so中。

在Android Studio构建APP时可以选择构建时匹配的CPU架构。在project的build.gradle可以明确指定

.. code-block:: groovy

    //在buildType标签下声明
    ndk{
        abiFilters "armeabi","armeabi-v7a","x86"
    }
    
以上代码可以指定在构建时，生成支持这三类CPU的so库。

so库的load:

- 相对路径load: System.loadLibrary("media_jni"); 其中media_jni名字会被自动替换成libmedia_jni.so

  在使用相对路径load时，需要注意相应的so库是否被打入到 aar包的libs目录下。此处需要注意ABI类型
- 绝对路径load:System.load("/绝对路径/libmedia_jni.so");

  绝对路径可以避免这个问题，但是要确保具有相应路径的访问权限，在接入AAR时候，假设合作方是厂商ROM级别的，部分路径需要提前协调。

jni层的方法对应关系：

全路径，将\.置换为_ 例如，假设当前函数native_init函数位于android.media这个包中，它的全路径名应该是android.media.MediaScanner.native_init，而JNI层函数的名字是android_media_MediaScanner_native_init

Android Studio
---------------

一些常遇到的问题
~~~~~~~~~~~~~~~~

1. `Error: Execution failed for task ':app:transformClassesWithDexForDebug'.>com.android.build.api.transform.TransformException:com.android.ide.common.process.ProcessException:java.util.concurrent.ExecutionException:com.android.dex.DexException:Multiple`

   app module 和 lib module 导入了同样的jar包，导致jar包重复。

2. gradle降级

   修改项目级gradle 的buildscript -> dependencies -> classpath, module 级gradle的android 下相应选项

   此外可能涉及到support library， 如果也需要降级，那么
   
   1) 将相应库修改后，

   2) 在使用到activity的地方，将继承的类改到相应版本

   3) 修改资源文件（values/styles.xml）中相应的信息






3. 权限

   同时确认代码permission 和 手机app 权限

4. ndk 相关的项目要注意 ndk 版本

一些经验
~~~~~~~~~

ndk 编程时， jsoncpp 代码在 sdk < 20 的设备上无法运行，这是因为相应版本上的stl 没有完全实现，可以通过修改android_stl 为 c++_static
等改正，但是如果还使用到了其他so库，而它又不是在 c++_static 条件下编译的，那么就没办法了。

