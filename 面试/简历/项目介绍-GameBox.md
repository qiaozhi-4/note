# 项目简历：GameBox 

## 项目介绍

**GameBox** 是一款基于 **Xposed/LSPosed 框架** 的 Android 模块，专注于为 Android 应用（特别是 Unity 游戏）提供运行时注入、功能增强及自定义浮窗（ImGui）功能。该项目融合了 Java/Kotlin 层的高级配置能力与 C++ 原生层的高性能渲染与 Hook 能力，旨在实现对目标进程的深度定制与交互。

## 功能介绍

*   **进程注入与管理**：利用 Xposed 框架自动识别目标进程，实现模块代码的动态注入。支持自定义延迟注入，确保兼容性。
*   **Native 级 Hook**：在原生层 Hook 图形渲染管线（如 `eglSwapBuffers`），实现自定义内容的屏幕绘制。
*   **ImGui 浮窗支持**：集成 ImGui 框架，在游戏/应用上层绘制高性能、可交互的调试/功能菜单。
*   **Unity 游戏适配**：内置针对 Unity 引擎（IL2CPP 运行时）的识别机制，自动搜索 `libunity.so` 和 `libil2cpp.so` 基址，方便后续的游戏逻辑修改。
*   **混合渲染模式**：支持原生 ImGui 渲染与 Java/Kotlin 层（DEX）渲染模式的切换。

## 系统架构

项目采用 **Java/Kotlin + C++ 混合架构**，分为应用层、桥接层和原生核心层：

1.  **应用层 (Java/Kotlin)**：负责用户界面（`MainActivity`）、配置管理（`AppConfig`）以及 Xposed 入口逻辑（`XposedHooks`）。
2.  **桥接层 (JNI)**：通过 `NativeBridge` 类与 JNI 接口实现 Java 层与 C++ 层的数据交互与控制指令传递。
3.  **原生核心层 (C++)**：包含核心 Hook 逻辑、ImGui 渲染引擎初始化、Unity 运行时分析及具体的功能实现。

## 架构设计

*   **模块化设计**：核心功能被封装在 `libxmg_core.so` 中，通过动态加载实现热插拔与更新。
*   **初始化链式调用**：采用分步初始化策略（InitJNI -> InitDex -> InitImGui -> InitUnity），确保各依赖项（如 JNI 环境、EGL 上下文、游戏引擎符号）按序就绪。
*   **渲染劫持**：通过 Hook 底层图形 API（EGL）获取渲染时机，插入自定义绘制逻辑，而不影响原应用的渲染流程。

## 技术实现方式

*   **语言栈**：Kotlin (Android 业务), C++17/20 (核心逻辑), CMake (构建系统)。
*   **核心依赖**：
    *   **Xposed API**：用于 Zygote 注入与包名过滤。
    *   **Dobby**：用于原生函数的 Hook 操作（如 `eglSwapBuffers`）。
    *   **ImGui**：用于构建原生层的即时模式图形界面。
    *   **KittyMemory / ElfScanner**：用于内存扫描与 ELF 符号解析。

## 技术实现细节

1.  **注入流程**：`XposedHooks` 监听 `handleLoadPackage`，匹配目标包名后，读取配置并加载 `libxmg_core.so`。
2.  **ImGui 集成**：
    *   使用 `DobbyHook` 劫持 `eglSwapBuffers`。
    *   在原 `eglSwapBuffers` 调用前，初始化 ImGui 上下文（`SetupImGui`）并执行 `internalDrawMenu()` 进行界面绘制。
    *   支持动态获取屏幕宽高与触摸事件转发。
3.  **Unity 适配**：循环检测 `libunity.so` 和 `libil2cpp.so` 是否加载，一旦定位成功，通过内存偏移计算获取关键函数地址（虽具体游戏逻辑未展示，但基础已搭建）。
4.  **资源加载**：支持从内存加载字体（TTF）与图片纹理，避免文件 I/O 对游戏性能的影响。

## 数据流

1.  **配置流**：UI 设置 -> SharedPreferences/Config文件 -> Xposed 注入读取 -> JNI 传递 -> C++ GlobalData。
2.  **渲染流**：游戏循环 -> EGL SwapBuffers -> (Hook) -> ImGui Render -> OpenGL Draw -> Original Audit。
3.  **输入流**：Native Input Event -> JNI/Native Filter -> ImGui IO -> 菜单响应。

## 接口设计

*   **JNI 接口 (`NativeBridge`)**：
    *   `initCore()`: 初始化核心功能。
    *   `loadCommonAndRun(commonPath, modPath)`: 加载通用配置与模组。
    *   `dexDraw()`: 触发 Java 层绘制逻辑（可选）。
    *   `onSurfaceCreated/Changed/DrawFrame`: 供 Java 层 SurfaceView 回调原生渲染（针对非 Hook 模式）。

## 性能设计

*   **零拷贝渲染**：ImGui 直接在当前 OpenGL 上下文中绘制，无额外显存拷贝。
*   **按需扫描**：Unity 符号查找采用轮询+休眠机制（`sleep(1)`），减少对 CPU 的占用。
*   **条件渲染**：仅在 EGL 交换缓冲区或 Surface 刷新时绘制，与宿主帧率同步，避免过度渲染。

## 安全考虑

*   **动态加载**：核心逻辑编译为 SO 文件，通过 `System.load` 动态载入，增加静态分析难度。
*   **权限控制**：利用 SELinuxHelper 检查文件访问权限，确保模块在受限环境中的稳定性。

## 维护和部署

*   **构建部署**：基于 Gradle 与 CMake 的标准化构建流程，输出 APK 即模块安装包。
*   **调试支持**：内置完善的 Logcat 封装（`LOGD`），便于定位注入与 Hook 阶段的问题。

## 项目周期和成果

*   **项目状态**：核心框架已搭建完成（Hook 框架、ImGui 渲染、Unity 识别均已实现），处于功能扩展阶段。
*   **成果**：成功实现了一套通用的 Android 游戏/应用 辅助框架，具备极高的扩展性，可快速适配不同 Unity 游戏并开发对应的修改器功能。