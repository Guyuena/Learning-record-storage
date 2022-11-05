　

# Intent



Intent，又称为意图，是一种运行时绑定机制，它能在程序运行的过程中链接两个不同的组件（Activity、Service、BroadcastReceiver）。通过Intent，程序可以向Android表达某种请求或意愿，Android会根据意愿的内容选择适当的组件来请求。



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103201216480.png" alt="image-20221103201216480" style="zoom:67%;" />



**对于向这三种组件发送intent有不同的机制：**

- 使用  Context.startActivity()   或    Activity.startActivityForResult()   ，传入一个intent来启动一个activity。使用 Activity.setResult()，传入一个intent来从activity中返回结果。

- 将intent对象传给Context.startService()来启动一个service或者传消息给一个运行的service。将intent对象传给 Context.bindService()来绑定一个service。

- 将intent对象传给 Context.sendBroadcast()，Context.sendOrderedBroadcast()，或者Context.sendStickyBroadcast()等广播方法，则它们被传给 broadcast receiver。



 在这些组件之间的通讯中，主要是由Intent协助完成的。Intent负责对应用中一次操作的动作、动作涉及数据、附加数据进行描述，Android则**根据此Intent的描述，负责找到对应的组件，将Intent传递给调用的组件，并完成组件的调用**。因此，Intent在这里起着一个媒体中介的作用，专门提供组件互相调用的相关信息，实现调用者与被调用者之间的解耦。



　　**通过Intent请求Activity，必须在AndroidManifest.xml文件中对被请求的Activity新增标签配置，否则会导致错误。**

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103200950157.png" alt="image-20221103200950157" style="zoom:67%;" />



### 用法

```java
        binding.button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
//                Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT).show();
                Toast toast = Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT);
                toast.setGravity(Gravity.CENTER_VERTICAL|Gravity.CENTER_HORIZONTAL , 0, 0);  //设置显示位置
                TextView v2 = (TextView) toast.getView().findViewById(android.R.id.message);
                v2.setTextColor(Color.BLUE);     //设置字体颜色
                toast.show();


                //启动活动activity
                //  startActivity(Intent intent )显式启动新的Activity
                //startActivity()接收一个Intent类对象来启动
                // 直接以“类名称”来指定要启动哪一个Activity
                // activity.class就是要指定启动的activity
                startActivity(new Intent(MainActivity.this, CameraActivity.class));
                // 也就是说在按下按键后，用当前主activity的具有的上下文信息去启动相机这个activity活动

                /** 等价于下面 */
                //Intent intent = new Intent(MainActivity.this, CameraActivity.class);
                //startActivity(intent);
                
                /**再或者下面的写法*/
                Intent intent2 = new Intent(); // 例化一个空的Intent对象
                //使用.setClass()方法来配置intent
                //intent.setClass(MainActivity.this, SecondActivity.class);
                intent.setClass(MainActivity.this, CameraActivity.class);
                
                //setClass函数的第一个参数是一个Context对象
                //Context是一个类，Activity是Context类的子类，也就是说，所有的Activity对象，都可以向上转型为Context对象
                //setClass函数的第二个参数是一个Class对象，在当前场景下，应该传入需要被启动的Activity类的class对象
                //当前最常用-简洁的用法还是  Intent intent = new Intent(MainActivity.this,SecondActivity.class);
                
            }
        });
```



## Intent类的属性



- component(组件)：目的组件
- action（动作）：用来表现意图的行动
- category（类别）：用来表现动作的类别
- data（数据）：表示与动作要操纵的数据
- type（数据类型）：对于data范例的描写
- extras（扩展信息）：扩展信息
- Flags（标志位）：期望这个意图的运行模式

**Intent类型分为显式Intent（直接类型）、隐式Intent（间接类型）。官方建议使用隐式Intent。上述属性中，component属性为直接类型，其他均为间接类型。**





**隐式启动**Activity的intent到底发给哪个activity，需要进行三个匹配，一个是action，一个是category，一个是data，可以是全部或部分匹配

**举例**

参考来源： https://blog.csdn.net/luohai859/article/details/7368745

```xml
MainActivity.java --主Activity

TestActivity.java --需要隐式启动的Activity


(1) 根据Action和Category来进行匹配

<activity android:name=".TestActivity" android:label="TestActivity">

<intent-filter >

<action android:name="cc.android/myaction.leo"/>

<category android:name="android.intent.category.DEFAULT"/>

</intent-filter>

</activity>



在MainActivity.java里启动它:

intent.setAction( "cc.android/myaction.leo");// 匹配XML中的标识字符串

//不加下面这行也行，因为intent的这个属性默认值即系Intent.CATEGORY_DEFAULT

intent.addCategory(Intent.CATEGORY_DEFAULT);
startActivity( intent );
```













参考详解：https://blog.csdn.net/rainbowcode/article/details/119390187



**2、Action（动作）：用来表现意图的行动**

日常生活中，描述一个意愿或愿望的时候，总是有一个动词在其中。比如：我想“做”三个俯卧撑；我要“写” 一封情书，等等。在Intent中，Action就是描述做、写等动作的，当你指明了一个Action，执行者就会依照这个动作的指示，接受相关输入，表现对应行为，产生符合的输出。在Intent类中，定义了一批量的动作，比如ACTION_VIEW，ACTION_PICK等， 基本涵盖了常用动作。加的动作越多，越精确。

**Action 是一个用户定义的字符串**，用于描述一个 Android 应用程序组件，一个 Intent Filter 可以包含多个 Action。在 AndroidManifest.xml 的Activity 定义时，可以在其 <intent-filter >节点指定一个 Action列表用于标识 Activity 所能接受的“动作”。



<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103204514031.png" alt="image-20221103204514031" style="zoom:67%;" />







