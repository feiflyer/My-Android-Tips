# My-Android-Tips
这里纪录自己在实际开发过程中遇到的一些坑
========================================

1、使用通知栏显示自定义通知布局时不设置setSmallIcon通知可能会显示不出来？
-------------------------------------------------------------------------
2、 Android属性动画values这个参数是值你要控制的属性的过过渡值
--------------------------------------------------------------------
     比如：
     ObjectAnimator.ofFloat(mToolbar , "translationX" , 200 , 300)；这段代码是是指mToolbar这个控件的
     translationX这个属性从0到200，再从200到300的过程；
3、  使用Android Studio,在项目中添加.so文件,报错java.lang.UnsatisfiedlinkError: Native method not found
-------------------------------------------------------------------------------------------------------
     解决方法: 
     在 src -> main 里面创建 jniLibs -> armeabi,然后添加到此文件夹中即可 
     或者在build.gradle中添加以下代码:  
            sourceSets { 
                main {
                    jniLibs.srcDirs = ['libs']
                }
            }
4、使用System.loadLibrary()方法加载so库的时候传入的so文件名需要忽略掉文件名前面的lib；
-------------------------------------------------------------------------------------
   比如你要加载libMeggaRun.so这个库，正确的代码应该是System.loadLibrary("MeggaRun")，
   而不是System.loadLibrary("libMeggaRun");
5、ListView的setEmptyView(emptyView)方法和addHeaderView(view)或addFooterView(view)有冲突；
----------------------------------------------------------------------------------------------
   使用了setEmptyView(emptyView)方法后可能会使得addHeaderView(view)或addFooterView(view)添加的view不可见；
   
# 6、获取键盘的高度的方法#

  利用mYourView.getWindowVisibleDisplayFrame(r)在初始化中拿到一个View的可见区域，监听该View的布局变化，判断可见区域可计算出键盘的高度。
# 7、使用java环境keyTool命令行自动生成签名文件
    keytool -genkey -dname "CN=fingki,OU=server,O=server,L=bj,ST=bj,C=CN" -alias dongbai.keystore -keyalg RSA -keystore dongbai.keystore -keypass 123456 -storepass 123456 -validity 20000

