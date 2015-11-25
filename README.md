# My-Android-Tips
这里纪录自己在实际开发过程中遇到的一些坑

1、 使用通知栏显示自定义通知布局时不设置setSmallIcon通知可能会显示不出来？
2、 Android属性动画values这个参数是值你要控制的属性的过过渡值，比如：
    ObjectAnimator.ofFloat(mToolbar , "translationX" , 200 , 300)；这段代码是是指mToolbar这个控件的
    translationX这个属性从0到200，再从200到300的过程；
