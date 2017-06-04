# Overview
* 应用开发者绘制图像到屏幕上有两种方法：使用Canvas 或者OpenGL。Canvas API的硬件加速是由一个叫OpenGLRender的渲染库(libhwui.so)实现的，这个库将Canvas的操作转换成可以在GPU上执行的OpenGL操作。
* WMS作为View容器的窗口的系统服务。监管窗口的生命周期，输入和焦点事件，转屏，切换，动画，位置，转换，z-序，以及诸多其他方面。WM将所有的Window信息发送给SF，这样SF就能够使用这些数据在Display上合成surface。
* BufferQueue可以以三种不同模式操作：同步模式（blocking mode），非阻塞模式（Non-blocking mode），丢弃模式（Discard Mode）。

# Architecture
* HWC决定Layer是OVERLAY还是GLES，surfaceflinger负责GLES的layer合成，OVERLAY由HWC处理。
* Overlay planes may be less efficient than GL composition when nothing on the screen is changing. This is particularly true when the overlay contents have transparent pixels, and overlapping layers are being blended together.
* FB_TARGET layer是GLES合成输出，是一个scratch buffer。
* Overlay还有一个重要的作用：显示DRM的内容，DRM的内容不能被SurfaceFlinger或者GLES访问。
* Triple-Buffering：
![BufferQueue](https://github.com/WellsLee/development-notes/blob/master/android/graphics/BufferQueue.png)
* 用于display surface的BufferQueue一般是3块buffer，但这些buffer是按需分配。所以如果producer产生buffer数据很慢（可能将会导致在60fps的display上只能有30fps的刷新率），BufferQueue里面可能就只有两块已分配的buffer。 这对降低内存消耗有帮助。可以通过dumpsys SurfaceFlinger的数据来了解每个layer的buffers。
* 曾经一段时间渲染都是由sw完成的，今天同样可以这么做。低层的实现是由skia 图形库所提供。随着时间的推移，支持多方面用途的3D引擎的设备出现，Android采用OpenGL ES来适应。特别要注意的是view的onDraw函数中的canvas可能是硬件加速的canvas，如果这个canvas是app直接通过lockCanvas()获取的就不是硬件加速的canvas。
* Producer 第一次从BufferQueue中请求buffer时，buffer将会被分配，并初始化为0。
