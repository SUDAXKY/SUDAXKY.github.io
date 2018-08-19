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





# 2018-07-27

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





# 2018-07-29

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





# 2018-07-31

## 覆写init方法

```objective-c
-(id) init{
    if(slef = [super init]){
        //初始化代码
    }
    return self
}
```



init 的返回类型为 id 类型， 这是编写可能会被继承的类的一般规则。



## 实例变量的作用域

```objective-c
@interface Printer{
    @private
        int para1;
    	int para2;
    @protected
        int para3;
    @public
        //
    
}
@end
```



## 有关实例变量与属性

实例变量（成员变量）在@interface的{}内声明(不推荐使用了)，属性用@property声明

```objective-c
@interface Example
    @property Window *window
@end
    
@implementation Example
    @synthesize window = _window//可以不写也会合成存取方法和生成实例变量_window作为属性的底层存储
```



## 自定义存取方法实例

```objective-c
@interface Person : NSObject

@property (nonatomic, copy) NSString* name;
@property (nonatomic, assign) NSUInteger age;

@end

@implementation Person

//编译器会帮我们自动生成_name和_age这两个实例变量，下面代码就可以正常使用这两个变量了
@synthesize name = _name;
@synthesize age = _age;

- (void)setName:(NSString*)name {
    //必须使用_name来赋值，使用self.name来设置值时编译器会自动转为调用该函数，会导致无限递归
    //使用_name则是直接访问底层的存储属性，不会调用该方法来赋值
    //这里使用copy是为了防止NSMutableString多态
    _name = [name copy];
}

- (NSString*)name {
    //必须使用_name来访问属性值，使用self.name来访问值时编译器会自动转为调用该函数，会造成无限递归
    return _name;
}

@end
```



使用自定义的getter和setter一般是用来实现懒加载(lazy load)，在很多情况下很常用，比如:创建一个比较大的而又不一定会使用的对象，可以按照如下方法编写。



```objective-c
@property (nonatomic, strong) CustomObject *customObject;

@synthesize customObject = _customObject;

- (CustomObject*) customObject {
    if (_customObject == nil) {
        //初始化操作，会调用setter方法
        self.customObject = [[CustomObject alloc] init];
        //不会造成无限递归，因为这里的self.cunstomerObject = 调用的是setter方法
        //如果按照如下方法编写不会调用setter方法，如果自定义setter方法需要完成一些事情建议使用self.customObject的方式来设置
        //_customObject = [[CustomObject alloc] init];
    }
    return _customObject;
}
```



## @property 的指示符

### atomic/nonatomic

指定合成存取方法是否为原子操作，可以理解为是否线程安全，但在iOS上即时使用`atomic`也不一定是线程安全的，要保证线程安全需要使用锁机制，超过本文的讲解范围，可以自行查阅。  可以发现几乎所有代码的属性设置都会使用`nonatomic`，这样能够提高访问性能，在iOS中使用锁机制的开销较大，会损耗性能。



### readwrite/readonly

readwrite合成getter和setter方法，readonly只合成getter方法



### assign/weak/unsafe_unretained

`assign`表示对属性只进行简单的赋值操作，不更改所赋的新值的引用计数，也不改变旧值的引用计数，常用于标量类型，如`NSInteger`，`NSUInteger`，`CGFloat`，`NSTimeInterval`等。  `assign`也可以修饰对象如`NSString`等类型对象，上面说过使用`assign`修饰不会更改所赋的新值的引用计数，也不改变旧值的引用计数，如果当所赋的新值引用计数为0对象被销毁时属性并不知道，编译器不会将该属性置为`nil`，指针仍旧指向之前被销毁的内存，这时访问该属性会产生野指针错误并崩溃，因此使用`assign`修饰的类型一定要为标量类型。



```objective-c
@interface Person : NSObject

@property (nonatomic, assign) NSString* name;
@property (nonatomic, assign) NSUInteger age;

@end

@implementation Person

@synthesize name = _name;
@synthesize age = _age;

@end

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *p = [[Person alloc] init];
        //这里使用NSMutableString而不使用NSString是因为NSString会缓存字符串，后面置空的时候实际没有被销毁
        NSMutableString *s = [[NSMutableString alloc] initWithString:@"Jiaming Chen"];
        //设置p.name不会增加s的引用计数，只是单纯将s指向的地址赋给p.name
        p.name = s;
        //输出两个变量的内存地址，可以看出是一致的
        NSLog(@"%p %p", p.name, s);
        //这里可以正常访问name
        NSLog(@"%@ %ld", p.name, p.age);
        //将上述字符串置空，引用计数为0，对象被销毁
        s = nil;
        //查看其地址时仍然可以访问到，表示其仍然指向那一块内存
        NSLog(@"%p", p.name);
        //访问内容时发生野指针错误，程序崩溃。因为对象已经被销毁
        NSLog(@"%@ %ld", p.name, p.age);
    }
    return 0;
}
```



使用`unsafe_unretained`修饰时效果与`assign`相同，不会增加引用计数，当所赋的值被销毁时不会被置为`nil`可能会发生野指针错误。`unsafe_unretained`与`assign`的区别在于，`unsafe_unretained`只能修饰对象，不能修饰标量类型，而`assign`两者均可修饰。



