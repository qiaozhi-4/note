# frida安装

## pc端

```sh
# 首先需要python环境，安装python
然后cmd执行下面命令
# 安装 frida库 
pip install frida
 
# 安装frida-tools工具
pip install frida-tools
 
#目前测试最好用的版本，坑少
#frida              12.11.18
#frida-tools        5.3.0
#frida-server       12.8.10

#frida              12.7.26
#frida-tools        5.3.0
#frida-server       12.8.10
```

-----------------------------





## 移动端

### 概括

Android ：

先root， 再配置 frida-server （下载并push到/data/tmp/local, 
以root权限执行， 下载地址 https://github.com/frida/frida/releases）


iOS ：

先 jailbreak，再从cydia下载frida插件。
果没法root或越狱，可简单的修改应用，在应用初始化时，主动加载frida组件，相当于进程内部hook。

-----------------



### 使用案例

**注意!!! 夜神模拟器的adb版本和adb工具的版本必须一致才能检测到** 

#### 连接手机

```shell
adb devices
#安卓10及一下需要 USB 连接才行，以上同一WiFi就行
```

-------------



#### 查看手机型号

```shell
adb shell getprop ro.product.cpu.abi
#GitHub下载对应型号的版本,并解压,比如: frida-server-16.0.11-android-x86_64

#我的运行结果
#C:\Users\qz>adb shell getprop ro.product.cpu.abi
#x86_64
```

--------------------



#### 把frida-server 放到 安卓的/data/local/tmp/目录

**可以使用mt管理器移动进去**

```shell
adb push frida-server /data/local/tmp/
 adb -s 192.168.0.100:38363 push .\dist\main.js /storage/emulated/0/.AAAAAAjshook
```

-------------------------------



#### 运行Android 设备中的 frida-server 

执行完毕后为运行状态。

保留此窗口 shell，以保证服务运行，关闭该shell 或者停止ctrl+c 则服务关闭。接下来的操作可另起shell 或该步骤命令另起 shell 执行。

**可以直接mt改权限**

```shell
# 进入 手机命令
adb shell
 
# 超级管理
su
 
# 进入 frida-server 目录
cd /data/local/tmp
 
# 修改 文件 权限
chmod 755 frida-server
 
# 运行文件
./frida-server
 
# 检查是否运行成功
netstat -tulnp | grep frida-server
netstat -tulnp | grep 27042
```

-----------------------



#### 进行端口转发监听

```shell
adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043
      
#要关闭通过adb建立的端口转发通信，可以使用以下命令：
adb forward --remove tcp:27042
```

----------------------



#### 检查是否成功 

```shell
# 命令成功输出进程列表
frida-ps -U
frida-ps -R 
# 会显示包名,但是只会有用户应用
frida-ps -U -a
 
# 进行连接，会直接启动这个包名的app,如果该app在运行,将被重启
#frida -U -f 包名 
frida -U -f com.r2games.myhero.aligames
```

----------------------





# Frida两种启动方式的区别

1. `span` 模式：frida 重新打开一个进程
   `frida -U -f 包名 -l js路径 --no-pause`
2. `attch` 模式: 附加在当前打开的进程
   `frida -U -l js路径 --no-pause`
   区别就是一个带 `-f` 一个不带

### 命令行参数详解

```vhdl
  --version             显示版本号
  -h, --help            查看帮助
  -D ID, --device=ID    用给定的ID连接到设备
  -U, --usb             连接 USB 设备
  -R, --remote          连接远程设备
  -H HOST, --host=HOST  连接远程的设备 地址
  -f FILE, --file=FILE  spawn FILE 模式
  -F, --attach-frontmost
                        attach to frontmost application 附加到最前端的应用程序
  -n NAME, --attach-name=NAME
                        attach to NAME 附加的名称
  -p PID, --attach-pid=PID
                        attach to PID 附加的进程ID
  --stdio=inherit|pipe  stdio behavior when spawning (defaults to “inherit”)
  --runtime=duk|v8      script runtime to use (defaults to “duk”) 指定js解释器 duk 或者 v8
  --debug               enable the Node.js compatible script debugger 
  -l SCRIPT, --load=SCRIPT 加载脚本文件
                        load SCRIPT
  -P PARAMETERS_JSON, --parameters=PARAMETERS_JSON
                        Parameters as JSON, same as Gadget
  -C CMODULE, --cmodule=CMODULE
                        load CMODULE
  -c CODESHARE_URI, --codeshare=CODESHARE_URI
                        load CODESHARE_URI
  -e CODE, --eval=CODE  evaluate CODE
  -q                    quiet mode (no prompt) and quit after -l and -e
  --no-pause            automatically start main thread after startup 启动后自动启动主线程
  -o LOGFILE, --output=LOGFILE 日志输出文件
                        output to log file
  --exit-on-error       exit with code 1 after encountering any exception in
                        the SCRIPT 异常退出脚本
```





