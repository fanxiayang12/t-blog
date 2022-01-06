1. android手机重启之后，adbd服务自动启动；
2. 定制编译android系统时，可以设置永久系统属性；一旦设置，后期即便是root将无法永久修改；
3. 但可以通过adb setprop临时修改属性，重启之后会失效；

## 非root
    必须先连接调试线，后期不能重启

```shell
adb tcpip 5555
adb shell setprop service.adb.tcp.port 5555
adb shell stop adbd
adb shell start adbd
```

## 已root
可以用非root，且可以直接在手机上安装shell软件，

### 如果设置过，直接使用
```shell
getprop service.adb.tcp.port
```

### 如果没设置过，设置
```shell
setprop service.adb.tcp.port 5555
stop adbd
start adbd
```