---
layout:     post   				   
title:      Objective-C 学习笔记 				
subtitle:   都写在这里了...反正有目录
date:       2018-07-27				
author:     Fortune					
header-img: img/post-bg-YesOrNo.jpg 	
catalog: true 					
tags:								
    - Objective-C
---







## 类的声明与实现

### 声明（@interface）
example：

@interface className: fatherClass{
    
    //成员变量
}

//成员函数


@end

#### tip：
1. 成员变量声明在接口中才对子类可见
2. 成员函数的的声明格式：
    -(void) functionName: (int) value
3. 返回值前的负号（-）表示这是一个实例方法，
    如果是正号（+）则表示这是一个类方法，例如创建类的新实例

### 实现（@implementation）
example:

@implementation calssName{

    //成员变量
}

//成员方法

@end


## 类实例的声明

example:

1.

```objectiivec
Fraction *myFraction = [Fraction alloc];
myFraction = [myFraction init];
```

2.
```objectiviec
Fraction *myFraction = [[Fraction alloc] init];
```

3.
```objectivec
Fraction *myFraction = [Fraction new];
```

## 实例方法的调用
```
returnValue = [myFaction functionName: para];
```

## 分离接口与实现文件
1. 类的声明放在class.h文件中
2. 类的实现放在同名的.m文件中

## 合成存取方法
1. 在接口部分为属性添加@property注解
2. 在实现部分为属性添加@synthesize注解

### tip：
可通过点运算符访问和修改属性但是从编码风格上来讲不推荐使用点运算符来访问方法进行信息传递（依旧使用中括号）

## 具有多个参数的方法
```
-(int) setA: (int) a andB: (int) b;
```

```
-(int) setPara: (int) a : (int) b;
```

## self关键字
相当于**this**





## @class关键字

在头文件中当一个类A持有另一个类B的引用时，而你又不想引入类B的头文件，可以增加这样一条注解

```objective-c
@class B
```

来告诉编译器，B是一个类，这样可以降低编译器负担。



## 含有对象的类的存值方法的注意点

一个类A持有另一个类B的引用（指针），如果存值方法这么写：

```objective-c
-(void) setB: (B *) b{
    A_b = b;
}
```

很明显会出现这样的问题：**A中的B和传进来的参数指向了同一块内存，也就是说实例A并没有为自己持有的B申请一块内存，所以如果参数b发生改变，A中的A_b也会改变，这样的耦合度是绝对不能接受的！**

我们将代码改写为这样：

```objective-c
-(void) setB: (B *) b{
    if(!A_b){
        A_b = [B new];
    }
    A_b.para = b.para;
    //其他参数
}
```

### tip：

1. 同时我们需要提一下，通过@property 和 @synthesize自动合成的存取方法与第一种写法无异，只是简单的进行了指针的赋值。
2. 同时我们的取值方法同样存在类似的问题，所以我们可以在取值方法中创建对象副本进行返回，当然这会带来一定的内存开销。



## 类对象（class-object）

1. 通过类来获得类对象

   ```objective-c
   [A class]
   ```

2. 通过实例来获得类对象

   ```objective-c
   [a class]
   ```



使用类对象的方法（处理动态类型）

```objective-c
-(BOOL) isKindOfCalss: class-object
-(BOOL) isMemberOfClass: class-object
+(BOOL) isSubclassOfClass: class-object
```



## Selector

生成选择子：

```objective-c
@selector (alloc)
//example
@selector (setA:B:)
    
//利用selector处理动态类型
-(BOOL) respondsToSelector: Selector//实例对象调用
+(BOOL) instancesRespondToSelector: Selector//类调用

//example
[Fraction instancesRespondToSelector: @selector (setA:B:)];//也可以测试继承来的方法

-(id)performSelector: selector
//example
SEL action;
id graphicObject;

action = @selector (draw);

[graphicObject performSelector: action];
//selector 暂时理解为对某个方法的封装
```



## 异常处理

```objective-c
@try{
    //
}
@catch(NSException * exception){
    
}
@finally{
    
}

////////////
@throw//用于抛出异常
```