#  常用的 Frida 命令行工具 

以下是一些常用的 Frida 命令行工具及其作用：

1. `frida-ps`: 列出设备上所有正在运行的进程。
2. `frida -U -n <app_name>`: 启动指定应用程序并附加到该进程。
3. `frida -U -f <app_id>`: 启动指定应用程序并附加到该进程。
4. `frida -U -p <process_id>`: 附加到指定进程。
5. `frida-trace`: 跟踪指定模块、方法或 API 的调用情况。
6. `frida-discover`: 自动发现可供攻击的漏洞。
7. `frida-kill`: 终止与指定进程或所有进程相关联的 Frida 会话。
8. `frida-repl`: 启动一个交互式 Python 或 JavaScript 环境，用于手动控制 Frida 会话。
9. `frida-ls-devices`: 列出当前连接的所有设备。
10. `frida-inject`: 在指定进程中注入 Frida 脚本。

以上命令只是 Frida 命令行工具的一小部分，还有许多其他有用的命令可以根据需要使用。您可以通过在命令行中输入 `frida --help` 来查看所有可用选项和命令。

-----------------













# 电脑adb连接手机

## 安卓10及其以下

-------------------------------

连接数据线

```shell
#查看是否能找到手机设备
adb devices
```





## 安卓11及其以上

-------------------------------------

```shell
#输入ip以及端口
adb pair 192.168.0.100:39753
#输入安卓WiFi调试的配对码
Enter pairing code: 460956
#配对成功之后直接连接
adb connect 192.168.0.100
```









## push脚本

```sh
adb -s 192.168.0.100 push ./dist/main.js /storage/emulated/0/.AAAAAAjshook
 adb -s 192.168.0.107 push .\.模板\偏移模版2.0.0.lua /storage/emulated/0/Download/GG/GG脚本/.模板/
```

--------------







# cmd

```sh
#启动脚本自动构建
npm run watch

#连接手机
adb connect 192.168.0.101:43451 
#连接手机2
adb pair 192.168.0.100:39753
#输入安卓WiFi调试的配对码
Enter pairing code: 460956

#启动游戏
#我的勇者九游
frida -U -f com.r2games.myhero.aligames -l .\dist\main.js
#见习猎魔团
frida -U -f com.jianxiliemotuan.guanbao -l .\dist\main.js

#push文件
adb -s 192.168.0.101 push ./dist/main.js /storage/emulated/0/.AAAAAAjshook
```











# JS方式使用

```shell
frida -U -f  -l 
```

这里的 `-U` 表示使用 USB 连接进行设备通信，`-f` 表示后面跟着要启动应用程序的包名或可执行文件路径，`-l` 表示后面跟着一个 JavaScript 文件，该文件将在 Frida 会话中加载并作为脚本运行。

综合起来，这个命令的含义是：通过 USB 连接使用 Frida 启动指定应用程序，并将指定的 JavaScript 文件加载到 Frida 会话中。

## frida使用

```sh
 #frida -U -l js名称 -f 目标包名
 frida -U -l exploit.js -f com.package.name
 #我的勇者九游版本
 frida -U -l .\dist\main.js -f com.r2games.myhero.aligames
```

其中js脚本写法

```js
setImmediate(function() {
    console.log("[*] Starting script");

    Java.perform(function() {
      myClass = Java.use("com.r2games.myhero.aligames.xxx某Activity");
      myClass.implementation = function(v) {
         // do sth.
      }
    })
})
```

--------------------



## frida-bridge使用

src/main.js 是源码,写完之后运行构建命令,会构建到 dist/main.js

