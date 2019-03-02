# Audio pre and post processing

```
Factory_HandleAudioPostCaptureBuffer
#if ENABLE_AUDIO_PROCESSING
           _factory.OnAudioPostCaptureInitialize += Factory_HandleAudioPostCaptureInit;
           _factory.OnAudioPostCaptureRuntimeSetting += Factory_HandleAudioPostCaptureRuntimeSetting;
           _factory.OnAudioPostCapture += Factory_HandleAudioPostCaptureBuffer;

           _factory.OnAudioPreRenderInitialize += Factory_HandleAudioPreRenderInit;
           _factory.OnAudioPreRenderRuntimeSetting += Factory_HandleAudioPreRenderRuntimeSetting;
           _factory.OnAudioPreRender += Factory_HandleAudioPreRenderBuffer;
#endif

#if ENABLE_AUDIO_PROCESSING
           _factory.OnAudioPostCaptureInitialize += Factory_HandleAudioPostCaptureInit;
           _factory.OnAudioPostCaptureRuntimeSetting += Factory_HandleAudioPostCaptureRuntimeSetting;
           _factory.OnAudioPostCapture += Factory_HandleAudioPostCaptureBuffer;

           _factory.OnAudioPreRenderInitialize += Factory_HandleAudioPreRenderInit;
           _factory.OnAudioPreRenderRuntimeSetting += Factory_HandleAudioPreRenderRuntimeSetting;
           _factory.OnAudioPreRender += Factory_HandleAudioPreRenderBuffer;
#endif

Conductor.cs
```