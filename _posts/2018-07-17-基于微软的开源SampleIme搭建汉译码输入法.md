---
layout:     post   				   
title:      基于微软开源SampleIme搭建自己的输入法 				
subtitle:   SampleIme 
date:       2018-07-17 				
author:     Fortune					
header-img: img/tag-bg-o.jpg 	
catalog: true 					
tags:								
    - Input Method
---

# 基于微软的开源SampleIme搭建自己的输入法

### 摘要

本文以微软官网的SampleIme开源代码为基础，讲述如何基于自定义的字典实现一款简易输入法

环境为Windows10 + vs2015（我也不知道怎么就被老师安排了这么个完全没有做过的东西 WIN32的学习路径是在是太陡峭了😭）

### Step1 

下载SampleIme源码

这里附上下载地址：https://code.msdn.microsoft.com/windowsdesktop/Input-Method-Editor-IME-b1610980

或者我的rep：https://github.com/NJUSSJ/SampleIme

### Step2

这里提供两条实现路径，一条路径是基于InstallerShield大将源码打包成安装文件；另一条路径是基于ddl文件手动注册输入法。

### 这里我们首先讲述第二条路径：

#### 1


在vs中打开刚刚下载的SampleIme项目（即项目中的.sln文件），接下来分别在Release WIN32和Release x64的构建条件下build Project。

这时你会发现，编译报错以及可能会出现链接错误，这是由于编译器的版本差异导致的，下面讲述如何解决这些问题进行正确编译。

***首先，在Solution Explorer视图中选择SampleIme右击选择Properties，进入properties dialog -> C/C++ -> General 将treat warning as errors 选项修改为No***



***其次，还是在刚刚的properties dialog -> Linker -> General 将Enable Incremental Linking 选项改为Yes***

***接下来，进入Linker -> Optimization 将References选项修改为Yes***



这时候我们再次在两个不同的构建条件下进行编译

