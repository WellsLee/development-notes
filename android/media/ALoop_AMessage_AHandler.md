# 基本流程
1. new ALooper(): 调用gLooperRoster.unregisterStaleHandlers将mHandlers列表remove掉。
2. mLooper->start()：创建一个LooperThread线程，然后在等待消息。
3. mLooper->registerHandler()：调用gLooperRoster.registerHandler将handler添加到mHandlers列表。
4. handler创建AMessage消息并post出去，post会调用到looper->post函数，将消息添加到mEventQueue，并signal通知之前创建的LooperThread线程（线程间通信）。
5. LooperThread线程调用event.mMessage->deliver，然后调用到handler->deliverMessage，最终由handler的onMessageReceived处理消息。
