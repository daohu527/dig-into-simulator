Map 目录主要是画出道路元素
ApolloMapTool 之后对画出的线路进行计算，即给出道路的左邻居，右邻居以及其他结构化信息。

Map -> ApolloMapTool 

Map -> MapManagerData  游戏中获取道路的结构化信息

NPCController 采用PID控制，目前NPC的行为比较简单，可以增加一些简单的行为。NPC目前采用的是移动刚体？另外需要注意使能物理引擎和不使能物理引擎的区别。

Bridge是否可以替换为apollo自带的bridge模块？？？

VehicleActions -> 车的一些行为控制（车灯，雨刮器等）
VehicleDynamics -> 车的动态模型


## 深度相机原理

下面函数的意义是什么？
```c#
AsyncGPUReadback.Request(Camera.targetTexture, 0, TextureFormat.RGBA32)
```

从GPU中异步读取数据
https://www.twblogs.net/a/5c2ccf66bd9eee35b3a45efa/zh-cn  

compute shader
https://docs.unity3d.com/Manual/class-ComputeShader.html  
https://www.jianshu.com/p/c7478722cad2  
https://www.nigauri.co/unity%E5%AE%9E%E7%8E%B0compute-shader%E7%9A%84%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/  

渲染管线
https://zhuanlan.zhihu.com/p/35932630  


获取深度图的方法
https://www.jianshu.com/p/4e8162ed0c8d  

渲染的流程基本上都是通过渲染管道来实现的？？？得到距离是通过shader文件实现的这个好理解。
但是UpdatePointCloudJob中计算点的距离的算法到底是如何实现的？？？
LidarSensor
DepthCameraSensor
SemanticCameraSensor

Radar的mesh是专门的脚本生成的RadarMesh.cs，通过trigger触发

## shader
https://zhuanlan.zhihu.com/p/46745694  

https://zhuanlan.zhihu.com/p/32593707  
https://docs.unity3d.com/560/Documentation/Manual/SL-DepthTextures.html  

首先获取深度纹理，然后从深度纹理中获取深度信息。LidarPass.hlsl中把深度信息转换成了float4类型？？？
GetPositionInput获取当前位置，然后通过GetPrimaryCameraPosition位置减去当前位置获取到距离，那么这里的surfaceData是如何体现出来的呢？  

如何在HDRP中增加命令
https://forum.unity.com/threads/hdrp-how-to-add-command-buffer-to-camera.568870/  

## 渲染流水线
渲染流水线的工作任务在于由一个三维场景出发，生成（或者说渲染）一张二维图像。换句话说，计算机需要从一系列的顶点数据、纹理等信息出发，把这些信息最终转换成一张人眼可以看到的图像。而这个工作通常是由CPU和GPU共同完成的。
渲染分为3个阶段： 应用阶段(Application Stage)、几何阶段(Geometry Stage)、光栅化阶段(Rasterizer Stage)。  
* **应用阶段** - 我们需要准备好场景数据，例如摄像机的位置、视锥体、场景中包含了哪些模型、使用了哪些光源等等；其次，为了提高渲染性能，我们往往需要做一个粗粒度剔除（culling）工作，以把那些不可见的物体剔除出去，这样就不需要再移交给几何阶段进行处理。最后，我们需要设置好每个模型的渲染状态。这些渲染状态包括但不限于它使用的材质、纹理，Shader等。这一阶段最重要的输出是渲染所需的几何信息，即渲染图元（rendering primitives）。  
* **几何阶段** - 几何阶段用于处理所有和我们要绘制的几何相关的事情。例如，决定需要绘制的图元是什么，怎样绘制它们，在哪里绘制它们。这一阶段通常在GPU上进行。几何阶段负责和每个渲染图元打交道，进行逐顶点、逐多边形的操作。  
* **光栅化阶段** - 这一阶段将会使用上个阶段传递的数据来产生屏幕上的像素，并渲染出最终的图像。这一阶段也是在GPU上运行。光栅化的任务主要是决定每个渲染图元中的哪些像素应该被绘制在屏幕上。它需要对上一阶段得到的逐顶点数据（例如纹理坐标、顶点颜色等）进行插值，然后再进行逐像素处理。  


#### 应用阶段
渲染流水线的起点是CPU，即应用阶段。应用阶段大致可分为下面3个阶段：
1. 把数据加载到显卡中
2. 设置渲染状态
3. 调用Draw Call

#### GPU流水线
对于概念的后两个阶段，即几何阶段和光栅化阶段，开发者无法拥有绝对的控制权，其实现的载体是GPU。
顶点着色器（Vertex Shader）是完全可编程的，它通常用于实现顶点的空间变换、顶点着色等功能。
片元着色器（Fragment Shader），则是完全可编程的，它用于实现逐片元（Per-Fragment）的着色操作。最后，逐片元操作(Pre-Fragment Operations)阶段负责执行很多重要的操作，例如修改颜色、深度缓冲、进行混合等，它不是可编程的，但具有很高的可配置性。  
由这一步开始就进入了光栅化阶段。从上一个阶段输出的信息是屏幕坐标系下的顶点位置以及和它们相关的额外信息，如深度值（z坐标）、法线方向、视角方向等。光栅化阶段有两个最重要的目标：计算每个图元覆盖了哪些像素，以及为这些像素计算它们的颜色。

#### 逐片元操作
逐片元操作(Per-Fragment Operations)是OpenGL中的说法，在DirectX中，这一阶段被称为输出合并阶段（Output-Merger）。
1. 决定每个片元的可见性。这涉及了很多测试工作，例如深度测试、模板测试等。
2. 如果一个片元通过了所有的测试，就需要把这个片元的颜色值和已经存储在颜色缓冲区中的颜色进行合并，或者说是混合。


#### Unity shader学习
1. 可以学习下官方教程 https://docs.unity3d.com/Manual/SL-Reference.html  
2. 看下是否有相关的教学视频 



