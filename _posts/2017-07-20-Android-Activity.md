---
layout: post
title: "安卓中的活动"
excerpt: "活动详细"
date: 2017-07-20 17:42
tag: [Android]
---
# Activity

## 活动是什么

活动（Activity）是最容易吸引用户的地方，它是一种可以包含用户界面的组件，主要用于和用户进行交互。一个应用程序中可以包含零个或多个活动，但不包含任何活动的应用程序很少见，谁也不想让自己的应用永远无法被用户看到吧？

项目中的任何活动都应该重写Activity的onCreate()方法，而目前我们的FirstActivity中已经重写了这个方法，这是由Android Studio自动帮我们完成的，代码如下所示：
```java
public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

}
```
可以看到，onCreate()方法非常简单，就是调用了父类的onCreate()方法。当然这只是默认的实现，后面我们还需要在里面加入很多自己的逻辑。

在创建布局文件时选择了LinearLayout作为根元素，因此现在布局文件中已经有一个LinearLayout元素了。那我们现在对这个布局稍做编辑，添加一个按钮，如下所示：
```html
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button_1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button 1"
        />

</LinearLayout>
```

这里添加了一个Button元素，并在Button元素的内部增加了几个属性。android:id是给当前的元素定义一个唯一标识符，之后可以在代码中对这个元素进行操作。你可能会对@+id/ button_1这种语法感到陌生，但如果把加号去掉，变成@id/button_1，这样你就会觉得有些熟悉了吧，这不就是在XML中引用资源的语法吗？只不过是把string替换成了id。是的，如果你需要在XML中引用一个id，就使用@id/id_name这种语法，而如果你需要在XML中定义一个id，则要使用@+id/id_name这种语法。随后android:layout_width指定了当前元素的宽度，这里使用match_parent表示让当前元素和父元素一样宽。android:layout_height指定了当前元素的高度，这里使用wrap_content表示当前元素的高度只要能刚好包含里面的内容就行。android:text指定了元素中显示的文字内容。

重新回到FirstActivity，在onCreate()方法中加入如下代码：
```java
public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.first_layout);
    }

}
```
可以看到，这里调用了setContentView()方法来给当前的活动加载一个布局，而在setContentView()方法中，我们一般都会传入一个布局文件的id。在第1章介绍项目资源的时候我曾提到过，项目中添加的任何资源都会在R文件中生成一个相应的资源id，因此我们刚才创建的first_layout.xml布局的id现在应该是已经添加到R文件中了。在代码中去引用布局文件的方法你也已经学过了，只需要调用R.layout.first_layout就可以得到first_layout.xml布局的id，然后将这个值传入setContentView()方法即可。

## 在AndroidManifest文件中注册

所有的活动都要在AndroidManifest.xml中进行注册才能生效，而实际上FirstActivity已经在AndroidManifest.xml中注册过了，我们打开app/src/main/AndroidManifest.xml文件瞧一瞧，代码如下所示：
```html
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.activitytest">
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FirstActivity"></activity>
    </application>
</manifest>
```
可以看到，活动的注册声明要放在<application>标签内，这里是通过<activity>标签来对活动进行注册的。那么又是谁帮我们自动完成了对FirstActivity的注册呢？当然是Android Studio了，之前在使用Eclipse创建活动或其他系统组件时，很多人都会忘记要去Android Manifest.xml中注册一下，从而导致程序运行崩溃，很显然Android Studio在这方面做得更加人性化。

在<activity>标签中我们使用了android:name来指定具体注册哪一个活动，那么这里填入的.FirstActivity是什么意思呢？其实这不过就是com.example.activitytest.FirstActivity的缩写而已。由于在最外层的<manifest>标签中已经通过package属性指定了程序的包名是com.example.activitytest，因此在注册活动时这一部分就可以省略了，直接使用.FirstActivity就足够了。

不过，仅仅是这样注册了活动，我们的程序仍然是不能运行的，因为还没有为程序配置主活动，也就是说，当程序运行起来的时候，不知道要首先启动哪个活动。配置主活动的方法其实在第1章中已经介绍过了，就是在<activity>标签的内部加入<intent-filter>标签，并在这个标签里添加<action android:name="android.intent.action.MAIN"/>和<category android: name="android.intent.category.LAUNCHER" />这两句声明即可。

除此之外，我们还可以使用android:label指定活动中标题栏的内容，标题栏是显示在活动最顶部的，待会儿运行的时候你就会看到。需要注意的是，给主活动指定的label不仅会成为标题栏中的内容，还会成为启动器（Launcher）中应用程序显示的名称。

修改后的AndroidManifest.xml文件，代码如下所示：
```html
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.activitytest">
    <application
        ... >
        <activity android:name=".FirstActivity"
            android:label="This is FirstActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```
