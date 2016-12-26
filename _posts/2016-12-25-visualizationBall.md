---
layout:     post
title:      "使用RayCasting画一个球"
subtitle:   "圣诞节，转发这个漂亮的球，一年内都会好运！！"
date:       2016-12-25
author:     "hh23485"
summary:	"这是我科学计算可视化课程的作业，其中只一个关键的问题：如何在不懂openGL，不太懂C++的情况下，用rayCasting画一个（好看的）球。"
header-img: "img/bgimage-ball.jpg"
tags:
    - openGL    
    - 作业
---

{:toc #看东西} 

这是我科学计算可视化课程的作业，这个作业只一个关键的问题：如何在不懂openGL，不太懂C++的情况下，用rayCasting画一个球。

# 看东西

这是可视化的作业，最后生成了一个晶莹剔透的球。
在第一次运行成功，看到它的时候，在一堆乱乱糟糟的代码上面，在一个小小的单调的框子里，姑且称为“悬浮”着吧，像艺术品一样。

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-%E5%BD%A9%E7%90%83.png)

不过，在这个作业中遇到很多的问题，发出来记载一下，顺便分享一下算法和代码。

问题很多，一个个来。

***

# 可视化是啥

大作业的内容，`╮(╯_╰)╭`，老师的意思是

> 我们会讲很多算法，来随便挑一个算法画个简单的球体吧。

这是一个在开学上前两节课就布置的作业，截止日期是考试后10天，感觉这个挺水的吧。

> 嗯，画个球  
> 很简单

然而随着课程上下去，有点坐不住了，可视化的算法开始出现一些`向量`，`三线性插值`，`区间树`，`粒子云`之类的名词了 🤔。嗯？

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-0fc637d12f2eb938dfad948edc628535e4dd6faf.jpg)

于是，赶紧去网上翻了翻当时正在上的MC算法代码是啥样，发现...

1. 找不到代码
2. 找不到例子
3. 算法介绍全英文
4. 各种数学公式
4. 我都看不懂

于是乎，直等到`rayCasting`算法出现的时候，才稍微听懂一些算法的步骤了。

对了，科学计算可视化的意思是：

>运用计算机图形学或者一般图形学的原理和 方法，将科学与工程计算等产生的大规模有空间结构 的数据转换为图形、图像，以直观的形式表示出来。

***

#  RayCasting

## 先看个概念

首先

rayCasting：一种**直接体绘制**的方法。

然后

直接体绘制：将**体元**的**三维数据场**按照一定的**规则**、**不通过**中间图元**直接转换**为**图形显示**设备帧缓存中**的二维离散信号**及生成每个像素点颜色的**RGB值**。

再然后

体元：拿**均匀数据场**来说，就是三维数据场中由**八个顶点**划分的**正方体区域**。（其实就是一个均匀三维网格的一个小格子）。

最后

体数据：包含对象内部信息的三维实体。（其实，就是一个物体，在物体内部的空间点赋了一些有物理意义的值）

（上述的不严谨定义仅供快速理解)

## 那rayCasting呢

是一个算法，它的原理是这样的

1. 从你的视点向成像区域的每个像素发射一个射线（就是目光`→ v →`）。这个时候你要绘制的物体肯定是在成像区域的后面，目光碰的到的地方。
2. 然后目光会碰到体数据，然后穿过一个个体元，然后去向浩瀚的……
3. 光线穿过物体肯定要是透明的，不然就停下来了。这个时候，体元的颜色、透明度不断的附着在光线上，按照一定规则叠加。
4. 当射向方向不会在有物体的时候，就可以停下来了，对应的射线上的样子，就作为成像区域对应像素点的颜色值。

搞定。  
不过，现实生活中，是由物体反射太阳的光到眼睛里面的，倒过来计算的话，也没有什么问题。

来几张学术一点的图说明一下：

一般来说，在穿过体元的时候，采用离散的方式取采样点，这样合成有限的点就好了，或者就积分吧（不会）。

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-2016-12-21-14-58-07.jpg?imageView/2/w/400/q/90)

这是课件上的步骤图。


![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-2016-12-21-14-47-19.jpg?imageView/2/w/400/q/90)

***

# 开画吧

我倒是想。

不过，体数据的生成是非常容易的，给定一个中心点和一个半径，就能够通过遍历坐标来区分球外面和里面的空间坐标。球和空气的质量不一样，那就给不同的值即可，要来点花样，那就离球心越近，密度越小。

假如球的密度有颜色的话，那就是你设成你想到的那种颜色，当然不透明度不能为1，不然就只能看到个最外面了（画个圆，那干嘛要体数据。。）

接下来的问题比较关键，在不会openGL、不会合成的情况下，画这个体数据。

## 终于

舍友@穆鹏飞完成了一份rayCasting的代码，那，问题就解决了。

由于他在向github第一的java博客学习，还没有正式搭建博客，那就我来代劳吧。

## 体数据代码

这里是我生成体数据的代码。

