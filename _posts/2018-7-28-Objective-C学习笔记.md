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

