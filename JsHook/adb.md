####  adb devices  连接手机

安卓10及一下需要 USB 连接才行，以上同一WiFi就行

-------



#### 查看手机型号
adb shell getprop ro.product.cpu.abi

---------------



#### 杀死所有adb服务

adb kill-server 

-------------------



#### 重启adb服务

 adb start-server 

----------------------



#### 查看adb占用的端口

 adb nodaemon server 

-----------------



####   查看占用该端口的pid 

netstat -ano | findstr "端口号" 

示例:netstat -ano | findstr "5037" 





#### adb安卓11及其以上wifi连接

```sh
#手机打开WiFi调试
adb pair 192.168.0.101:37333
#输入配对码
#配对成功之后连接
adb connect 192.168.0.101:37333
```





























#### 显示连接设备程序

##### frida-ps -U  

显示连接设备的所有进程

##### frida-ps -Ua 

显示连接设备的正在运行应用程序

##### frida-ps -Uai 

显示连接设备所有已经安装的应用程序

