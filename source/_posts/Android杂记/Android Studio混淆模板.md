---
title: Android Studio混淆模板
date: 2022-11-04 18:58:06
tags: Android
categories: Android杂记
copyright: true
---



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
