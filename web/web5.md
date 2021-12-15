# `upload-labs`通关简单手册

```
P1：前端js校验，firefox进行config禁用js即可
P2：MIME校验，bp抓包改MIME为图片即可
P3：后端黑名单校验，上传php5等即可（需要服务器配置解析）
P4：后端黑名单校验，上传.htaccess修改解析即可
P5：黑名单校验，大小写绕过，但可能只有Windows能解析
P6：黑名单校验，末尾空格绕过，但可能需要Windows解析（Windows会忽略末尾空格）
P7：黑名单校验，没有对后缀名进行去点处理，利用Windows特性上传phpinfo.php.即可
P8：黑名单校验，利用Windows特性上传phpinfo.php::$DATA即可
P9：黑名单校验，利用Windows特性，点 空格 点 绕过（phpinfo.php. .）
P10：去除php字符，上传info.pPHPhp进行双写绕过
P11：路径可控，%00截断，需要php版本和配置允许
P12：POST %00截断，需要对%00进行URL decode
P13：上传图片马（copy xxx.jpg /b + phpinfo.php /a info.jpg）然后利用文件包含漏洞进行解析
P14：getimagesize()检查，上传图片马绕过
P15：exif_imagetype()检查，上传图片马绕过

以下最后五关答案来自网络

P16：图片二次渲染，上传gif文件进行比对，寻找二次渲染之后不会改变的部分，将php代码写入该区域，上传绕过
P17：条件竞争，代码审计得到文件上传过程，在操作文件前访问webshell
P18：后缀白名单，条件竞争，上传图片马
P19：CVE-2015-2348 在savename处进行%00截断（POST需要编码），或构造upload-19.php/.绕过
P20：（据说是CTF题目）代码审计，比较复杂。。需要根据网上教程做（这里略过）
```