```shell
#在frida-brige目录下执行cmd命令行,会自动安装需要的依赖
npm install

#构建编写的脚本
npm run watch

#更新
npm update
```

```shell
#如果不知道app名称,可以使用 frida-ps -U -a 
#frida -U app名称 -l 脚本路径或者名称
frida -U Gadget -l main.js
frida -U 我的勇者 -l .\dist\main.js

frida -U -f com.r2games.myhero.aligames -l .\dist\main.js
#模拟器
frida -U -f com.r2games.myhero.aligames -l .\dist\main.js --realm=emulated
```

---------------------









# frida-bridge语法

#### 获取模块

```js
Il2Cpp.Domain.assembly("模块名称")
```

--------------------





#### push diste文件下的main

```shell
 adb -s 192.168.0.100:38363 push .\dist\main.js /storage/emulated/0/.AAAAAAjshook
```







#### 获取类的class对象

```js
//先获取模块
Il2Cpp.Domain.assembly("模块名称").image
	//先获取类的class
    .class("类全路径")
Il2Cpp.Domain.assembly("模块名称").image.class("类全路径");
Il2Cpp.Domain.assembly("UnityEngine.CoreModule").image.class("UnityEngine.Vector3");
```

------------------------



#### 获取类的实例对象

```js
//先获取模块
Il2Cpp.Domain.assembly("模块名称").image
	//先获取类的class
    .class("类全路径")
	//创建对象
    .new();
Il2Cpp.Domain.assembly("模块名称").image.class("类全路径").new();
```

-------------





#### 创建string,int32等系统类

```js

```

---------------------







#### 修改已有对象的属性

```js
//对象.field('属性名称').value = 0.0;
pos.field('yyyyy').value = 0.0;
```

----------------





#### 创建枚举

```js
Il2Cpp.Domain.assembly("模块名称").image.class("类全路径").field('枚举名称').value
//例子
myAssembly.class("Data.TaskState").field('AlreadyReceived').value
```

------------------------------





#### 追踪可疑方法

```js
Il2Cpp.trace()
	//追踪哪个模块
    .assemblies(Il2Cpp.Domain.assembly('Assembly-CSharp'))
	//过滤类出我需要的包路径,不包括类名
	// .filterClasses(klass => RegExp(/msg/).test(klass.namespace))
	//过滤类出我需要的参数名
	// .filterParameters(param => RegExp(/msg/).test(param.name))
	//过滤类出我需要的方法名
    .filterMethods(method => RegExp(/msg/).test(method.name) && ! RegExp(/msg/).test(method.name))
    .and()
	//详细打印
    .attach("detailed");
```

------------------



#### hook方法

###### 无参数无返回值

```js
//获取libil2cpp.so
const mylib = Il2Cpp.Domain.assembly("Assembly-CSharp").image;
//"Battle.BattleManager": 类的全路径	
mylib.class("Battle.BattleManager")
	//"ResetHeroPosition":方法名
    .method("ResetHeroPosition").implementation = function (){
    			//在方法执行前干什么
                this.method("ResetHeroPosition").invoke(pos);
    			//方法执行后干什么
            };
```

-----------------



###### 有参数有返回值

```js
const mylib = Il2Cpp.Domain.assembly("Assembly-CSharp").image;
mylib.class("Battle.BattleManager")
    .method("ResetHeroPosition").implementation = function (pos){
    			//在方法执行前干什么
    			//修改参数的值,这是一个对象,如果是  Int32 类似的类型直接改就行
                pos.field('x').value = 200.0;
                let r = this.method("ResetHeroPosition").invoke(pos);
    			//方法执行后干什么
    			//有返回值则返回,没有则不写
    			return r
            };
```

--------------------







#### list

##### 创建list