这样的话，FirstActivity就成为我们这个程序的主活动了，即点击桌面应用程序图标时首先打开的就是这个活动。另外需要注意，如果你的应用程序中没有声明任何一个活动作为主活动，这个程序仍然是可以正常安装的，只是你无法在启动器中看到或者打开这个程序。这种程序一般都是作为第三方服务供其他应用在内部进行调用的，如支付宝快捷支付服务。

## 在活动中使用Toast

Toast是Android系统提供的一种非常好的提醒方式，在程序中可以使用它将一些短小的信息通知给用户，这些信息会在一段时间后自动消失，并且不会占用任何屏幕空间，我们现在就尝试一下如何在活动中使用Toast。

首先需要定义一个弹出Toast的触发点，正好界面上有个按钮，那我们就让点击这个按钮的时候弹出一个Toast吧。在onCreate()方法中添加如下代码：
```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.first_layout);
    Button button1 = (Button) findViewById(R.id.button_1);
    button1.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            Toast.makeText(FirstActivity.this, "You clicked Button 1",
                Toast.LENGTH_SHORT).show();
        }
    });
}
```
在活动中，可以通过findViewById()方法获取到在布局文件中定义的元素，这里我们传入R.id.button_1，来得到按钮的实例，这个值是刚才在first_layout.xml中通过android:id属性指定的。findViewById()方法返回的是一个View对象，我们需要向下转型将它转成Button对象。得到按钮的实例之后，我们通过调用setOnClickListener()方法为按钮注册一个监听器，点击按钮时就会执行监听器中的onClick()方法。因此，弹出Toast的功能当然是要在onClick()方法中编写了。

Toast的用法非常简单，通过静态方法makeText()创建出一个Toast对象，然后调用show()将Toast显示出来就可以了。这里需要注意的是，makeText()方法需要传入3个参数。第一个参数是Context，也就是Toast要求的上下文，由于活动本身就是一个Context对象，因此这里直接传入FirstActivity.this即可。第二个参数是Toast显示的文本内容，第三个参数是Toast显示的时长，有两个内置常量可以选择Toast.LENGTH_SHORT和Toast.LENGTH_LONG。

## 在活动中使用Menu

手机毕竟和电脑不同，它的屏幕空间非常有限，因此充分地利用屏幕空间在手机界面设计中就显得非常重要了。如果你的活动中有大量的菜单需要显示，这个时候界面设计就会比较尴尬，因为仅这些菜单就可能占用屏幕将近三分之一的空间，这该怎么办呢？不用担心，Android给我们提供了一种方式，可以让菜单都能得到展示的同时，还能不占用任何屏幕空间。

main.xml中添加如下代码：
```html
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"
        android:title="Add"/>
    <item
        android:id="@+id/remove_item"
        android:title="Remove"/>
</menu>
```
这里我们创建了两个菜单项，其中<item>标签就是用来创建具体的某一个菜单项，然后通过android:id给这个菜单项指定一个唯一的标识符，通过android:title给这个菜单项指定一个名称。

重写onCreateOptionsMenu()方法

然后在onCreateOptionsMenu()方法中编写如下代码：
```java
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main, menu);
    return true;
}
```
通过getMenuInflater()方法能够得到MenuInflater对象，再调用它的inflate()方法就可以给当前活动创建菜单了。inflate()方法接收两个参数，第一个参数用于指定我们通过哪一个资源文件来创建菜单，这里当然传入R.menu.main。第二个参数用于指定我们的菜单项将添加到哪一个Menu对象当中，这里直接使用onCreateOptionsMenu()方法中传入的menu参数。然后给这个方法返回true，表示允许创建的菜单显示出来，如果返回了false，创建的菜单将无法显示。

当然，仅仅让菜单显示出来是不够的，我们定义菜单不仅是为了看的，关键是要菜单真正可用才行，因此还要再定义菜单响应事件。在FirstActivity中重写onOptionsItemSelected()方法：
```java
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
    case R.id.add_item:
        Toast.makeText(this, "You clicked Add", Toast.LENGTH_SHORT).show();
        break;
    case R.id.remove_item:
        Toast.makeText(this, "You clicked Remove", Toast.LENGTH_SHORT).show();
        break;
    default:
    }
    return true;
}
```
在onOptionsItemSelected()方法中，通过调用item.getItemId()来判断我们点击的是哪一个菜单项，然后给每个菜单项加入自己的逻辑处理，这里我们就活学活用，弹出一个刚刚学会的Toast。

## 销毁一个活动

或许你现在心中会有个疑惑，如何销毁一个活动呢？

其实答案非常简单，只要按一下Back键就可以销毁当前的活动了。不过如果你不想通过按键的方式，而是希望在程序中通过代码来销毁活动，当然也可以，Activity类提供了一个finish()方法，我们在活动中调用一下这个方法就可以销毁当前活动了。

修改按钮监听器中的代码，如下所示：
```java
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        finish();
    }
});
```
重新运行程序，这时点击一下按钮，当前的活动就被成功销毁了，效果和按下Back键是一样的。

