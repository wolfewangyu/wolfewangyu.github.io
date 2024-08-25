---
layout: post
title: Blender中LED素材渲染
date: 2024-08-25 12:37 +0800
---

对于我们即将到来的业务，很大程度上是要体现屏幕内容的，这两天看到了一个youtube博主关于这方面的介绍，
https://www.youtube.com/watch?v=gLdZzjxGCS0  

案例中有几个信息点值得借鉴：
1. 屏幕素材的亮度调整通常会引起过曝（flow out溢出效果），为了避免，并且灵活增加被照射场景亮度（如地面，墙面）采用的一个方案
2. editor内的一些工作流程，提高效果，如：Wrangler的使用（ctrl + T快捷键）, shader editor中的几个贴图结构的复制（base color, bump, roughness等等）
3. 其他：屏幕所在的简单空间的搭建：使用了solidifier, array两个modifier, 使用了path来做圆柱支撑。渲染中整体volume的使用

渲染效果（无volume和没有volume的）

<!--图片排列用的模版，width 根据不同的图片数量可以调整为 100除以图片数量，再少一点（空隙）-->
<div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
    <img src="https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/tWHotg.png" alt="no volume" style="width: 49%; height: auto; margin-right: 10px;">
    <img src="https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/VNWASa.png" alt="volume" style="width: 49%; height: auto;">
</div>



案例步骤：
新建Plane
赋材质，贴入LED素材（UV贴图调整），调整shader中的Emission调整
环境空间制作  


# 新建Plane  
这里注意，小键盘（numPad中，如果不激活numLock则实际会变成对timeline的控制）

<!--图片排列用的模版，width 根据不同的图片数量可以调整为 100除以图片数量，再少一点（空隙）-->
<div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
    <img src="https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/1j9RNA.png" alt="Image 1" style="width: 20%; height: auto; margin-right: 10px;">

</div>



# 材质，video及UV调整

## 编辑Shader Editor节点
打开shader Editor：  
Ctrl + T: 自动为node添加texture, mapping
（adds on, community, 搜索nodes 找到nodes wrangler激活）
这里可以仔细看一下一个Base color的贴图和mapping，UV的关系
![PXGZnC](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/PXGZnC.png)  


选择plane, tab进入编辑模式，点2（面模式）

## 视频设置
在image Texture中选择视频文件，打开选中视频文件，选择视频文件，设置视频长度（frame）及起始位置（第几帧），勾选Cyclic, autofresh。

![75e72J](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/75e72J.png)


设置，注意右上角要选择view port shading才能看到材质的样子

![3jnaH1](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/3jnaH1.jpg)


## 关于UV
右键激活对象的面：UVfaces（或者按U）


1）先unwrapped  
2）调整到正向plane的视图  
3）Project from view （左侧贴图出现一个框，就是要贴的面的比例框） ，2，3步骤非常重要

![gEZufe](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/gEZufe.png)

4） 左侧的框进入面编辑模式，s放大  
![4HKZER](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/4HKZER.png)

*如果是比例一致可以直接（project from bounds）, 或者smart UV scale to bounds. Bounds这个指的就是贴进去的素材本身。

## 素材的发光设置
【重要】
如果只用一个emission就会出现调整strength的时候，屏幕内容太亮会过曝，更多的时候是希望调整被屏幕照亮的环境（如地面等），用这个方式，上面的emission是调整环境亮度，下面的emission是调整屏幕本身素材亮度
![pGpiUk](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/pGpiUk.jpg)


# 环境空间制作

环境空间制作，比较简单
## ceiling结构：
复制一个plane, 右键Subdivisions
Insect, (点击键盘i两次，插入面)
Modifier: solidify（增加厚度）, bevel（倒角）

![m2Nvct](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/m2Nvct.png)

背景调整到黑色：World, background to black

## volume
volumne(视频11:47), 增加氛围， 在world中设置，这个是整体加雾气，出一个朦胧感觉的设置
参数设置：
选择principle Volumes  
density要小一点0.05，如果到1就什么也看不见了  


<div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
    <img src="https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/130pPP.png" alt="Image 1" style="width: 20%; height: auto; margin-right: 10px;">
</div>

## 地面的材质

粘贴材质
屋顶框架设置完了之后（material 03）,地面直接选择material 003,同样的材质不需要重新设置了，注意要点击那个小盾牌上的2，等于新建了一个！！！
![3mLYm8](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/3mLYm8.png)

# 其他结构制作： bars屏幕支撑
（这部分详情看视频）

关于屏幕的固定，使用几个贴的bar
Path,画直线，然后在Data, Geometry, Depth增加厚度（圆柱）

modifier是个array

之前有个小错误：法线部分应该用bump（凸起），之前选的normal map, 下面是正确的节点

![1vXTrR](https://para-1255470189.cos.ap-nanjing.myqcloud.com/uPic/1vXTrR.png)



# 文内资源
原视频地址
https://www.youtube.com/watch?v=gLdZzjxGCS0  

材质资源（免费）
https://ambientcg.com/list  

Realtime materials资源库（blender插件，可以实时显示材质在模型上的显示）
https://blendermarket.com/products/realtime-materials-for-blender  