```js
Il2Cpp.Image.corlib.class('System.Collections.Generic.List`1').inflate(Il2Cpp.Image.corlib.class("指定泛型")).new();
Il2Cpp.Image.corlib.class('System.Collections.Generic.List`1').inflate(Il2Cpp.Image.corlib.class("System.UInt32")).new();
```

------------------------------



##### 遍历list

```js
let list = this.method("get_TalentDataList").invoke()
console.log("列表:",list);
for (let index = 0; index < list.method("get_Count").invoke(); index++) {
    const element = list.method("get_Item").invoke(index);
    console.log("\t 项目:",element);
}
console.log("总数:",list.method("get_Count").invoke());
```

------------------



##### list常用方法

```js
list.method("get_Count").invoke()//获取计数
list.method("get_Item").invoke(index)//根据下标获取元素
list.method("Add").invoke(value)//添加
list.method("Clear").invoke();//清除全部
```

--------------------





#### 字典

##### 遍历字典

```js
let Dic = taskData.field('mTaskCfgTypeList').value;
let Enumerator = Dic.method("System.Collections.IDictionary.GetEnumerator").invoke();
while (Enumerator.method("MoveNext").invoke()) {
    let itme = Enumerator.method("get_Current").invoke();
    let key = itme.field('key').value;
    let value = Dic.method("get_Item").invoke(key);
    console.log('key:',key,'\tval:',value);
}
console.log('总数',Dic.field('count').value);
```

-------------------







#### 数组创建

```js
Il2Cpp.array(类,长度)
Il2Cpp.array(类,[数据1,数据2])
Il2Cpp.array(Il2Cpp.corlib.class("System.Object"), [])
```







#### 创建对指定值的引用

```js
Il2Cpp.perform(() => {
  const TryParse = Il2Cpp.Image.corlib.class("System.Boolean").method("TryParse");

  const value = Il2Cpp.Reference.to(false);

  console.log(value); // ->false
    
  TryParse.invoke(Il2Cpp.String.from("TrUe"), value);
  console.log(value); // ->true
});
```

-------------------







#### 延时执行

```js
Il2Cpp.currentThread?.schedule(() => {
                console.log('定时任务执行中 ');
              }, 2000);
```

--------------------------------











# 游戏包名

```shell
#我的勇者
frida -U -f com.r2games.myhero.aligames -l .\dist\main.js

#妙奇星球
frida -U -f com.taolu.freakplanet -l .\dist\main.js

```























# frida中js基本语法

## 判断模块是否执行

```js
Module.ensureInitialized(name)
确保已运行指定模块的初始化程序。这在早期检测期间很重要，即代码在进程生命周期的早期运行，以便能够安全地与 API 交互。一个这样的用例是与给定模块提供的ObjC类交互。
```

-----------------------------





## 获取地址

#### 获取模块地址

```js
//获取 libil2cpp.so 的首地址
var address = Module.findBaseAddress('libil2cpp.so')
```

---------------------------------



#### 获取函数地址

```js
//获取在 libil2cpp.so 中 func 函数的地址
var targetFunc = Module.findExportByName("libil2cpp.so", "func");

//如果知道 libil2cpp.so 的首地址到方法的偏移量的话,可以先获取 libil2cpp.so 的地址在加上偏移量
var offset = '0x013eeda0' //偏移量
var address = Module.findBaseAddress('libil2cpp.so')
var targetFunc = address.add(offset)
```

-----------------------







## hook native  函数

```js
// 找到要拦截的函数地址
var targetFunc = Module.findExportByName("libfoo.so", "foo");

