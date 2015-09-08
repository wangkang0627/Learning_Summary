# DataBind入门篇（一）
----------------------
###这里主要是围绕android的新特性DataBind进行全方面的讲解，本教程大致分为两个篇章，分别为入门篇，中级篇，高级篇。入门篇主要是针对官方提供的文档进行讲解，中级篇针对一些实际场景进行讲解，而高级篇主要是针对DataBind进行封装，让使用起来更便捷，有翻译不对的地方也请大家指正。
-----
####配置：

+ 在根项目的build.gradle里面添加dataBinder的依赖，当然这里得保证你的studio的版本在1.3以上。

```java

 dependencies {
       classpath "com.android.tools.build:gradle:1.3.0-beta4"
       classpath "com.android.databinding:dataBinder:1.0-rc1"
   }

```

+ 主项目的build.gradle中增加插件。

```java

apply plugin: 'com.android.application'
apply plugin: 'com.android.databinding'

```

通过以上步骤完成配置，接下来在会在activity中进行使用。
####Activity中绑定数据源
```java

  @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserModel userModel = new UserModel();
        userModel.card_num="##########0016";
        userModel.name = "测试";
        userModel.date = new Date();
        //这个类是自动生成的，一般都是Activity开头加当前Activity的名字
        ActivityDatabindLayoutBinding databindLayoutBinding = DataBindingUtil.setContentView(this, R.layout.activity_databind_layout);
        //这个方法也是自动生成的，用来设置data标签里面变量的
        databindLayoutBinding.setUser(userModel);
        //上面的databindLayoutBinding.setUser(userModel) 可以替换成这个方法也可以用
       // databindLayoutBinding.setVariable(BR.user,userModel);
        setContentView(databindLayoutBinding.getRoot());
        databindLayoutBinding.executePendingBindings();
    }

```
通过上述的代码，可以通过不初始化View对数据进行绑定，大大的减少了以前的findViewById。通过databindLayoutBinding.executePendingBindings()对数据进行绑定，接下来在xml里面设置对应的属性：

```xml

<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable name="user"  type="com.bindlearn.model.UserModel" />
        <import type="org.apache.commons.lang3.time.DateFormatUtils" />
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical">
        <TextView
            android:textColor="@android:color/black"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text='@{@string/name+user.name}' />
        <TextView
            android:textColor="@android:color/black"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text='@{"card_num:"+user.card_num}' />
        <TextView
            android:textColor="@android:color/black"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text='@{"birthday:"+DateFormatUtils.format(user.date,"yyyy-MM-dd HH:mm:ss")}' />
    </LinearLayout>
</layout>


```

<font color="red">data标签</font>里面的<font color="red">variable标签</font>是成员变量，通过在activity里面databindLayoutBinding.setUser(userModel)绑定数据，也可以替换成这个方法databindLayoutBinding.setVariable(BR.user,userModel)。BR.user跟标签的name=user对应.

<font color="red"> ```<import type="org.apache.commons.lang3.time.DateFormatUtils" />``` </font> 这个主要是导入一些类，使用里面的静态方法，上述方法通过调用静态方法对时间格式进行转换。

<font color="red"> ```android:text='@{@string/name+user.name}'``` </font> 在这里我可以通过@string去引用资源包里面的类容，其实大致使用方式都是差不懂的，只是换了种语法而已。


###使用时遇到的bug
<font color="red">
1. 不要在xml里面用中文，写中文字，否则一定会报错的。
2. 注意单引号和双引号的使用
</font>