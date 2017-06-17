# 流程
* ACodec ---> BnOMX ---> OMXNodeInstance ---> handle(看作是厂家OMX的句柄)
* handle ---> OnEvent(OMXNodeInstance) ---> OnEvent(BnOMX) ---> post(CallbackDispatcher) ---> onMessage(OMXNodeInstance) ---> onMessage(OMXCodecObserver) ---> on_message(ACodec)
