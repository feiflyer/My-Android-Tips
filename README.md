# My-Android-Tips
## 这里纪录自己在实际开发过程中遇到的一些坑
### 1、使用通知栏显示自定义通知布局时不设置setSmallIcon通知可能会显示不出来？

### 2、 Android属性动画values这个参数是值你要控制的属性的过过渡值

 比如：
 `ObjectAnimator.ofFloat(mToolbar , "translationX" , 200 , 300)`；这段代码是是指mToolbar这个控件的
 translationX这个属性从0到200，再从200到300的过程；
 
### 3、 使用Android Studio,在项目中添加.so文件,报错`java.lang.UnsatisfiedlinkError: Native method not found`

 解决方法: 
 在 src -> main 里面创建 jniLibs -> armeabi,然后添加到此文件夹中即可 
 或者在build.gradle中添加以下代码:  
 ```
        sourceSets { 
            main {
                jniLibs.srcDirs = ['libs']
            }
        }
```
### 4、使用System.loadLibrary()方法加载so库的时候传入的so文件名需要忽略掉文件名前面的lib；

比如你要加载`libMeggaRun.so`这个库，正确的代码应该是`System.loadLibrary("MeggaRun")`， 而不是`System.loadLibrary("libMeggaRun")`;

### 5、ListView的setEmptyView(emptyView)方法和addHeaderView(view)或addFooterView(view)有冲突；

使用了setEmptyView(emptyView)方法后可能会使得addHeaderView(view)或addFooterView(view)添加的view不可见；

### 6、获取键盘的高度的方法
利用`mYourView.getWindowVisibleDisplayFrame(r)`在初始化中拿到一个View的可见区域，监听该View的布局变化，判断可见区域可计算出键盘的高度。

### 7、使用java环境keyTool命令行自动生成签名文件

`keytool -genkey -dname "CN=fingki,OU=server,O=server,L=bj,ST=bj,C=CN" -alias dongbai.keystore -keyalg RSA -keystore dongbai.keystore -keypass 123456 -storepass 123456 -validity 20000`

### 8、关于图片压缩的几点说明：
 图片压缩分为尺寸压缩和质量压缩两种；质量压缩它不会减少图片的像素,比方说, 
 你的图片是300K的, 1280*700像素的, 经过该方法压缩后, File形式的图片是在100以下, 以方便上传服务器, 
 但是你BitmapFactory.decodeFile到内存中,变成Bitmap时,它的像素仍然是1280*700；
 质量压缩实例代码：
 ```
 private Bitmap compressImage(Bitmap image) {  
    ByteArrayOutputStream baos = new ByteArrayOutputStream();  
    //质量压缩方法，这里100表示不压缩，把压缩后的数据存放到baos中  
    image.compress(Bitmap.CompressFormat.JPEG, 100, baos);
    int options = 100;  
    //循环判断如果压缩后图片是否大于100kb,大于继续压缩 
    while ( baos.toByteArray().length / 1024>100) {          
        baos.reset();//重置baos即清空baos  
        //这里压缩options%，把压缩后的数据存放到baos中 
        image.compress(Bitmap.CompressFormat.JPEG, options, baos); 
        options -= 10;//每次都减少10  
    }  
    //把压缩后的数据baos存放到ByteArrayInputStream中 
    ByteArrayInputStream isBm = new ByteArrayInputStream(baos.toByteArray()); 
    Bitmap bitmap = BitmapFactory.decodeStream(isBm, null, null);//把ByteArrayInputStream数据生成图片  
    return bitmap;  
}
```
尺寸压缩改变了图片的像素点，改变了bitmap在内存中的大小和尺寸大小；实例代码：
```
private Bitmap compressImage(Bitmap image) {
BitmapFactory.Options newOpts = new BitmapFactory.Options();  
//开始读入图片，此时把options.inJustDecodeBounds 设回true了  
newOpts.inJustDecodeBounds = true;  
Bitmap bitmap = BitmapFactory.decodeStream(isBm, null, newOpts);  
newOpts.inJustDecodeBounds = false;  
int w = newOpts.outWidth;  
int h = newOpts.outHeight;  
//现在主流手机比较多是800*480分辨率，所以高和宽我们设置为  
float hh = 800f;//这里设置高度为800f  
float ww = 480f;//这里设置宽度为480f  
//缩放比。由于是固定比例缩放，只用高或者宽其中一个数据进行计算即可  
int be = 1;//be=1表示不缩放  
if (w > h && w > ww) {//如果宽度大的话根据宽度固定大小缩放  
    be = (int) (newOpts.outWidth / ww);  
} else if (w < h && h > hh) {//如果高度高的话根据宽度固定大小缩放  
    be = (int) (newOpts.outHeight / hh);  
}  
if (be <= 0)  
    be = 1;  
newOpts.inSampleSize = be;//设置缩放比例  
//重新读入图片，注意此时已经把options.inJustDecodeBounds 设回false了  
isBm = new ByteArrayInputStream(baos.toByteArray());  
bitmap = BitmapFactory.decodeStream(isBm, null, newOpts);  
return bitmap;
} 
```
至于什么时候采用尺寸压缩，什么时候采用质量压缩由程序需求决定，一般来说为了优化内存方便显示则采用尺寸压缩， 为了网络传输和节省手机磁盘存储空间则进行质量压缩； bitmap进行质量压缩并不会减少它所占用的内存，所以不必为了减少内存而进行质量压缩， 一般上传图片的流程是先判断图片文件的大小，如果超出一定大小则进行质量压缩，保存缓存文件， 然后上传缓存文件，缓存文件上传成功后删除缓存文件；