### week/strong

`strong`表示属性对所赋的值持有强引用表示一种“拥有关系”(owning relationship)，会先保留新值即增加新值的引用计数，然后再释放旧值即减少旧值的引用计数。只能修饰对象。如果对一些对象需要保持强引用则使用`strong`。

`weak`表示对所赋的值对象持有弱引用表示一种“非拥有关系”(nonowning relationship)，对新值不会增加引用计数，也不会减少旧值的引用计数。所赋的值在引用计数为0被销毁后，`weak`修饰的属性会被自动置为`nil`能够有效防止野指针错误。 



### copy

`copy`修饰的属性会在内存里拷贝一份对象，两个指针指向不同的内存地址。  一般用来修饰有对应可变类型子类的对象。  如：`NSString/NSMutableString`,`NSArray/NSMutableArray`,`NSDictionary/NSMutableDictionary`等。  为确保这些不可变对象因为可变子类对象影响，需要`copy`一份备份，如果不使用`copy`修饰，使用`strong`或`assign`等修饰则会因为多态导致属性值被修改。 



使用strong修饰不可变对象：

```objective-c
@interface Person : NSObject

//使用strong修饰NSString
@property (nonatomic, strong) NSString* name;
@property (nonatomic, assign) NSUInteger age;

@end

@implementation Person

@synthesize name = _name;
@synthesize age = _age;

@end


int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *p = [[Person alloc] init];
        NSMutableString *s = [[NSMutableString alloc] initWithString:@"Jiaming Chen"];
        //将可变字符串赋值给p.name
        p.name = s;
        //输出的地址和内容均一致
        NSLog(@"%p %p %@ %@", p.name, s, p.name, s);
        //修改可变字符串s
        [s appendString:@" is a good guy"];
        //再次输出p.name被影响
        NSLog(@"%p %p %@ %@", p.name, s, p.name, s);
    }
    return 0;
}
```



对于可变对象类型，如`NSMutableString`、`NSMutableArray`等则不可以使用`copy`修饰，因为`Foundation`框架提供的这些类都实现了`NSCopying`协议，使用`copy`方法返回的都是不可变对象，如果使用`copy`修饰符在对可变对象赋值时则会获取一个不可变对象，接下来如果对这个对象进行可变对象的操作则会产生异常，因为`OC`没有提供`mutableCopy`修饰符，对于可变对象使用`strong`修饰符即可。



```objective-c
@interface Person : NSObject

//使用copy修饰NSMutableString
@property (nonatomic, copy) NSMutableString* name;
@property (nonatomic, assign) NSUInteger age;

@end

@implementation Person

@synthesize name = _name;
@synthesize age = _age;

@end


int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *p = [[Person alloc] init];
        NSMutableString *s = [[NSMutableString alloc] initWithString:@"Jiaming Chen"];
        //将可变字符串赋值给p.name
        p.name = s;
        //输出的地址不一致，内容一致
        NSLog(@"%p %p %@ %@", p.name, s, p.name, s);
        //修改p.name，此时抛出异常
        [p.name appendString:@" is a good guy."];
    }
    return 0;
}
```



有时候我们需要`copy`一个对象，或是`mutableCopy`一个对象，这时需要遵守`NSCopying`和`NSMutableCopying`协议，来实现`copyWithZone:`和`mutableCopyWithZone:`两个方法，而不是重写`copy`和`mutableCopy`两个方法。 
`Foundation`框架中的很多数据类型已经帮我们实现了上述两个方法，因此我们可以使用`copy`方法和`mutableCopy`方法来复制一个对象，两者的区别在于`copy`的返回值仍未不可变对象，`mutableCopy`的返回值为可变对象。

| copy       | mutableCopy                    |
| ---------- | ------------------------------ |
| NS*        | 浅拷贝，只拷贝指针，地址相同   |
| NSMutable* | 单层深拷贝，拷贝内容，地址不同 |

由上述表格可以看出，对于不可变类型，使用`copy`方法时是浅拷贝，只拷贝指针，因为内容是不会变化的。使用`mutableCopy`时由于返回可变对象因此需要一份拷贝，供其他对象使用。对于可变类型，不管是`copy`还是`mutableCopy`均会进行深拷贝，所指向指针不同。

前文介绍`copy`修饰符的时候讲过，在修饰`NSString`这样的不可变对象的时候使用`copy`修饰，但其实当给对象赋一个`NSString`时仍旧只复制了指针而不是拷贝内容，原因同上。



```objective-c
@interface Person : NSObject

//使用copy修饰
@property (nonatomic, copy) NSString* name;
@property (nonatomic, assign) NSUInteger age;

@end

@implementation Person

@synthesize name = _name;
@synthesize age = _age;

@end


int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *p = [[Person alloc] init];
        NSString *s = @"Jiaming Chen";
        p.name = s;
        //p.name的地址与s地址相同，不可变对象copy是浅拷贝
        NSLog(@"%p %p %@ %@", p.name, s, p.name, s);
    }
    return 0;
}
```



