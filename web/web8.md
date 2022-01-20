## CSRF-跨站请求伪造：
通过构造恶意url等方式，伪造目标的请求，当对方触发的时候以对方身份对服务器发起请求，往往是创建管理员或修改密码一类的操作
比如
`<script src="http://www.xxx.com/adduser.php?username=xxx&password=xxx"></script>`

防御：制定同源策略（检测来源是否同一域名），使用token或referer进行验证

`csrf+xss`配合可以极大提高攻击性



## SSRF-服务端请求伪造
目标：从外网无法访问的内部系统
原因：由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制

漏洞挖掘：
```
1.从web功能上寻找：
分享：通过url地址分享网页内容
转码服务：通过url地址把原地址的网页内容调优使其适合手机屏幕浏览
在线翻译：通过url地址翻译对应文本的内容，提供此功能的国内公司有百度/有道等
图片加载与下载：通过url地址加载或下载图片
图片/文章收藏功能
未公开的api实现以及其他调用url的功能

2.从url关键字中寻找：
share
wap
url
link
src
source
target
u
3g
display
sourceURI
imageURL
domain
```

漏洞验证：`burpsuite抓包/右键打开图片`

利用绕过：
```
http://A.com@10.10.10.10
ip地址转换成进制：115.239.210.26=16373751032
```

关于探测：`如果目标是企业类，可以尝试探测邮件服务端口（如25端口）`

各个协议调用探针：`http，file，dict，ftp，gopher等`
漏洞攻击：`端口扫描，指纹识别，漏洞利用，内网探针等`
利用示例：
```
http://192.168.64.144/phpmyadmin
file:///D:/www.txt
dict://192.168.64.144:3306/info
ftp://192.168.64.144:21
```