```
//
//  Sphere.h
//  visualizationBall
//
//  Created by hh23485 on 2016/12/24.
//  Copyright © 2016年 😃. All rights reserved.
//

#include <cmath>
/**
 点结构
 */
struct point{
    int x;
    int y;
    int z;
};

/**
 颜色结构：rgb值和透明度
 */
struct color{
    float r;
    float b;
    float g;
    float transparency;
};

class sphere{
public:
    sphere(){}
    point center;
    int rad;
    color *Data;
    int Datalength;
    
    sphere(point center,int rad,int Datalength);
    
    bool inSphere(int i,int j,int k);
    
    int getOneOffset(int i, int j, int k);
    
    bool generateSphere();
};

/**
 球体构造函数

 @param center 中心坐标位置
 @param rad 球半径
 @param Datalength 体数据单维长度
 */
sphere::sphere(point center,int rad,int Datalength){
    this->center=center;
    this->rad=rad;
    this->Data=(color *)malloc(sizeof(color)*pow(Datalength,3));
    this->Datalength=Datalength;
    generateSphere();
}

/**
 判断坐标(i,j,k)是不是在球体内部

 @param i i
 @param j j
 @param k k
 @return 返回判断结果，在内部返回true，否则返回false
 */
bool sphere::inSphere(int i,int j,int k) {
    int rad2 = pow(rad, 2);
    int Datalength2 = pow(Datalength, 2);
    if ((i - center.x)*(i - center.x) + (j - center.y)*(j - center.y) + (k - center.z)*(k - center.z) <= rad2)
    {
        return true;
    }
    else {
        return false;
    }
}


/**
 获取坐标(i,j,k)在一维数组展开后的偏移坐标
 
 @param i i
 @param j j
 @param k k
 @return 偏移量
 */
int sphere::getOneOffset(int i, int j, int k) {
    int Datalength2 = pow(Datalength, 2);
    return k*Datalength2 + j*Datalength + i;
}


/**
 生成体数据
 体数据对应的值为颜色，bgr值分别对应坐标(i,j,k)的坐标分量

 @return 是否生成成功
 */
bool sphere::generateSphere(){
    if(!Data || !Datalength || !rad){
        return false;
    }
    for (int k = 0; k<Datalength; k++)
    {
        for (int j = 0; j<Datalength; j++)
        {
            for (int i = 0; i<Datalength; i++)
            {
                int offset = getOneOffset(i, j, k);
                color color;
                if (inSphere(i,j,k))
                {
                    color.r=(float)k/Datalength;
                    color.g=(float)j/Datalength;
                    color.b=(float)i/Datalength;
                    color.transparency = 0.3;
                }
                else {
                    color.r = color.g = color.b = 1.0f;
                    color.transparency = 0.009;
                }
                Data[offset] = color;
                
            }
        }
    }
    return true;
}
```

最开始的装逼似的前缀是xcode自动生成的，不是我干的，我一般都放[这个](http://bbs.csdn.net/topics/390866984)。

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-2016-12-25-15-44-33.jpg?imageView/2/w/400/q/90)

***

## 使用rayCasting的部分涉及到了openGL

### 首先在xcode里

由于MacOS自带了openGL，所以在xcode创建项目后，直接在项目中引入`GLUT.framework`，`OpenGL.framework`即可。他们在磁盘的位置是`/System/Library/Frameworks/`，如果使用了其他的IDE可以尝试自己引入。

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-2016-12-25-14-37-13.jpg)

### 如果是windows的话

可以先下载[GLUT](http://www.opengl.org/resources/libraries/glut/glutdlls37beta.zip)，将压缩包内的5个文件按照如下规则存放：

1. glut.h放入 `C:\Program Files(x86)\Windows Kits\8.1\Include\um\gl` 中，这个位置可能在不同机器上不同。不过有个小技巧，在vs中输入`#include <gl\>`，vs会悬浮显示gl的位置，找到那个位置，放进去即可。
2. glut.lib和glut32.lib放置到 `vs安装路径/VC/lib`下。
3. glut.dll和glut32.dll复制到`C:\Windows\System32`（32位）下或者`C:\Windows\SysWOW64`（64）位下。

如果出现无法打开 `glut.h` 无法打开的错误，则在VS`项目`属性的`VC++`的`库依赖`中加入上述**步骤1**的`gl`文件目录。这对我的win10 64bit是有效的。

## 最后

放上主函数和rayCasting相关的代码，在xcode中可以直接运行，但vs里需要修改`#include<GLUT/GLUT.h>`为`#include<gl/glut.h>`。

``` cpp

//作业截止后可见

```

这只是一份粗糙的代码，不过重在完整和可运行。先不考虑安全性啦，不过重新看这种代码到时回顾了一下c++通过指针来生成动态数组的方法。

运行，生成的结果就是这样，供以后的选了这个课的孩子们参考一下吧。

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-%E5%BD%A9%E7%90%83.png)

**再一遍，是不是很好看**

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-006tKfTcgw1fb32dyxekwg306y06y4pa.gif?imageView/2/w/50/q/90)

***

最后，大家圣诞快乐🎅🏻

另，据舍友说，RayCasting部分也是东拼西凑别的大神的代码修改加工的结果，不过由于找不到是哪些了，如果有冒犯的话，请联系我。

