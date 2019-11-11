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