![1](https://raw.githubusercontent.com/NJUSSJ/NJUSSJ.github.io/master/sources/1.png)

编译成功！！

#### 2.

下面我们到资源管理器中找到项目你x64文件夹下的Release中的SampleIme.dll文件将其复制到C:\Windows\System32文件夹下；同理在项目的Release文件夹下将WIN32条件下编译的SampleIme.dll文件复制到C:\Windows\SysWOW64 文件夹下。

之后我们在SampleIme文件夹下找到Dictionary文件夹，里面有一份拼音码表，有兴趣的胖友可以打开熟悉以下码表的形式。我们将这份码表分别复制到刚刚提到的两个文件夹下（C:\Windows\System32 **&** C:\Windows\SysWOW64）

#### 3. 

以管理员权限打开cmd

然后键入这行命令：regsvr32 sampleIme.dll

然后退到上层文件夹（cd ..）

进入SysWOW64（cd SysWOW64）

同样运行这行命令：regsvr32 sampleIme.dll



![2](https://raw.githubusercontent.com/NJUSSJ/NJUSSJ.github.io/master/sources/2.png)



出现上图，说明注册成功。

![3](https://raw.githubusercontent.com/NJUSSJ/NJUSSJ.github.io/master/sources/3.png)



这时你会发现你的输入法中出现了Sample IME！*★,°*:.☆(￣▽￣)/$:*.°★* 。

随意打开一个编辑器尝试一下把。



### 利用InstallerShield生成安装软件：

btw，这个方法对于我这里安装的vs 2015 community不适用了，因为已经没有了默认的InstallerShield模板

需要安装Ultimate版本或者vs2013

安装后参照以下步骤：

#### 1.

- On the **File** menu, point to **New** and select **Project**. The **New Project** dialog opens.
- In the left pane, navigate to **Templates > Other Project Types > Setup and Deployment**, click **Enable InstallShield Limited Edition**, and click **OK**. Follow the installation instructions.
- Restart Visual Studio 2013.

#### 2.

- Open the IME solution (.sln) file.
- Build the solution（**Attention！！! in the mode of Debug WIN32**）.

#### 3.(这步的一些内容怎么开心怎么写就好...😊)

- In Solution Explorer, right-click the solution, point to **Add**, and select **New Project**. The **Add New Project** dialog opens.
- In the left tree view control, navigate to **Templates > Other Project Types > InstallShield Limited Edition**.
- In the center window, click **InstallShield Limited Edition Project**.
- In the **Name** text box, type "SetupIME" and click **OK**.
- In the **Project Assistant** dialog, click **Application Information**.
- Fill in your company name and the other fields.

#### 4.

- Click **Application Files**.
- In the left pane, right-click the **[INSTALLDIR]** folder, and select **New Folder**. Name the folder "Plugins".
- Click **Add Files**. Navigate to **SampleIME.dll**, which is in the **C++\Debug folder**, and add it to the **Plugins** folder. **Repeat** this step for the IME dictionary, which is in the **C++\SampleIME\Dictionary folder**.

#### 5.（Do not forget this!）

- Right-click the IME DLL and select **Properties**. The **Properties** dialog opens.
- In the **Properties** dialog, click the **COM & .NET Settings** tab.
- Under **Registration Type**, select **Self-registration** and click **OK**.

#### 6.

- Build the solution. The IME DLL is built, and InstallShield creates a setup.exe file that enables users to install your IME on Windows 10. The **setup.exe** file is located in the **SetupIME\SetupIME\Express\DVD-5\DiskImages\DISK1** folder.

### ps:

demo中提供的码表为全拼码表

这里我处理了一份五笔的码表以供参考（https://github.com/NJUSSJ/SampleIme/tree/master/C%2B%2B/SampleIME/Dictionary）

只需要将源码中define.h头文件中对应的字典文件名修改，以及上述步骤中的相应字典文件做个替换就可以了，其他步骤一样。



——————————————————————分割线2018-07-28———————————————————



## 关于无聊甲方的一些新需求（正好也更细节的学习了一下源码）

### 1. 为汉字在候选框添加拼音和标准码

首先想到的肯定是直接修改码表，不过在修改码表后存在的问题就是：选中候选字后，甲方爸爸所要求的拼音和标准码会一同输出，解决这个问题的策略当然是在输出的接口中对字符串做一个简单的截断，这里首先我们要先找到源码响应空格的流程（这里鸣谢一下一片CSDN没什么人看过的blog **@fonx**）并且通过猜想关键词Finalize定位到了源码中的**_HandleCandidateFinalize**方法（具体在哪个文件我就不详述了，感兴趣的旁友可以自己搜索），但是我们修改的并不是这个方法，我们在这个接口中进一步找到了**getSelectedCandidateString()**这个方法，进到这个方法后，直接修改了他的返回值（长度），这里我们暂时写死为1，因为甲方目前给出的码表并没有词组都是单字，不过计算长度的方法也已经写在对应的文件中，被我注释掉了。至此算是完成了一半了，不过进而引发了下一个问题。

### 2.取消联想

由于我们的码表中是有汉语拼音的，而我们最终截出来的只是这个标准码对应的文本的一部分，而源码中实现的联想功能（通过空格响应）是根据你选中的文本进行二次搜索更新CandidateList， 所以你也就知道了，如果截取第一个字作为选中候选字那么一定会触发联想，而且是不间断的触发，因为你选中的候选字你永远能在码表中找到一他开头的（这个也是通过反复试验发现的，我也记不清我tm反复注册解注册了多少次），首先想到的当然是直接取消他的联想功能（毕竟目前的需求只是单字嘛），所以我们定位到了在**CandidateUIPresenter.cpp**文件中的**_HandleCandidateWorker**方法，阅读代码后，直接注释掉了联想的部分（具体注释了哪些代码，可以参见我GitHub上的源码https://github.com/NJUSSJ/SampleIme），当然这个需求其实有更根本的解决方法，我也是更新这篇的时候刚想起来。参见第三点吧

### 3.解决WildCard + VK_Space 的异常

我们之前是实现了通配符搜索的，你可以把？理解为SQL中的_，把*理解为%。但是在源码中，他对通配符+空格的的响应做了特别的处理（具体代码参见**CompositionProcessorEngine.cpp**文件），想到的解决方案是将空格作为回车来处理（**这其实就是我刚刚说的解决第二点问题的更直接方法，因为回车不会触发联想...**）所以我们将所有对空格响应的**Function**替换为了对回车的响应，这里有一点是要注意的，就是在WildCard && VK_Space那里要记得把key的**Category**换成**Candidate**（不可以是**Composing**或者**Phrase**），其实这三个状态是递进的，大家自行理解。

至此，勉强完成，至于一些莫名其妙的需求，我就当没听见了...