---
title: 设计模式-工厂模式
date: 2019-09-26 22:57:26
categories:
- 设计模式
tags:
- 设计模式
- 笔记
---
对于设计模式，虽然在上大学的时候学过，但是一毕业，东西都还给了老师，脑海里只留下几丝印象。  
在平时的工作中，我也会遇到一些对象名都是以Factory为结尾的，这就让我想起了工厂模式。工厂模式在java中也是一个常用的设计模式之一，觉得有必要对几个常用的设计模式进行下温习。  
<!--more-->
# 简单工厂模式
简单工厂又可以称为静态工厂。  
最直观的表现来看，工厂模式是在实例化对象时不再使用new的方式，而是根据你给出的描述，工厂会返回相应的对象。
``` java
public interface Animal {
    public void desc();
}
```
``` java
public class Cat implements Animal {
    @Override
    public void desc(){
        System.out.println("猫");
    }
}
```
``` java
public class Dog implements Animal {
    @Override
    public void desc(){
        System.out.println("狗");
    }
}
```
在没有使用简单工厂模式之前，创建对象是这样的：
``` java
public class App 
{
    public static void main( String[] args )
    {
        Animal cat=new Cat();
        Animal dog=new Dog();
        cat.desc();
        dog.desc();
    }
}
```
在使用简单工厂模式之后：
``` java
public class EasyFactory {
    public static Animal getAnimal(String speak){
        Animal animal=null;
        switch (speak){
            case "miao":
                animal= new Cat();
                break;
            case "wang":
                animal= new Dog();
                break;
            default:
                System.out.println("is null");
        }
        return animal;
    }
}
```
``` java 
public class App 
{
    public static void main( String[] args )
    {
        EasyFactory.getAnimal("miao").desc();
        EasyFactory.getAnimal("wang").desc();
    }
}
```
在简单工厂里，不需要知道子类是啥，只需要提供一个字符串，就可以得相应的对象。当对子类进行修改添加时候，我们只需要对着Factory进行修改，无需修改客户端代码。
# 工厂模式
对于简单工厂来说，每增加一个子类，在工厂类就进行一次修改。当子类过多时，会导致工厂类庞大臃肿，不利维护。这时候就需要知道工厂模式了。
``` java 
public interface Factory {
    Animal getAnimal();
}
```
``` java
public class DogFactory implements Factory {
    @Override
    public Animal getAnimal() {
        return new Dog();
    }
}
```
``` java 
public class CatFactory implements Factory {
    @Override
    public Animal getAnimal() {
        return new Cat();
    }
}
```
``` java 
public class App 
{
    public static void main( String[] args )
    {
        Factory dogFactory=new DogFactory();
        Factory catFactory=new CatFactory();
        dogFactory.getAnimal().desc();
        catFactory.getAnimal().desc();
    }
}
```

# 抽象工厂模式
当工厂不仅仅生产一种东西时，就有抽象工厂。
``` java 
public interface Food {
    public void desc();
}
```
``` java 
public class CatFood implements  Food {
    @Override
    public void desc() {
        System.out.println("猫粮");
    }
}
```
``` java 
public class DogFood implements Food {
    @Override
    public void desc() {
        System.out.println("狗粮");
    }
}
```
``` java 
public interface Factory {
    Animal getAnimal();
    Food getFood();
}
```
``` java 
public class CatFactory implements Factory {
    @Override
    public Animal getAnimal() {
        return new Cat();
    }

    @Override
    public Food getFood() {
        return new CatFood();
    }
}
```
``` java 
public class DogFactory implements Factory {
    @Override
    public Animal getAnimal() {
        return new Dog();
    }

    @Override
    public Food getFood() {
        return new DogFood();
    }
}
```
``` java 
public class App 
{
    public static void main( String[] args )
    {
        Factory dogFactory=new DogFactory();
        Factory catFactory=new CatFactory();
        dogFactory.getAnimal().desc();
        dogFactory.getFood().desc();
        catFactory.getAnimal().desc();
        catFactory.getFood().desc();
    }
}
```
# 最后
首先了解一下什么是开闭原则：对扩展开放，对修改封闭。  
在简单工厂模式里，每增加一个类，都需要对工厂类进行了修改，不符合开闭原则。  
而在工厂模式里，增加一个类，就会增加一个工厂，没有对其原来的代码进行修改，所以符合开闭原则。  
在抽象工厂模式中的例子。狗和猫属于一个产品等级，而狗和狗粮属于一个产品族。当产品族中新增加一个产品时，对应的工厂类的接口也需要新增接口，这就对原来的进行了修改，不符合开闭原则。
但是，新增加一个工厂，只需要新加上对应的工厂类以及产品类，没有对原来的进行修改，符合开闭原则。可以说抽象工厂模式是有一定的开闭原则倾斜性。