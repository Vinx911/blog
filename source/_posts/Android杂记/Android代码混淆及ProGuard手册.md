---
title: Android代码混淆及ProGuard手册
date: 2022-11-04 18:58:06
id: 39
tags: Android
categories: Android杂记
copyright: true
---



为什么把这三个内容放到一起说？因为在 Android Gradle 中配置方法基本是在一起的。

官方说明如下：

> 为了尽可能减小应用的大小，您应在发布 build 中启用缩减功能来移除不使用的代码和资源。启用缩减功能后，您还会受益于两项功能，一项是混淆处理功能，该功能会缩短应用的类和成员的名称；另一项是优化功能，该功能会采用更积极的策略来进一步减小应用的大小。本页介绍 R8 如何为项目执行这些编译时任务，以及您如何对这些任务进行自定义。
>
> 当您使用 [Android Gradle 插件 3.4.0](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Freleases%2Fgradle-plugin%233-4-0) 或更高版本构建项目时，该插件不再使用 ProGuard 执行编译时代码优化，而是与 R8 编译器协同工作，处理以下编译时任务：
>
> * **代码缩减（即摇树优化）**：从应用及其库依赖项中检测并安全地移除不使用的类、字段、方法和属性（这使其成为了一个对于规避 [64k 引用限制](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Fbuild%2Fmultidex)非常有用的工具）。例如，如果您仅使用某个库依赖项的少数几个 API，那么缩减功能可以识别应用不使用的库代码并仅从应用中移除这部分代码。如需了解详情，请转到介绍如何[缩减代码](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Fbuild%2Fshrink-code%23shrink-code)的部分。
> * **资源缩减**：从封装应用中移除不使用的资源，包括应用库依赖项中不使用的资源。此功能可与代码缩减功能结合使用，这样一来，移除不使用的代码后，也可以安全地移除不再引用的所有资源。如需了解详情，请转到介绍如何[缩减资源](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Fbuild%2Fshrink-code%23shrink-resources)的部分。
> * **混淆**：缩短类和成员的名称，从而减小 DEX 文件的大小。如需了解详情，请转到介绍如何[对代码进行混淆处理](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Fbuild%2Fshrink-code%23obfuscate)的部分。
> * **优化**：检查并重写代码，以进一步减小应用的 DEX 文件的大小。例如，如果 R8 检测到从未采用过给定 if/else 语句的 `else {}` 分支，则会移除 `else {}` 分支的代码。如需了解详情，请转到介绍[代码优化](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Fbuild%2Fshrink-code%23optimization)的部分。
>
> 默认情况下，在构建应用的发布版本时，R8 会自动执行上述编译时任务。不过，您也可以停用某些任务或通过 ProGuard 规则文件自定义 R8 的行为。事实上，**R8 支持所有现有 ProGuard 规则文件**，因此您在更新 Android Gradle 插件以使用 R8 时，无需更改现有规则。

## 开启混淆功能

上面没有提到的一个代码混淆的重要作用：我们知道 apk 文件是相对容易被反编译的，未加混淆的 apk，反编译后基本裸奔。而混淆的 apk 即使被反编译，类名与变量名都会处理成无意义的字符，很大程度上降低了源码的可读性。

以下是在 Android Studio 中开启混淆的方法和常用的配置。Android Studio 的 Gradle 混淆功能默认是关闭的，我们需要手动在 module 的 build.gradle 中打开。

1. 在 Module 的 build.gradle 中添加如下配置：

```java
android{
    ...
    buildTypes {

        release {
            // 开启混淆
            minifyEnabled true
            // 开启资源压缩，编译时会自动删除未使用到的res资源
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }

        debug {
            //开启混淆会导致编译速度变慢，debug通常不开启
            minifyEnabled false
            shrinkResources false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
复制代码
```

之后我们需要在 proguard-rules.pro 文件中编辑混淆规则。

## proguard 手册

### 输入/输出配置项

* `@filename`

  等同于：-include filename，filename 文件名。
* `-include [filename]`

  从给定文件中读取配置项，filename 文件名。
* `-basedirectory [directoryname]`

  为后续的相对文件指定基础目录，directoryname 目录名。
