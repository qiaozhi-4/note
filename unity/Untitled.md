以下是JNI 函数结合 Unity Android 引擎的实际使用场景，解释了每个 native 函数在什么时候被 Java/Kotlin 层调用（触发时机）以及它的主要作用。



1. initJni(Landroid/content/Context;)V  
   调用时机：Activity onCreate → UnityPlayer.<init> 时  
   作用：初始化 native 引擎，把 Android Context 传给 C++ 层，做各种全局初始化（最先调的）

2. nativeDone()Z  
   调用时机：UnityPlayer.quit() 或进程即将彻底结束时  
   作用：通知 native 层可以安全释放所有资源，返回是否真的完成了清理

3. nativePause()Z  
   调用时机：Activity onPause 时  
   作用：暂停游戏主循环、音频、物理等，返回是否暂停成功

4. nativeResume()V  
   调用时机：Activity onResume 时  
   作用：恢复游戏主循环、音频、输入等

5. nativeRecreateGfxState(ILandroid/view/Surface;)V  
   调用时机：SurfaceCreated、SurfaceChanged、从后台恢复、OpenGL 上下文丢失后  
   作用：重新创建 OpenGL/EGL/Vulkan 上下文，重建渲染表面

6. nativeSendSurfaceChangedEvent()V  
   调用时机：SurfaceChanged 回调（宽高变化）时  
   作用：通知 C++ 层窗口大小变了，更新 viewport、投影矩阵等

7. nativeRender()Z  
   调用时机：每帧由 Choreographer 驱动（UnityPlayer.nativeRender()）  
   作用：执行一帧的 Update + Render，返回 false 表示渲染线程要退出

8. nativeLowMemory()V  
   调用时机：系统广播 TRIM_MEMORY_COMPLETE 或 RUNNING_CRITICAL  
   作用：通知游戏内存紧张，要求立即释放缓存（纹理、音频、AssetBundle 等）

9. nativeApplicationUnload()V  
   调用时机：UnityPlayer.unload()，通常在进程被系统杀掉前  
   作用：强制卸载所有 C++ 插件和资源，准备进程退出

10. nativeFocusChanged(Z)V  
    调用时机：Activity onWindowFocusChanged(hasFocus)  
    作用：窗口获得/失去焦点，常用于自动暂停或恢复游戏

11. nativeSetInputArea(IIII)V  
    调用时机：软键盘弹出时（Android 11+ insets）  
    作用：设置可输入区域的边距（left, top, right, bottom），避免被键盘遮挡

12. nativeSetKeyboardIsVisible(Z)V  
    调用时机：软键盘显示/隐藏时  
    作用：通知 C++ 层键盘当前是否可见（某些游戏需要暂停或隐藏 UI）

13. nativeSetInputString(Ljava/lang/String;)V  
    调用时机：输入法 commitText 时  
    作用：把输入法输入的文字直接塞给游戏（自定义输入框用）

14. nativeSetInputSelection(II)V  
    调用时机：输入法设置选区时  
    作用：设置当前输入框的光标位置和选中范围

15. nativeSoftInputClosed()V  
    调用时机：用户点“完成/回车”正常关闭软键盘  
    作用：软键盘正常关闭，游戏可以恢复正常逻辑

16. nativeSoftInputCanceled()V  
    调用时机：用户点返回键强制关闭软键盘  
    作用：软键盘被取消关闭，通常需要隐藏自定义输入框

17. nativeSoftInputLostFocus()V  
    调用时机：输入框失去焦点但键盘可能还开着  
    作用：游戏需要隐藏输入光标、收起自定义键盘等

18. nativeReportKeyboardConfigChanged()V  
    调用时机：键盘配置变化（切换语言、输入法切换等）  
    作用：通知游戏键盘布局变了，可能要重新计算按键映射

19. nativeInjectEvent(Landroid/view/InputEvent;I)Z  
    调用时机：Java 层拦截了某些输入事件后需要手动注入  
    作用：把 MotionEvent/KeyEvent 直接扔给 Unity 输入系统处理，返回是否消费

20. nativeUnitySendMessage(Ljava/lang/String;Ljava/lang/String;[B)V  
    调用时机：任何需要 Java → C# 的地方  
    作用：最经典的跨线程通信函数，把消息发到指定 GameObject 的指定方法

21. nativeIsAutorotationOn()Z  
    调用时机：系统询问是否允许自动旋转时  
    作用：返回 Unity 当前是否开启了自动旋转

22. nativeMuteMasterAudio(Z)V  
    调用时机：音量键按下或系统静音开关切换  
    作用：静音/恢复整个游戏的所有音频（Master mute）

23. nativeSetLaunchURL(Ljava/lang/String;)V  
    调用时机：DeepLink 或 scheme URL 启动 App 时  
    作用：把启动时携带的 URL 传给游戏处理

24. nativeOrientationChanged(II)V  
    调用时机：屏幕方向真正变化后（宽高交换）  
    作用：通知 C++ 层当前实际的 orientation 和 resolution（参数通常是 width, height）

25. nativeGetNoWindowMode()Z  
    调用时机：某些特殊场景询问  
    作用：返回当前是否是无 Android 窗口模式（纯 NativeActivity，常用于 VR/AR）

26. nativeHidePreservedContent()V  
    调用时机：Android 12+ 的 Picture-in-Picture、折叠屏等特殊模式  
    作用：隐藏被系统保留的内容区域（刘海、摄像头区域等）



总结最常用的几个（几乎所有 Unity Android 项目都会用到）：
- initJni → 初始化
- nativeResume / nativePause → 前后台切换
- nativeRecreateGfxState + nativeRender → 渲染核心
- nativeUnitySendMessage → Java 呼叫 C# 最重要通道
- nativeLowMemory → 防 OOM 必接
- 所有 nativeSetInput* / nativeSoftInput* → 自己做输入法必须接

这些函数基本覆盖了 Unity 在 Android 平台上与 Java 层交互的所有生命周期和事件。希望这个表格对你逆向、魔改或写 Unity Android 插件有帮助！