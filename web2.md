# SQL注入技术

## SQL注入基础
1. SQL注入产生条件：可控变量，带入数据库查询，变量未存在过滤或过滤不严谨

2. MYSQL注入信息收集：操作系统@@version_compile_os，数据库名database()，数据库用户user()，数据库版本version()，其他（网站路径等）

3. MYSQL数据注入：低版本下 暴力查询或结合读取查询；高版本下 information_schema有据查询

4. MYSQL高权限注入：常规查询（数据注入），跨库查询，文件读写

### note：
判断SQL注入点，可以直接在参数后面加随机数据，由于只有带入数据库查询才会导致页面不正常，故而可以判断是否存在注入（备注：有时这种情况会被检测，往往发生404以及跳转，此时一般是没有注入的）；

在Mysql5.0以上的版本中加入了一个information_schema这个系统表，这个系统表中包含了该数据库的所有数据库名、表名、列表，可以通过SQL注入来拿到用户的账号和口令，而Mysql5.0以下的只能暴力跑表名；5.0 以下是多用户单操作，5.0 以上是多用户多操作；

数据库中符号"."代表下一级，如xiao.user表示xiao数据库下的user表名；

information_schema.tables：记录所有表名信息的表；

information_schema.columns：记录所有列名信息的表；

information_schema.schemata：记录所有库名信息的表；

table_name：表名；

column_name：列名；

schema_name：库名；

table_schema：information_schema.tables表中记录数据库名的字段；

group_concat(table_name)：全部表名；

路径获取常见方法：报错显示，遗留文件，漏洞报错，平台配置文件，爆破等

当注入点权限不足时，可能无法列出所有数据库，应该先查询当前数据库，然后在本数据库中列出数据表

目标为MongoDB时，sqlmap等工具可能无效，需要使用专门的nosql注入工具，比如NoSQLAttack等

## MYSQL文件读写：
1. select load_file('C:/pwd.txt');    读取指定文件，可能由于权限问题而返回NULL

2. show global variables like '%secure%';    或 select @@secure_file_priv    查看secure-file-priv参数的值，默认为NULL，此时修改mysql.ini文件（也可能是my.ini文件），在\[mysqld\]下加入secure-file-priv =     然后保存重启mysql即可解决load_file函数权限问题；

3. select 'x' into outfile 'c:/x.txt';    或 select 'x' into dumpfile 'c:/x.txt';    写入文件

4. outfile函数可以导出多行，而dumpfile只能导出一行数据 outfile函数在将数据写到文件里时有特殊的格式转换，而dumpfile则保持原数据格式

5. PHP魔术引号开关：magic_quotes_gpc（或addslashes()函数），该选项启用时将自动使用"\\"转义字符，以阻止SQL注入，可以用编码方式绕过

## SQL盲注：
语法参考：

like 'ro%'        判断ro或ro...是否成立

regexp '^xiaodi\[a-z\]'        匹配xiaodi及xiaodi...等

if(条件,5,0)        条件成立，返回5，否则返回0

sleep(5)        SQL语句延时执行5秒

mid(a,b,c)        在a字符串里，从b位置开始截取c位（b似乎是从1开始的）

substr(a,b,c)        同mid()

left(a,b)        从左侧截取a的前b位

length(database())=8        判断数据库database()名的长度

ord(a)或ascii(a)        得到a的ascii码（ASCII码加上数值比较（<>），可以快速缩小所查字符范围）

SQL DNSlog注入：高权限但有限制条件的情况下使用，有DnslogSqlinj等工具（解决盲注不能回显数据，效率低的问题）

## SQLi BypassWAF：
编码绕过：%23（注释符#）,%0A（换行符）等
	`如 id=-1 union%23a%0Aselect 1,2,3#
	这样等同于
	union #a
	select 1,2,3#
	（a是用作干扰字符）`

大小写

加密解密

等价函数

特殊符号

反序列化

注释符混用

提交方式：更改提交方式/变异

其他：Fuzz/数据库特性/垃圾数据溢出/HTTP参数污染 等

HTTP参数污染：
	如 `?id=1/**&id=-1 union select 1,2,database()--+*/`
	利用服务器接收参数的特性和注释符，使得真正的payload绕过WAF被服务器接收

### SQLMAP bypass技巧：
SQLMAP默认的UA会暴露自身，可以使用--random-agent将UA随机化

当目标开启了CC防护，sqlmap会因为速度过快而被拦截，这时可以使用 代理池/降低速度/修改特征为搜索引擎爬虫 等方法绕过拦截

如： `--user-agent="Mozilla/5.0 ...（省略）... /spider.html)"`