* `injars [classPath]` 指定要处理的输入 jar（等压缩包），包中的 class 文件会被处理后输出到 outjars 的指定路径下，非 class 文件直接输出到 outjars 指定路径，classpath jar 包输入路径。
* `outjars [classPath]` 指定输出路径，接收 injars 的文件输出，classPath 输出路径。
* `libraryjars [classPath]` 指定不被混淆的 jar，classPath 文件路径。

### 压缩配置项

* `-dontshrink`

  不进行代码压缩，默认会进行代码压缩，打开此配置项则不进行压缩。
* `-printusage [fileName]`

  将被压缩的代码输出到指定路径。

### 优化配置项

* `-dontoptimize`

  指定不进行代码优化，默认情况下 ProGuard 会优化所有代码。
* `-optimizations [optimization_filter]`

  在更细粒度的级别指定要启用和禁用的优化，篇幅有限不展开讲了，具体可以参考：[Configuration - Optimizations](https://link.juejin.cn/?target=https%3A%2F%2Fwww.guardsquare.com%2Fmanual%2Fconfiguration%2Foptimizations)

  常用的配置方式：`-optimizations !code/simplification/cast,!field/*,!class/merging/*`
* `-optimizationpasses n`

  指定代码迭代优化的次数，默认执行一遍。

### 混淆配置

* `-dontobfuscate`

  不进行混淆，默认会进行代码混淆。
* `-printmapping [filename]`

  将混淆前后映射表输出到指定文件。
* `-dontusemixedcaseclassnames`

  混淆时不使用大小写混合，混淆后类名为小写。
* `-keepattributes [attribute_filter]`

  指定要保留的属性，如:`-keepattributes Exceptions,InnerClasses`,具体可参考：[Optional attributes](https://link.juejin.cn/?target=https%3A%2F%2Fwww.guardsquare.com%2Fmanual%2Fconfiguration%2Fattributes)

### 通用配置项

* `-verbose`

  指定在处理期间输出更多信息。如果程序因异常终止，此选项将打印出整个堆栈跟踪，而不仅仅是异常消息。
* `-dontnote [class_filter]`

  指定不打印匹配类的相关异常信息，class_filter 为正则表达式，符合表达式的类不打印信息。
* `-dontwarn [class_filter]`

  指定相关类不发出警告，class_filter 为正则表达式。如：`-dontwarn androidx.**`
* `-dump [filename]`

  将所有 class 的内部结构输出到指定文件。如：`-dump proguard/class_files.txt`

### keep 配置项

* `-keep []`

  指定要保留的类和类成员。

  ```groovy
  //View子类，类名不混淆
  - keep public class * extends android.view.View{
   		//set开头的方法不混淆
      void set*(***);
    	//构造函数不混淆
      public <init>(android.content.Context);
      public <init>(android.content.Context, android.util.AttributeSet);
      public <init>(android.content.Context, android.util.AttributeSet, int);
  }
  复制代码
  ```

  指定的类名和类成员不被混淆，需要注意的是以下配置只能保证类名不被混淆。

  ```groovy
  -keep public class * extends android.app.Activity
  复制代码
  ```

  想要类名和类成员全部不混淆应该使用：

  ```groovy
  -keep public class * extends android.app.Activity{*;}
  复制代码
  ```
* `-keepclassmembers`

  保留指定的类成员。

  ```groovy
  //指定不混淆的类成员，类名会混淆
  -keepclassmembers class * {
      //事件监听类（参数符合**On*Event）的方法不混淆
      void *(**On*Event);
  }
  复制代码
  ```
* `-keepclasseswithmembers`

  在所有类成员都存在的情况下，指定要保留的类和类成员。

  ```groovy
  //需要匹配类和所有指定成员都存在的类，类和类成员不混淆
  -keepclasseswithmembers class * {
      public <init>(android.content.Context, android.util.AttributeSet);
      public <init>(android.content.Context, android.util.AttributeSet, int);
  }
  复制代码
  ```

  功能与 `-keep` 很像，举个例子：

  ```java
  public class MyClass extends android.view.View{
      public MyClass(Context context) {
          super(context);
      }
  
      public MyClass(Context context, @Nullable AttributeSet attrs) {
          super(context, attrs);
      }
  
      public MyClass(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
          super(context, attrs, defStyleAttr);
      }
  
      /**
       * 自定义方法
       * @param onClickListener
       */
      public void addListener(OnClickListener onClickListener){
          //TODO: todo
      }
  }
  复制代码
  ```

  当使用 `-keep` 时：

  ```groovy
  - keep public class * extends android.view.View{
    	//构造函数不混淆
      public <init>(android.content.Context);
      public <init>(android.content.Context, android.util.AttributeSet);
      public <init>(android.content.Context, android.util.AttributeSet, int);
  
    	public void addListener(***)
    	public void getListener(***)
  }
  复制代码
  ```

  MyClass 类名和指定的类成员不会被混淆（未指定的类成员也会被混淆），因为 MyClass 符合 ` class * extends android.view.View` 条件。

  而使用 `-keepclasseswithmembers` 时：

  ```groovy
  - keep public class * extends android.view.View{
    	//构造函数不混淆
      public <init>(android.content.Context);
      public <init>(android.content.Context, android.util.AttributeSet);
      public <init>(android.content.Context, android.util.AttributeSet, int);
  
    	public void addListener(***)
    	public void getListener(***)
  }
  复制代码
  ```

  MyClass 会被混淆，因为 MyClass 不符合 `public void getListener(***)` 条件。当 `keepclasseswithmembers` 指定的类和类成员全部存在的情况下，才会匹配该类（MyClass 不存在 `getListener()` 方法，所以不能被匹配）。
* `-keepnames/-keepclassmembernames和-keepclasseswithmembernames`

  |选项|等同于|作用|
  | ------------------------------| ----------------------------------------| ---------------------------------------------------------------------------------------------|
  |-keepnames **|-keep,allowshrinking **|指定要保留的类和类成员,允许代码压缩优化，成员可能在压缩阶段被删除|
  |-keepclassmembernames **|- keepclassmembers,allowshrinking **|指定要保留的类成员，允许代码压缩优化，成员可能在压缩阶段被删除|
  |-keepclasseswithmembernames **|-keepclasseswithmember,allowshrinking **|在所有类成员都存在的情况下，指定要保留的类和类成员,允许代码压缩优化，成员可能在压缩阶段被删除|

  简单来说使用 `-keep/-keepclassmember和-keepclasseswithmember` 可能会阻止成员被压缩优化，而使用 `-keepnames/-keepclassmembernames和-keepclasseswithmembernames` 而不会。

#### Keep 配置项修饰符

可用用来修饰 `-keep/-keepclassmember和-keepclasseswithmember`，使用方式：

```groovy
-keep[,modifiter,...] class_specification
-keep,allowshrinking public class * extends android.view.View{*;}
复制代码
```

* `allowshrinking`

  上面提到过了，允许对象进行代码压缩，对象可能在压缩阶段被删除。
* `allowoptimization`

  指定的对象可能会被改变（优化步骤），但可能不会被混淆或者删除。
* `allowobfuscation`

  指定对象可能会被重命名，但是不会被删除和优化。
* `includedescriptorclasses`
* `includecode`

  这两个自行了解吧，太不常用了。（其实我也没搞懂。。。）

### 通配符

|符号|意义|人话|
| ----| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| --------------------------------------------------------------------------------------|
|`?`|matches any single character in a class name, but not the package separator. For example, "`com.example.Test?`" matches "`com.example.Test1`" and "`com.example.Test2`", but not "`com.example.Test12`".|匹配名称中的任意单个字符。|
|`*`|matches any part of a class name not containing the package separator. For example, "`com.example.*Test*`" matches "`com.example.Test`" and "`com.example.YourTestApplication`", but not "`com.example.mysubpackage.MyTest`". Or, more generally, "`com.example.*`" matches all classes in "`com.example`", but not in its subpackages.|匹配名称中任意多个字符，不包含包分隔符和目录分隔符，用在类体中可以匹配任意字段和方法。|
|`**`|matches any part of a class name, possibly containing any number of package separators. For example, "`**.Test`" matches all `Test` classes in all packages except the root package. Or, "`com.example.**`" matches all classes in "`com.example`" and in its subpackages.|匹配名称中任何部分，可以包含分隔符。|
|`<n>`|matches the *n*'th matched wildcard in the same option. For example, "`com.example.*Foo<1>`" matches "`com.example.BarFooBar`".|在相同选项中匹配第 n 个匹配的通配符。|

篇幅有限只列举了部分常用的配置项，详细的说明可以查询：[ProGuard 手册](https://link.juejin.cn/?target=https%3A%2F%2Fwww.guardsquare.com%2Fen%2Fproducts%2Fproguard%2Fmanual%2Fusage)。

## proguard 常用配置规则

常用文件配置如下：

### 1.常用配置

```groovy
-optimizationpasses 5
# 混淆时不使用大小写混合，混淆后的类名为小写
-dontusemixedcaseclassnames
# 指定不去忽略非公共库的类
-dontskipnonpubliclibraryclasses
# 指定不去忽略非公共库的成员
-dontskipnonpubliclibraryclassmembers
# 混淆时不做预校验
-dontpreverify
# 混淆时不记录日志
-verbose
# 代码优化
-dontshrink
# 不优化输入的类文件
-dontoptimize
# 保留注解不混淆
-keepattributes *Annotation*,InnerClasses
# 避免混淆泛型
-keepattributes Signature
# 保留代码行号，方便异常信息的追踪
-keepattributes SourceFile,LineNumberTable
# 混淆采用的算法
-optimizations !code/simplification/cast,!field/*,!class/merging/*

# dump.txt文件列出apk包内所有class的内部结构
-dump proguard/class_files.txt
# seeds.txt文件列出未混淆的类和成员
-printseeds proguard/seeds.txt
# usage.txt文件列出从apk中删除的代码
-printusage proguard/unused.txt
# mapping.txt文件列出混淆前后的映射
-printmapping proguard/mapping.txt
复制代码
```

### 2. Android 系统类

#### android 类

```groovy
# Android类
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class * extends android.view.View
复制代码
```

#### support 包

```groovy
# support
-keep class android.support.** {*;}
-keep public class * extends android.support.**
-dontwarn android.support.**
-keep interface android.support.** { *; }
复制代码
```

#### androidx 包

```groovy
# androidx
-keep class androidx.** {*;}
-keep interface androidx.** {*;}
-keep public class * extends androidx.**
-dontwarn androidx.**
复制代码
```

#### 自定义控件 get/set 和构造

```groovy
# 自定义控件
-keep public class * extends android.view.View{
    *** get*();
    void set*(***);
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
复制代码
```

#### R 文件

```groovy
# R文件
-keep class **.R$* {
 *;
}
复制代码
```

#### webview

```groovy
# webview
-keepclassmembers class android.webkit.WebView {
   public *;
}
-keepclassmembers class * extends android.webkit.WebViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String);
}
-keepclassmembers class * extends android.webkit.WebViewClient {
    public void *(android.webkit.WebView, *);
}
复制代码
```

#### android 事件方法

```groovy
# 按键等事件
-keepclassmembers class * {
    void *(**On*Event);
}
# onClick
-keepclassmembers class * extends android.app.Activity{
    public void *(android.view.View);
}
复制代码
```

#### 枚举类型

```groovy
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}
复制代码
```

#### 其他类

```groovy
# native方法
-keepclasseswithmembernames class * {
    native <methods>;
}
# View构造方法
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
# Parcelable
-keep class * implements android.os.Parcelable {
  public static final android.os.Parcelable$Creator *;
}
# Serializable
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}
复制代码
```

### 3. 第三库

#### ButterKnife

```groovy
-keep class butterknife.** { *; }
-dontwarn butterknife.internal.**
-keep class **$$ViewBinder { *; }
-keepclasseswithmembernames class * {
    @butterknife.* <fields>;
}
-keepclasseswithmembernames class * {
    @butterknife.* <methods>;
}
复制代码
```

#### OkHttp

```groovy
-dontwarn com.squareup.okhttp3.**
-keep class com.squareup.okhttp3.** { *;}
-dontwarn okio.**
复制代码
```

#### glide 3

```groovy
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** {
  **[] $VALUES;
  public *;
}
复制代码
```

#### glide 4

```groovy
-keep public class * implements com.bumptech.glide.module.AppGlideModule
-keep public class * implements com.bumptech.glide.module.LibraryGlideModule
-keep public enum com.bumptech.glide.load.ImageHeaderParser$** {
  **[] $VALUES;
  public *;
}
复制代码
```

#### gson

```groovy
-keep class com.google.gson.** {*;}
-keep class com.google.**{*;}
-keep class sun.misc.Unsafe { *; }
-keep class com.google.gson.stream.** { *; }
-keep class com.google.gson.examples.android.model.** { *; }
复制代码
```

#### greendao

```groovy
-keepclassmembers class * extends org.greenrobot.greendao.AbstractDao {
public static java.lang.String TABLENAME;
}
-keep class **$Properties
# If you do not use SQLCipher:
-dontwarn org.greenrobot.greendao.database.**
# If you do not use Rx:
-dontwarn rx.**
复制代码
```

#### Retrofit2

```groovy
-dontwarn retrofit2.**
-keep class retrofit2.** { *; }
-keepattributes Signature
-keepattributes Exceptions
复制代码
```

#### RxJava、RxAndroid

```groovy
-dontwarn sun.misc.**
-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
   long producerIndex;
   long consumerIndex;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode producerNode;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode consumerNode;
}
复制代码
```

#### Picasso

```groovy
-keep class com.parse.*{ *; }
-dontwarn com.parse.**
-dontwarn com.squareup.picasso.**
-keepclasseswithmembernames class * {
    native <methods>;
}
复制代码
```

#### fastJson

```groovy
-dontwarn com.alibaba.fastjson.**
-keep class com.alibaba.fastjson.**{*; }
复制代码
```

#### EventBus

```groovy
# EventBus2
-keepclassmembers class ** {
    public void onEvent*(***);
}

# Only required if you use AsyncExecutor
-keepclassmembers class * extends de.greenrobot.event.util.ThrowableFailureEvent {
    <init>(java.lang.Throwable);
}
#EventBus3
-keepattributes *Annotation*
-keepclassmembers class ** {
    @org.greenrobot.eventbus.Subscribe <methods>;
}
-keep enum org.greenrobot.eventbus.ThreadMode { *; }

# Only required if you use AsyncExecutor
-keepclassmembers class * extends org.greenrobot.eventbus.util.ThrowableFailureEvent {
    <init>(java.lang.Throwable);
}
复制代码
```

#### 阿里云推送

```groovy
-keepclasseswithmembernames class ** {
    native <methods>;
}
-keepattributes Signature
-keep class sun.misc.Unsafe { *; }
-keep class com.taobao.** {*;}
-keep class com.alibaba.** {*;}
-keep class com.alipay.** {*;}
-keep class com.ut.** {*;}
-keep class com.ta.** {*;}
-keep class anet.**{*;}
-keep class anetwork.**{*;}
-keep class org.android.spdy.**{*;}
-keep class org.android.agoo.**{*;}
-keep class android.os.**{*;}
-keep class org.json.**{*;}
-dontwarn com.taobao.**
-dontwarn com.alibaba.**
-dontwarn com.alipay.**
-dontwarn anet.**
-dontwarn org.android.spdy.**
-dontwarn org.android.agoo.**
-dontwarn anetwork.**
-dontwarn com.ut.*
-dontwarn com.ta.**
复制代码
```

以上只是列举了一些 Android 系统和第三方库常用的混淆配置方式，项目中需要按照实际需求进行配置。

‍

## proguard 模版

* Android Studio混淆模板[^1]

‍

[^1]: # Android Studio混淆模板

    ```properties
    #############################################
    #
    # 对于一些基本指令的添加
    #
    #############################################
    
    # 关闭压缩
    #-dontshrink 
    
    # 关闭优化
    #-dontoptimize  
    # 代码混淆压缩比，在0~7之间，默认为5，一般不做修改
    -optimizationpasses 5
    
    # 关闭混淆
    #-dontobfuscate 
     
    # 混合时不使用大小写混合，混合后的类名为小写
    -dontusemixedcaseclassnames
     
    # 指定不去忽略非公共库的类
    -dontskipnonpubliclibraryclasses
     
    # 这句话能够使我们的项目混淆后产生映射文件
    # 包含有类名->混淆后类名的映射关系
    -verbose
     
    # 指定不去忽略非公共库的类成员
    -dontskipnonpubliclibraryclassmembers
     
    # 不做预校验，preverify是proguard的四个步骤之一，Android不需要preverify，去掉这一步能够加快混淆速度。
    -dontpreverify
     
    # 保留Annotation不混淆
    -keepattributes *Annotation*,InnerClasses
     
    # 避免混淆泛型
    -keepattributes Signature
     
    # 抛出异常时保留代码行号
    -keepattributes SourceFile,LineNumberTable
     
    # 指定混淆是采用的算法，后面的参数是一个过滤器
    # 这个过滤器是谷歌推荐的算法，一般不做更改
    -optimizations !code/simplification/cast,!field/*,!class/merging/*
     
    #############################################
    #
    # Android开发中一些需要保留的公共部分
    #
    #############################################
    
    # 保留我们使用的四大组件，自定义的Application等等这些类不被混淆
    # 因为这些子类都有可能被外部调用
    -keep public class * extends android.app.Activity
    -keep public class * extends android.app.Fragment
    -keep public class * extends android.app.Application
    -keep public class * extends android.app.Service
    -keep public class * extends android.content.BroadcastReceiver
    -keep public class * extends android.content.ContentProvider
    -keep public class * extends android.app.backup.BackupAgentHelper
    -keep public class * extends android.preference.Preference
    -keep public class * extends androidx.fragment.app.Fragment
    # 这个应该是Google play相关的
    -keep public class com.android.vending.licensing.ILicensingService
    
    # support
    -keep class android.support.** {*;}
    -keep public class * extends android.support.**
    -dontwarn android.support.**
    -keep interface android.support.** { *; }
    
    # androidx
    -keep class androidx.** {*;}
    -keep interface androidx.** {*;}
    -keep public class * extends androidx.**
    -dontwarn androidx.**
    
    # 自定义控件
    -keep public class * extends android.view.View{
        *** get*();
        void set*(***);
        public <init>(android.content.Context);
        public <init>(android.content.Context, android.util.AttributeSet);
        public <init>(android.content.Context, android.util.AttributeSet, int);
    }


    # 保留R下面的资源
    -keep class **.R$* {*;}
    
    # 这个主要是在layout 中写的onclick方法android:onclick="onClick"，不进行混淆
    -keepclassmembers class * extends android.app.Activity {
        public void *(android.view.View);
    }
    
    # 对于带有回调函数的onXXEvent、**On*Listener的，不能被混淆
    -keepclassmembers class * {
        void *(**On*Event);
        void *(**On*Listener);
    }
    
    # 保持枚举enum类不被混淆
    -keepclassmembers enum * {
        public static **[] values();
        public static ** valueOf(java.lang.String);
    }


    # 保持 native 方法不被混淆
    -keepclasseswithmembernames class * {
        native <methods>;
    }
    
    # 保留Parcelable序列化类不被混淆
    -keep class * implements android.os.Parcelable {
        public static final android.os.Parcelable$Creator *;
    }
    
    # 保持注解继承类不混淆
    -keep class * extends java.lang.annotation.Annotation {*;}
    
    # 保持Serializable实现类不被混淆
    -keepnames class * implements java.io.Serializable
    # 保持Serializable不被混淆并且enum 类也不被混淆
    -keepclassmembers class * implements java.io.Serializable {
        static final long serialVersionUID;
        private static final java.io.ObjectStreamField[] serialPersistentFields;
        !static !transient <fields>;
        !private <fields>;
        !private <methods>;
        private void writeObject(java.io.ObjectOutputStream);
        private void readObject(java.io.ObjectInputStream);
        java.lang.Object writeReplace();
        java.lang.Object readResolve();
    }
    
    # ViewBinding & DataBinding
    -keepclassmembers class * implements androidx.viewbinding.ViewBinding {
      public static * inflate(android.view.LayoutInflater);
      public static * inflate(android.view.LayoutInflater, android.view.ViewGroup, boolean);
      public static * bind(android.view.View);
    }
    
    ```