// 注册一个拦截器
Interceptor.attach(targetFunc, {
    onEnter: function(args) {
        // 打印函数参数
        console.log("foo(" + args[0] + ", " + args[1] + ")");

        // 修改参数
        args[0] = 0;
        args[1] = ptr("0x12345678");
    },
    onLeave: function(retval) {
        // 打印函数返回值
        console.log("foo => " + retval);
    }
});
```

------------------------------



## native 主动调用

```js
var friendlyFunctionName = new NativeFunction(friendlyFunctionPtr, 'void', ['pointer', 'pointer']);
var returnValue = Memory.alloc(sizeOfLargeObject);
friendlyFunctionName(returnValue, param1);
```

-----------------------------











# gpt回答

####  hook libil2cpp.so 中的函数 

要在Android游戏中使用Frida钩取`libil2cpp.so`库中的函数，您需要进行以下步骤：

1. 安装Frida服务器和Frida客户端（Frida-Server和Frida-Client）到您的Android设备中。您可以从Frida官方网站上下载最新版本的Frida软件包。

2. 启动Frida服务器，在终端或命令行中输入以下命令：

   ```
   $ frida-server -D
   ```

   如果一切正常，您将会看到类似下面的输出：

   ```
   * daemon not running. starting it now on port 5037 *
   * daemon started successfully *
   ```

3. 连接Frida客户端到Frida服务器，输入以下命令：

   ```
   $ frida-ps -U
   ```

   如果您的Android设备已经连接到电脑并打开了USB调试，您将会看到一个列表显示所有当前正在运行的应用程序和进程。找到您想要钩取的游戏进程，并记录它的进程ID（PID）。

4. 创建一个Frida脚本文件，该文件将包含您要钩取的功能代码。您可以使用JavaScript编写Frida脚本。

   在脚本中，您需要使用`Module.load()`函数加载`libil2cpp.so`库，并使用`Interceptor.attach()`函数来拦截您感兴趣的函数。

   下面是一个示例脚本，展示如何在Unity游戏中拦截`UnityEngine.Debug.Log()`函数：

   ```javascript
   // Load the libil2cpp.so library
   var libil2cppModule = Module.load('/data/app/com.example.game/lib/arm64/libil2cpp.so');

   // Find the UnityEngine.Debug.Log() function
   var debugLogFuncAddr = libil2cppModule.findExportByName('UnityEngine.Debug.Log');

   // Attach an interceptor to the function
   Interceptor.attach(debugLogFuncAddr, {
     onEnter: function(args) {
       console.log('[*] UnityEngine.Debug.Log() called with message: ' + args[0].readUtf8String());
     }
   });
   ```

5. 启动Frida客户端，使用`frida`命令加上要运行的脚本文件路径和游戏进程ID来启动脚本。

   ```
   $ frida -U -l /path/to/script.js -p <pid>
   ```

   如果一切正常，您将会看到类似下面的输出：

   ```
   [Device::com.example.game]-> [*] UnityEngine.Debug.Log() called with message: Hello, world!
   ```

这就是使用Frida钩取`libil2cpp.so`库中函数的基本步骤。请注意，具体的实现可能因不同的游戏和场景而异。





####  主动调用libil2cpp.so 中的函数 

要在Android游戏中使用Frida主动调用`libil2cpp.so`库中的函数，您需要进行以下步骤：

1. 安装Frida服务器和Frida客户端（Frida-Server和Frida-Client）到您的Android设备中。您可以从Frida官方网站上下载最新版本的Frida软件包。

2. 启动Frida服务器，在终端或命令行中输入以下命令：

   ```
   $ frida-server -D
   ```

   如果一切正常，您将会看到类似下面的输出：

   ```
   * daemon not running. starting it now on port 5037 *
   * daemon started successfully *
   ```

3. 连接Frida客户端到Frida服务器，输入以下命令：

   ```
   $ frida-ps -U
   ```

   如果您的Android设备已经连接到电脑并打开了USB调试，您将会看到一个列表显示所有当前正在运行的应用程序和进程。找到您想要钩取的游戏进程，并记录它的进程ID（PID）。

4. 创建一个Frida脚本文件，该文件将包含您要调用的函数代码。您可以使用JavaScript编写Frida脚本。

   在脚本中，您需要使用`Module.load()`函数加载`libil2cpp.so`库，并使用`NativeFunction()`函数来获取要调用的函数的指针。

   下面是一个示例脚本，展示如何调用`UnityEngine.Debug.Log()`函数：

   ```javascript
   // Load the libil2cpp.so library
   var libil2cppModule = Module.load('/data/app/com.example.game/lib/arm64/libil2cpp.so');

   // Get the address of the UnityEngine.Debug.Log() function
   var debugLogFuncAddr = libil2cppModule.findExportByName('UnityEngine.Debug.Log');

   // Create a NativeFunction object for the function
   var debugLogFunc = new NativeFunction(debugLogFuncAddr, 'void', ['pointer']);

   // Call the function with the message you want to log
   debugLogFunc(Memory.allocUtf8String('Hello, world!'));
   ```

5. 启动Frida客户端，使用`frida`命令加上要运行的脚本文件路径和游戏进程ID来启动脚本。

   ```
   $ frida -U -l /path/to/script.js -p <pid>
   ```

   如果一切正常，您将会看到类似下面的输出：

   ```
   [Device::com.example.game]-> Hello, world!
   ```

这就是使用Frida主动调用`libil2cpp.so`库中函数的基本步骤。请注意，具体的实现可能因不同的游戏和场景而异。



































