



# Toast   吐司消息提示框



*Android用于提示信息的一个控件——Toast(吐司)！Toast是一种很方便的消息提示框,会在 屏幕中显示一个消息提示框,没任何按钮,也不会获得焦点一段时间过后自动消失！ 非常常用！*





**`Toast` 会浮在所有的窗口之上，而且不会识别任何手势，比如单击动作等**



**需要**

```
import android.widget.Toast;
```





<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103170324930.png" alt="image-20221103170324930" style="zoom:50%;" />

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103170347265.png" alt="image-20221103170347265" style="zoom:50%;" />



```java
binding.button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT).show();
        //             第一个是上下文对象！对二个是显示的内容！第三个是显示的时间
        startActivity(new Intent(MainActivity.this, CameraActivity.class));
    }
});
// 点击完按键，在显示界面弹出一个自定义信息的信息提示，过一会自定就消失，用作简单的提示作用

Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT).show();
这个就简洁的用法，还可以进行更个性的设置

```





```
Toast toast = Toast.makeText(global_context, str, showTime);  
toast.show();
```



```java
 binding.button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
//                Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT).show();
                // 先例化一个 Toast对象  第一个是上下文对象！对二个是显示的内容！第三个是显示的时间
                Toast toast = Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT);
                //调用setGravity设置Toast显示的位置
                toast.setGravity(Gravity.CENTER_VERTICAL|Gravity.CENTER_HORIZONTAL , 0, 0);  //设置显示位置
                //返回 Toast 的 View
                TextView v2 = (TextView) toast.getView().findViewById(android.R.id.message);
                //设置字体颜色
                v2.setTextColor(Color.BLUE);   
                // 显示消息提示内容
                toast.show();



                startActivity(new Intent(MainActivity.this, CameraActivity.class));
            }
        });
```





**findViewById方法则是系统自动生成的R类里面的ID子类里面根据所给的ID去从已有的xml布局文件中提取已经写好的View对象**



[https://www.runoob.com/w3cnote/android-tutorial-toast.html]: 



### **自定义消息提示**

参考：https://www.runoob.com/w3cnote/android-tutorial-toast.html

