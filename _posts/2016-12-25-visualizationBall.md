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

``` cpp
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
#include<stdio.h>
#include<stdlib.h>
#include<math.h>
#include <GLUT/GLUT.h>  //windows下为<gl/glut.h>
#include "Sphere.h"     //球体类

//-------------------函数声名
//数据分类
void Classify(sphere &ball, float* CData);
//求取图像空间到物体空间变换的旋转矩阵
void RotationMatrix(float *R, float *eye, float *center, float *up);
//合成像素颜色值
void Composite(float *rgba, int x0, int y0, float *CData, float *R, float *T);
//求光线与包围盒交点坐标
bool Intersection(float *startpos, float *pos, float *dir);
//三线性插值
void TrInterpolation(float *rgba, float *pos, float *CData);
//判断点是否在包围盒内
bool CheckinBox(float *point);
//矩阵向量乘积
void MatrixmulVec(float *c, float *a, float *b);
//向量叉乘
void CrossProd(float *c, float *a, float *b);
//向量归一化
void Normalize(float *norm, float *a);
//显示图像
void Mydisplay();

//-------------------全局变量声名
#define EPSILON 0.000001
#define WIDTH 500
#define HEIGHT 500
int Datelength=200;                                         //设置球体数据单维数据长度
float* Image=(float*)malloc(sizeof(float)*WIDTH*HEIGHT*4);  //设置图像数组大小

//-------------------主函数
int main(int argc, char **argv)
{
    point sphere_center={100,100,100};  //球中心坐标
    int rad=100;                        //球半径
    sphere ball(sphere_center,rad,Datelength);  //生成球体数据
    float *CData = (float*)malloc(sizeof(float)*pow(Datelength,3) * 4); //初始化颜色数据数组
    float R[9];                         //旋转矩阵
    float T[3] = { 0,-30,350 };         //平移向量（要根据R调整，以保证获得体数据全貌）
    float eye[3] = { 0.5,0.5,1 };       //视点位置
    float center[3] = { 0,0,0 };        //物体参考点位置
    float up[3] = { 0,1,0 };            //相机朝上的方向
    
    RotationMatrix(R, eye, center, up); //获得旋转矩阵
    Classify(ball,CData);               //将体数据转换为颜色值
    
    float *LinePS = Image;
    for (int j = 0; j<HEIGHT; j++)      //逐个合成像素值
    {
        for (int i = 0; i<WIDTH; i++)
        {
            Composite(LinePS, i, j, CData, R, T);
            LinePS += 4;
        }
    }
    free(CData);
    
    //使用OpenGL显示此二维图像
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGBA);
    glutInitWindowSize(400, 400);
    glutInitWindowPosition(300, 300);
    glutCreateWindow("Ray-Casting");
    //背景设为白色
    glClearColor(0, 0, 0, 0);
    //显示图像
    glutDisplayFunc(Mydisplay);
    glutMainLoop();
}

//-------------------方法实现

/**
 取出体数据赋值颜色和透明度

 @param ball 球体
 @param CData 颜色数据
 */
void Classify(sphere &ball, float* CData)
{
//    color *LinePS = ball.Data;
    float *LinePD = CData;
    for (int k = 0; k<ball.Datalength; k++)
    {
        for (int j = 0; j<ball.Datalength; j++)
        {
            for (int i = 0; i<ball.Datalength; i++)
            {
                int offset=ball.getOneOffset(i, j, k);
                LinePD[0] = ball.Data[offset].r;
                LinePD[1] = ball.Data[offset].g;
                LinePD[2] = ball.Data[offset].b;
                LinePD[3] = ball.Data[offset].transparency;
                LinePD += 4;
            }
        }
    }
}


/**
 图像空间到物体空间转换

 @param R 旋转矩阵
 @param eye 视点位置
 @param center 物体参考点位置
 @param up 相机朝上的方向
 */
void RotationMatrix(float *R, float *eye, float *center, float *up)
{
    float XX[3], YY[3], ZZ[3];//图像空间的基向量
    ZZ[0] = eye[0] - center[0];
    ZZ[1] = eye[1] - center[1];
    ZZ[2] = eye[2] - center[2];
    CrossProd(XX, up, ZZ);
    CrossProd(YY, ZZ, XX);
    Normalize(XX, XX);
    Normalize(YY, YY);
    Normalize(ZZ, ZZ);
    //由图像空间基向量构成旋转矩阵
    R[0] = XX[0]; R[1] = YY[0]; R[2] = ZZ[0];
    R[3] = XX[1]; R[4] = YY[1]; R[5] = ZZ[1];
    R[6] = XX[2]; R[7] = YY[2]; R[8] = ZZ[2];
}


/**
 合成像素值

 @param rgba 合成颜色值
 @param x0 二维x坐标
 @param y0 二维y坐标
 @param CData 颜色数据
 @param R 空间转换坐标
 @param T 平移向量
 */
void Composite(float *rgba, int x0, int y0, float *CData, float *R, float *T)
{
    int stepsize = 1;//采样步长
    float cumcolor[4];//累计颜色值
    cumcolor[0] = cumcolor[1] = cumcolor[2] = cumcolor[3] = 0.0;
    float pos[3], dir[3];//投射光线起点、方向
    float startpos[3];//光线与包围盒近视点处的交点坐标
    float samplepos[3];//采样点坐标
    float samplecolor[4];//采样点颜色
    //采用平行投影，故在图像空间中投射光线的方向(0,0,-1),起点(x0,y0,0) z坐标代表上下
    pos[0] = x0; pos[1] = y0; pos[2] = 0;
    //将光线描述转换到物体空间
    //*********************************//
    dir[0] = -R[2]; dir[1] = -R[5]; dir[2] = -R[8];//光线方向在物体空间的表达
    MatrixmulVec(pos, R, pos);//旋转
    pos[0] += T[0];//平移
    pos[1] += T[1];
    pos[2] += T[2];
    //*********************************//
    if (Intersection(startpos, pos, dir))//判断光线与包围盒是否相交
    {
        samplepos[0] = startpos[0];
        samplepos[1] = startpos[1];
        samplepos[2] = startpos[2];
        while (CheckinBox(samplepos) && cumcolor[3]<1)//当光线射出包围盒或累计不透明度超过1时中止合成
        {
            TrInterpolation(samplecolor, samplepos, CData);//三线性插值获得采样点处的颜色及不透明度
            //合成颜色及不透明度,采用的是从前到后的合成公式
            cumcolor[0] += samplecolor[0] * samplecolor[3] * (1 - cumcolor[3]);//R
            cumcolor[1] += samplecolor[1] * samplecolor[3] * (1 - cumcolor[3]);//G
            cumcolor[2] += samplecolor[2] * samplecolor[3] * (1 - cumcolor[3]);//B
            cumcolor[3] += samplecolor[3] * (1 - cumcolor[3]);				//A
            //下一个采样点
            samplepos[0] += dir[0] * stepsize;
            samplepos[1] += dir[1] * stepsize;
            samplepos[2] += dir[2] * stepsize;
        }
        rgba[0] = cumcolor[0];
        rgba[1] = cumcolor[1];
        rgba[2] = cumcolor[2];
        rgba[3] = cumcolor[3];
        return;
    }
    rgba[0] = rgba[1] = rgba[2] = rgba[3] = 1.0;//若光线与包围盒不相交，赋白色
}


/**
 求交
 思路：将包围盒6个面无限扩展，并分成3组，即平行于XOY,YOZ,ZOX平面的各有2个；
 求光线与6个平面的交点，从每组的2个交点中选出距离视点较近者，这样得到3个候
 选交点；从这3个候选交点中选出距离视点最远的那个。最后判断这个点是否落在包
 围盒内，若在，即为光线与包围盒的靠近视点处的交点。
 @param startpos 靠近视点的交点坐标
 @param pos 光线起点坐标
 @param dir 光线方向向量
 @return 返回是否相交，相交为true，否则返回false；
 */
bool Intersection(float *startpos, float *pos, float *dir)
{
    float nearscale = -1000000;
    float scale1, scale2;
    //光线与包围盒平行于YOZ的2个平面交点
    if ((dir[0] <= -EPSILON) || (dir[0] >= EPSILON))
    {
        scale1 = (0 - pos[0]) / dir[0];
        scale2 = (Datelength - 1 - pos[0]) / dir[0];
        //选出靠近视点的交点，并与当前候选点比较，保留较远者
        if (scale1 < scale2)
        {
            if (scale1 > nearscale)
                nearscale = scale1;
        }
        else
        {
            if (scale2 > nearscale)
                nearscale = scale2;
        }
    }
    //光线与包围盒平行于ZOX的2个平面交点
    if ((dir[1] <= -EPSILON) || (dir[1] >= EPSILON))
    {
        scale1 = (0 - pos[1]) / dir[1];
        scale2 = (Datelength - 1 - pos[1]) / dir[1];
        //选出靠近视点的交点，并与当前候选点比较，保留较远者
        if (scale1 < scale2)
        {
            if (scale1 > nearscale)
                nearscale = scale1;
        }
        else
        {
            if (scale2 > nearscale)
                nearscale = scale2;
        }
    }
    //光线与包围盒平行于XOY的2个平面交点
    if ((dir[2] <= -EPSILON) || (dir[2] >= EPSILON))
    {
        scale1 = (0 - pos[2]) / dir[2];
        scale2 = (Datelength - 1 - pos[2]) / dir[2];
        //选出靠近视点的交点，并与当前候选点比较，保留较远者
        if (scale1 < scale2)
        {
            if (scale1 > nearscale)
                nearscale = scale1;
        }
        else
        {
            if (scale2 > nearscale)
                nearscale = scale2;
        }
    }
    startpos[0] = pos[0] + nearscale * dir[0];
    startpos[1] = pos[1] + nearscale * dir[1];
    startpos[2] = pos[2] + nearscale * dir[2];
    //判断该点是否在包围盒内，在才有效，不在则无效
    return CheckinBox(startpos);
}


/**
 三线性插值

 @param rgba 插值结果
 @param pos 采样点坐标
 @param CData 颜色数据
 */
void TrInterpolation(float *rgba, float *pos, float *CData)
{
    int x0, y0, z0, x1, y1, z1;
    float fx, fy, fz;
    float v0, v1, v2, v3, v4, v5, v6;
    int Slicesize = Datelength * Datelength * 4;
    int Stepsize = Datelength * 4;
    x0 = (int)pos[0];   //整数部分
    y0 = (int)pos[1];
    z0 = (int)pos[2];
    fx = pos[0] - x0;   //小数部分
    fy = pos[1] - y0;
    fz = pos[2] - z0;
    x1 = x0 + 1;
    y1 = y0 + 1;
    z1 = z0 + 1;
    if (x1 >= Datelength)x1 = Datelength - 1;   //防止越界
    if (y1 >= Datelength)y1 = Datelength - 1;
    if (z1 >= Datelength)z1 = Datelength - 1;
    for (int i = 0; i<4; i++)
    {
        //采样点处的值由邻近的8个点插值获得
        v0 = CData[z0*Slicesize + y0*Stepsize + 4 * x0 + i] * (1 - fx) + CData[z0*Slicesize + y0*Stepsize + 4 * x1 + i] * fx;
        v1 = CData[z0*Slicesize + y1*Stepsize + 4 * x0 + i] * (1 - fx) + CData[z0*Slicesize + y1*Stepsize + 4 * x1 + i] * fx;
        v2 = CData[z1*Slicesize + y0*Stepsize + 4 * x0 + i] * (1 - fx) + CData[z1*Slicesize + y0*Stepsize + 4 * x1 + i] * fx;
        v3 = CData[z1*Slicesize + y1*Stepsize + 4 * x0 + i] * (1 - fx) + CData[z1*Slicesize + y1*Stepsize + 4 * x1 + i] * fx;
        v4 = v0*(1 - fy) + v1*fy;
        v5 = v2*(1 - fy) + v3*fy;
        v6 = v4*(1 - fz) + v5*fz;
        if (v6>1)v6 = 1;    //防止越界
        rgba[i] = v6;
    }
}

/**
 点是否在包围盒内

 @param point 点坐标
 @return 返回判断结果，在返回true，否则返回false;
 */
bool CheckinBox(float *point)
{
    if (point[0] < 0 || point[0] >= Datelength || point[1] < 0 || point[1] >= Datelength || point[2] < 0 || point[2] >= Datelength)
        return false;
    else
        return true;
}

/**
 矩阵乘向量

 @param c 结果
 @param a op矩阵
 @param b op向量
 */
void MatrixmulVec(float *c, float *a, float *b)
{
    float x, y, z;
    x = a[0] * b[0] + a[1] * b[1] + a[2] * b[2];
    y = a[3] * b[0] + a[4] * b[1] + a[5] * b[2];
    z = a[6] * b[0] + a[7] * b[1] + a[8] * b[2];
    c[0] = x;
    c[1] = y;
    c[2] = z;
}

/**
 向量叉乘

 @param c 结果
 @param a op向量1
 @param b op向量2
 */
void CrossProd(float *c, float *a, float *b)
{
    float x, y, z;
    x = a[1] * b[2] - b[1] * a[2];
    y = a[2] * b[0] - b[2] * a[0];
    z = a[0] * b[1] - b[0] * a[1];
    c[0] = x;
    c[1] = y;
    c[2] = z;
}

/**
 向量归一化

 @param norm 归一化结果
 @param a 输入向量
 */
void Normalize(float *norm, float *a)
{
    float len = sqrt(a[0] * a[0] + a[1] * a[1] + a[2] * a[2]);
    norm[0] = a[0] / len;
    norm[1] = a[1] / len;
    norm[2] = a[2] / len;
}

/**
 绘制
 */
void Mydisplay()
{
    glClear(GL_COLOR_BUFFER_BIT);
    glDrawPixels(WIDTH, HEIGHT, GL_RGBA, GL_FLOAT, Image);//使用OpenGL的绘图函数
    glFlush();
}

```

这只是一份粗糙的代码，不过重在完成和可运行。先不考虑安全性啦，不过重新看这种代码到时回顾了一下c++通过指针来生成动态数组的方法。

运行，生成的结果就是这样，供以后的选了这个课的孩子们参考一下吧。

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-%E5%BD%A9%E7%90%83.png)

**再一遍，是不是很好看**

![](http://oiqdakvix.bkt.clouddn.com/2016-12-25-006tKfTcgw1fb32dyxekwg306y06y4pa.gif?imageView/2/w/50/q/90)

***

最后，大家圣诞快乐🎅🏻

另，据舍友说，RayCasting部分也是东拼西凑别的大神的代码修改加工的结果，不过由于找不到是哪些了，如果有冒犯的话，请联系我。

