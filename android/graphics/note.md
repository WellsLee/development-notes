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
* 如果想渲染带纹理的多边形，可以使用GLES调用；如果像将你的渲染推送到屏幕上，必须使用EGL调用。在使用GLES做事情之前，必须创建一个GL上下文。在EGL中，也就是创建一个EGLContext，和一个EGLSurface。
* 对外开放的Surface 类是用java 实现的。其在C/C++中的实现是ANativeWindow。
* SurfaceView的厉害之处时：你可以在一个单独的线程或进程渲染这块surface，与App ui 相关的渲染操作无关，这块buffer会直接传递给SF。你可以用任何能够feed给BufferQueue机制来更新这块surface。你可以：使用Surface提供的canvas 函数，附到EGLSurface上，通过GLES绘制，可以配置一个MediaCodec 的视频解码器对其进行写操作。
* Source Crop表示SF将要显示的这个Surface其对应的buffer上的区域（相对Surface的buffer而言的区域）。frame表示这个矩形在display上的显示区域。
* 如果通过其他手段GLES等渲染这块surface时，你可以通过SurfaceHolder#setFixedSize() 来设置surface的大小。比如，你可以配置游戏总是在1280x720上渲染，将会有效减少像素的数量。
* GLSurfaceView 提供一些帮助类来帮助管理EGL 上下文，线程间通信，与Activity生命周期的交互。
* SurfaceView is the combination of a Surface and a View, SurfaceTexture is the combination of a Surface and a GLES texture。
* 当创建一个SurfaceTextuer时，将会创建一个以APP作为Consumer的BufferQueue。当新的buffer 被producer归还到队列时，通过onFrameAvailable() app将会被通知到。APP可以调用updateTexture(),来释放上一次持有的buffer，从队列acquire新的buffer，并且调用一些EGL 调用使这个buffer变成一个GLES可用的外部Texture。
* 如果你深入看这个类SurfaceTexture文档，你可能看到一对奇怪的调用。一个是获取时间戳，另外一个是转换矩阵，他们的值是通过上一次updateTexImage调用设置的。它证明了BufferQueue传递不止一个buffer的handle 给Consumer。每个Buffer都携带一个时间戳和转换矩阵。