在AndroidManifest.xml中注册这个动作

![image-20221103213646288](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103213646288.png)

上方代码，表示SecondActicity可以匹配第4行的MY_ACTION这个动作，此时，如果在其他的Acticity通过这个action的条件来查找，那SecondActicity就具备了这个条件。

注：如果没有指定的category，则必须使用默认的DEFAULT（即上方第5行代码）

也就是说：只有<action>和<category>中的内容同时能够匹配上Intent中指定的action和category时，这个活动才能响应Intent。如果使用的是DEFAULT这种默认的category，在稍后调用startActivity()方法的时候会自动将这个category添加到Intent中。

<img src="C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20221103213924892.png" alt="image-20221103213924892" style="zoom:67%;" />











**3、category（类别）：用来表现动作的类别**

Category属性也是作为<intent-filter>子元素来声明的。例如：

<intent-filter>

　　<action android:name="com.vince.intent.MY_ACTION"></action>

　　<category android:name="com.vince.intent.MY_CATEGORY"></category> 

　　<category android:name="android.intent.category.DEFAULT"></category> 

</intent-filter>  

Action 和category通常是放在一起用的



更多Android Intent  详解：https://blog.csdn.net/rainbowcode/article/details/119390187)





#### Intent.putExtra()

方法对其传入数据

```
intent.putExtra("data", "当前是页面2，信息来自页面1");
```

#### Activity.getIntent()

获得当前Activity的Intent。

```
Intent intent =getIntent();
```



#### Intent.getXxxExtra()

方法获得其中保存的数据。

```
 //getXxxExtra方法获取Intent传递过来的数据
String msg=intent.getStringExtra("data");
```





## 从Activity中返回数据

1. **传递数据需要使用**Activity.startActivityForResult()方法启动Activity，需要传递请求码，而不是Activity.startActivity()。
2. 返回数据的时候，调用Activity.setResult()方法设置返回Intent以及返回码。
3. **需要重写**源Activity的onActivityResult()方法以便于接受返回的Intent，在onActivityResult()中会判断请求码和响应码。



请求码-响应码



### 举例

通过一个例子说明从Activity返回数据。此程序有两个Activity，在MainActivity中输入加法运算的计算数，跳转到otherActivity中输入计算结果，并在点击返回后，把计算结果输出到MainActivity中。

```java
public class MainActivity extends Activity {
     private EditText one,two,result;     private Button btn;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        one=(EditText)findViewById(R.id.one);
        two=(EditText)findViewById(R.id.two);
        result=(EditText)findViewById(R.id.result);
        btn=(Button)findViewById(R.id.btnGo);
        btn.setOnClickListener(new View.OnClickListener() {
            
            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub
                int ione=Integer.parseInt(one.getText().toString());
                int itwo=Integer.parseInt(two.getText().toString());
                
                Intent intent=new Intent(MainActivity.this, otherActivity.class);
                intent.putExtra("one", ione);
                intent.putExtra("two", itwo);                
                
                //启动需要监听返回值的Activity，并设置请求码：requestCode
                // 因为有返回值，所以启动函数是startActivityForResult而不是startActivity
                 startActivityForResult(intent, 1);
             }
         }); 
         
     }
     // 因为进行activity之间的信息交互后，对方有返回值，为了接收返回值就要
     // 重写onActivityResult()方法
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         //当otherActivity中返回数据的时候，会响应此方法
         //requestCode和resultCode必须与请求startActivityForResult()和返回setResult()的时候传入的值一致。
         if(requestCode==1&&resultCode==2)
         {
             int three=data.getIntExtra("three", 0);
             result.setText(String.valueOf(three));
         }
     }
 
     @Override
     public boolean onCreateOptionsMenu(Menu menu) {
         // Inflate the menu; this adds items to the action bar if it is present.
         getMenuInflater().inflate(R.menu.main, menu);
         return true;
     }
 }
```



```java
 public class otherActivity extends Activity {
     TextView tvShow;
     EditText etResult;
     @Override
     protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
        setContentView(R.layout.other);
         
         tvShow=(TextView)findViewById(R.id.tvShow);
         etResult=(EditText)findViewById(R.id.etResult);
         
         Intent intent=getIntent();
         int a=intent.getIntExtra("one", 0);
         int b=intent.getIntExtra("two", 0);
         tvShow.setText(a+" + "+b+" = "+" ? ");        
         
         Button btnResult=(Button)findViewById(R.id.btnReturn);
        btnResult.setOnClickListener(new View.OnClickListener() {            
             @Override
             public void onClick(View v) {
                 //新声明一个Intent用于存放放回的数据
                 Intent i=new Intent();
                 int result=Integer.parseInt(etResult.getText().toString());
                 i.putExtra("three", result);                
                 setResult(2, i);//设置resultCode，onActivityResult()中能获取到
                 finish();//使用完成后结束当前Activity的生命周期
             }
         });                
     }
 }
```





## Intent 启动方法

### startActivity()

此程序仅在两个页面之间相互跳转，但是每次跳转会创建新的Activity，所以在startActivity()之后需要调用finish()销毁当前Activity，如果不销毁，多次跳转后，程序的Activity栈中会存放多个Activity，点击设备的返回按钮，会发现会一直向后退。



也就是用startActivity()的方法启动另一个activity可以不用finish()方法，这样就是按返回键就能回退，这也是我们最惯用的方法，就是为了能回到原来的界面



### startActivityForResult()

这个启动方法就是算当前activity去启动另一个activity，想从目的activity得到返回值







## 涉及其他设计文件

各个activity下面的AndroidManifest.xml文件中的

```xml
            <intent-filter>
 				.....
            </intent-filter>
```

代码段



intent-filter：  意图过滤器，过滤除了在列表里面的，其他的就不接收