### 9、如果想让PorterDuffXferMode按照预期Demo（或者效果图）的效果图像实现，必须满足以下条件：
 - 、关闭硬件加速。
 
 - 、两个bitmap大小尽量一样。
 
 - 、背景色为透明色。
 
 - 、如果两个bitmap位置不完全一样，可能也是预期效果，只不过你看到的效果和你自己脑补的预期效果不一致。
 
### 10、单例的几种写法比较：

 - 单线程写法
 ```
public class Singleton {   
private static Singleton = new Singleton();
private Singleton() {}
public static getSignleton(){
    return singleton();
}
}
```
- 考虑线程安全的写法（volatile关键字是JDK1.5之后的产物）

```
public class Singleton {
private static volatile Singleton singleton = null;
private Singleton(){}
public static Singleton getSingleton(){
    synchronized (Singleton.class){
        if(singleton == null){
            singleton = new Singleton();
        }
    }
    return singleton;
 }    
}
```

- 兼顾线程安全和效率的写法
```
public class Singleton {
private static volatile Singleton singleton = null;
private Singleton(){
}
public static Singleton getSingleton(){
    if(singleton == null){
        synchronized (Singleton.class){
            if(singleton == null){
                singleton = new Singleton();
            }
        }
    }
    return singleton;
}    
}
```
- 静态内部类法（由于静态内部类只会被加载一次，所以这种写法也是线程安全的）
```
public class Singleton {
private static class Holder {
    private static Singleton singleton = new Singleton();
}
private Singleton(){}

public static Singleton getSingleton(){
    return Holder.singleton;
}
}
```
### 11、关于fitsSystemWindows属性和android:clipToPadding属性的介绍
 [fitsSystemWindows和android:clipToPadding介绍](http://gold.xitu.io/entry/56f25693f3609a00549b4102)
 
### 12、判断ListView到底底部或顶部的方法
 ```
 if ((firstVisibleItem + visibleItemCount) == totalItemCount) {
    View lastVisibleItemView = mListView.getChildAt(mListView.getChildCount() - 1);
    if (lastVisibleItemView != null && lastVisibleItemView.getBottom() == mListViewHeight) {
        Log.d("ListView", "#####滚动到底部######");       
    }
}
```
