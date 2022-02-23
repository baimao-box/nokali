# 反序列化安全

## Java反序列化：

### 利用：Payload生成器（ysoerial），自定义检测工具或脚本

### 检测：

### 黑盒：数据格式点（HTTP请求中的参数，自定义协议，RMI协议，子主题5（？）等），特定扫描：扫描，然后利用

### 白盒：函数点，组件点（参考ysoserial库），代码点（RCE执行，数据认证等）

### Java中对于序列化的API实现：

序列化：`ObjectOutputStream类-->writeObject()`

反序列化：`ObjectInputStream类-->readObject()`

### JavaWeb靶场：webgoat（网络山羊（？））

### Java Jar图形化反编译工具：`jd-gui.exe`

### 关于识别Java序列化的特征：

一段数据以`rO0AB`开头，基本上是Java序列化base64编码的数据

以aced开头，则是这段java序列化的16进制



### Java反序列化解密工具

SerializationDumper
