---
title: 了解java反射机制
categories:
  - 笔记
tags:
  - java
  - 反射机制
date: 2020-06-22 22:19:12
description:
---

# 介绍 
JAVA反射机制是在运行状态中，对于任何一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java的反射机制。
# 实例
### BookObject类
``` java 
package com.note;

public class BookObject {
    private String name;

    public BookObject(){
        name="狂人日记";
    }

    public void publicMethod(String au){
        System.out.println(au);
    }

    private void privateMethod(){
        System.out.println(name);
    }
}
```
### App类
``` java
package com.note;

import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class App 
{
    public static void main( String[] args )
    {
        try {
            Class<?> bookClass = Class.forName("com.note.BookObject");
//            Class<?> bookClass = BookObject.class;
            BookObject bookObject = (BookObject)bookClass.newInstance(); //创建实例
            bookObject.publicMethod("public");

            Method[] methods = bookClass.getDeclaredMethods();//获取所有方法
            for(Method method:methods){
                System.out.println(method.getName());
            }

            Method publicMethod = bookClass.getDeclaredMethod("publicMethod",String.class);//获取指定方法
            publicMethod.invoke(bookObject,"invoke publicMethod");

            Field field = bookClass.getDeclaredField("name");
            field.setAccessible(true);//关闭安全检查
            System.out.println(field.get(bookObject));
            field.set(bookObject,"骆驼祥子"); //更改值

            Method privateMethod = bookClass.getDeclaredMethod("privateMethod");
            privateMethod.setAccessible(true);//关闭安全检查
            privateMethod.invoke(bookObject);


        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
    }
}
```
### 分析
获取Class的方式
1. `Class.forName("xx.xx.xx");` xx.xx.xx为完整类名
2. `XXClass.class` 类名.class
3. `BookObject book = new BookObject();  book.getClass();` 已创建实例对象.getClass();  

其中最常用的为1跟2。  
`getDeclaredMethod()` 可以获取指定方法。  
`invoke()`调用其方法。  
`getDeclaredField()`获取指定属性。  
调取私有方法或者属性需要关闭安全检查`setAccessible(true)`。  
作为一个业务仔，在平时的开发中几乎用不到反射，但是日常使用的spring框架中大量使用到了反射机制，以及刚接触jdbc的时候，`Class.forNmae("com.mysql.jdbc.Driver");`
# Class方法
|方法|介绍|
|-|-|
|asSubclass(Class<T> clazz) |把传递的类的对象转换成代表其子类的对象|
|Cast|把对象转换成代表类或是接口的对象|
|getClassLoader()|获得类的加载器|
|getClasses()|返回一个数组，数组中包含该类中所有公共类和接口类的对象|
|getDeclaredClasses()|返回一个数组，数组中包含该类中所有类和接口类的对象|
|forName(String className)|根据类名返回类的对象|
|getName()|获得类的完整路径名字|
|newInstance()|创建类的实例|
|getPackage()|获得类的包|
|getSimpleNanme()|获得类的名字|
|getSuperclass()|获得当前类继承的父类的名字|
|getInterfaces()|获得当前类实现的类或是接口|
|getField(String name)|获得某个公有的属性对象|
|getFields()|获得所有公有的属性对象|
|getDeclaredField(String name)|获得某个属性对象|
|getDeclaredFields()|获得所有属性对象|
|getAnnotation(Class<A> annotationClass)|返回该类中与参数类型匹配的公有注解对象|
|getAnnotations()|返回该类所有的公有注解对象|
|getDeclaredAnnotation(Class<A> annotationClass)|返回该类中与参数类型匹配的所有注解对象|
|getDeclaredAnnotations()|返回该类所有的注解对象|
|getConstructor(Class...<?> parameterTypes)|获得该类中与参数类型匹配的公有构造方法|
|getConstructors()|获得该类的所有公有构造方法|
|getDeclaredConstructor(Class...<?> parameterTypes)|获得该类中与参数类型匹配的构造方法|
|getDeclaredConstructors()|获得该类所有构造方法|
|getMethod(String name, Class...<?> parameterTypes)|获得该类某个共有的方法|
|getMethods()|获得该类所有公有的方法|
|getDeclaredMethod(String name, Class...<?> parameterTypes)|获得该类某个方法|
|getDeclaredMethods()|获得该类所有方法|
|isAnnotation()|是否是注解类型，是返回true|
|isAnnotationPresent(Class<? extends Annotation> annotationClass)|是否是指定类型注解类型，是返回true|
|isAnonymousClass()|是否是匿名类|
|isArray()|是否是数组类|
|isEnum()|是否是枚举类|
|isInstance(Object obj)|obj是否是该类的实例|
|isInterface()|是否是接口类|
|isLocalClass()|是否是局部类|
|isMemberClass()|是否是内部类|