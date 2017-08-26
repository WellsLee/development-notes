# 基本概念
* AudioTrack有两种数据加载模式：MODE_STREAM和MODE_STATIC，MODE_STREAM延时大，MODE_STATIC是先拷贝再播放。如果采用STATIC模式，须先调用write写数据，然后再调用play。
* 调用Native对象的流程：
1）new一个AudioTrack，使用无参的构造函数。
2）调用set函数，把Java层的参数传进去，另外还设置了一个audiocallback回调函数。
3）调用了AudioTrack的start函数。
4）调用AudioTrack的write函数。
5）工作完毕后，调用stop。
* TrackHandle和RecordHandle是基于Binder通信的，它作为Proxy，用于接收请求并派发给对应的Track和RecordTrack。