## 使用Intent在活动之间穿梭

只有一个活动的应用也太简单了吧？没错，你的追求应该更高一点。不管你想创建多少个活动，方法都和上一节中介绍的是一样的。唯一的问题在于，你在启动器中点击应用的图标只会进入到该应用的主活动，那么怎样才能由主活动跳转到其他活动呢？我们现在就来一起看一看。

使用显式Intent

创建SecondActivity

点击Finish完成创建，Android Studio会为我们自动生成SecondActivity.java和second_layout.xml这两个文件。不过自动生成的布局代码目前对你来说可能有些复杂，这里我们仍然还是使用最熟悉的LinearLayout，编辑second_layout.xml，将里面的代码替换成如下内容：
```html
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button_2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button 2"
        />

</LinearLayout>
```
我们还是定义了一个按钮，按钮上显示Button 2。

然后SecondActivity中的代码已经自动生成了一部分，我们保持默认不变就好，如下所示：
```java
public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.second_layout);
    }

}
```
另外不要忘记，任何一个活动都是需要在AndroidManifest.xml中注册的，不过幸运的是，Android Studio已经帮我们自动完成了，你可以打开AndroidManifest.xml瞧一瞧：
```html
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity
        android:name=".FirstActivity"
        android:label="This is FirstActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
    <activity android:name=".SecondActivity"></activity>
</application>
```
由于SecondActivity不是主活动，因此不需要配置<intent-filter>标签里的内容，注册活动的代码也简单了许多。现在第二个活动已经创建完成，剩下的问题就是如何去启动这第二个活动了，这里我们需要引入一个新的概念：Intent。

Intent是Android程序中各组件之间进行交互的一种重要方式，它不仅可以指明当前组件想要执行的动作，还可以在不同组件之间传递数据。Intent一般可被用于启动活动、启动服务以及发送广播等场景，由于服务、广播等概念你暂时还未涉及，那么本章我们的目光无疑就锁定在了启动活动上面。

Intent大致可以分为两种：显式Intent和隐式Intent，我们先来看一下显式Intent如何使用。

Intent有多个构造函数的重载，其中一个是Intent(Context packageContext, Class<?> cls)。这个构造函数接收两个参数，第一个参数Context要求提供一个启动活动的上下文，第二个参数Class则是指定想要启动的目标活动，通过这个构造函数就可以构建出Intent的“意图”。然后我们应该怎么使用这个Intent呢？Activity类中提供了一个startActivity()方法，这个方法是专门用于启动活动的，它接收一个Intent参数，这里我们将构建好的Intent传入startActivity()方法就可以启动目标活动了。

修改FirstActivity中按钮的点击事件，代码如下所示：
```java
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
        startActivity(intent);
    }
});
```
我们首先构建出了一个Intent，传入FirstActivity.this作为上下文，传入SecondActivity.class作为目标活动，这样我们的“意图”就非常明显了，即在FirstActivity这个活动的基础上打开SecondActivity这个活动。然后通过startActivity()方法来执行这个Intent。

## 使用隐式Intent

相比于显式Intent，隐式Intent则含蓄了许多，它并不明确指出我们想要启动哪一个活动，而是指定了一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent，并帮我们找出合适的活动去启动。

什么叫作合适的活动呢？简单来说就是可以响应我们这个隐式Intent的活动，那么目前SecondActivity可以响应什么样的隐式Intent呢？额，现在好像还什么都响应不了，不过很快就会有了。

通过在<activity>标签下配置<intent-filter>的内容，可以指定当前活动能够响应的action和category，打开AndroidManifest.xml，添加如下代码：
```html
<activity android:name=".SecondActivity" >
    <intent-filter>
        <action android:name="com.example.activitytest.ACTION_START" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```
在<action>标签中我们指明了当前活动可以响应com.example.activitytest.ACTION_START这个action，而<category>标签则包含了一些附加信息，更精确地指明了当前的活动能够响应的Intent中还可能带有的category。只有<action>和<category>中的内容同时能够匹配上Intent中指定的action和category时，这个活动才能响应该Intent。

修改FirstActivity中按钮的点击事件，代码如下所示：
```java
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent("com.example.activitytest.ACTION_START");
        startActivity(intent);
    }
});
```
可以看到，我们使用了Intent的另一个构造函数，直接将action的字符串传了进去，表明我们想要启动能够响应com.example.activitytest.ACTION_START这个action的活动。那前面不是说要<action>和<category>同时匹配上才能响应的吗？怎么没看到哪里有指定category呢？这是因为android.intent.category.DEFAULT是一种默认的category，在调用startActivity()方法的时候会自动将这个category添加到Intent中。

重新运行程序，在FirstActivity的界面点击一下按钮，你同样成功启动SecondActivity了。不同的是，这次你是使用了隐式Intent的方式来启动的，说明我们在<activity>标签下配置的action和category的内容已经生效了！