## RCE

### RCE-代码执行：危害：执行脚本代码

脚本：`php，java，python等`

产生：
```
Web源码：thinkphp，eyoucms，wordpress
中间件平台：Tomcat，Apache Struts2，Redis
其他环境：PHP-CGI，Jenkins-CI，Java RMI
```

检测：
```
白盒：代码审计
黑盒：漏扫工具，公开漏洞，手工看参数值及功能点
```

防御：`敏感函数禁用，变量过滤或固定，WAF产品`



### RCE-命令执行：危害：执行系统命令

系统：`Linux，Windows`

产生：
```
Web源码：Nexus，Webmin，ElasticSearch
中间件平台：Weblogic，Apache
其他环境：Postgresql，Samba，Supervisord
```

检测：
```
白盒：代码审计
黑盒：漏扫工具，公开漏洞，手工看应用功能点决定
```

防御：`敏感函数禁用，变量过滤或固定，WAF产品`



```
php eval()把字符串当作php代码执行
该字符串必须是合法的php代码，并且必须以分号结尾
如果没有在代码字符串中调用return语句，则返回NULL
如果存在解析错误，则返回false
eval(echo 123;);

php system()把字符串当作系统命令执行
system(ipconfig);
```



代码审计的时候，遇到编码等情况可以去网上找php在线运行环境然后echo
可以比较方便的理清代码内容

关于命令执行，打开文件时可以使用很多种方法尝试
```
cat flag.txt
cat<flag.txt
tac flag.txt
...
```

关于执行系统命令，不一定要用system函数，也可以用反引号
`echo 然后反引号内的内容可被作为系统命令执行`

