# SQL注入技术进阶

### note：
1. 路径获取常见方法：报错显示，遗留文件，漏洞报错，平台配置文件，爆破等

2. 当注入点权限不足时，可能无法列出所有数据库，应该先查询当前数据库，然后在本数据库中列出数据表

3. 目标为MongoDB时，sqlmap等工具可能无效，需要使用专门的nosql注入工具，比如NoSQLAttack等

## SQL盲注：
1. 语法参考：
    ```
    like 'ro%'        判断ro或ro...是否成立
    regexp '^xiaodi[a-z]'        匹配xiaodi及xiaodi...等
    if(条件,5,0)        条件成立，返回5，否则返回0
    sleep(5)        SQL语句延时执行5秒
    mid(a,b,c)        在a字符串里，从b位置开始截取c位（b似乎是从1开始的）
    substr(a,b,c)        同mid()
    left(a,b)        从左侧截取a的前b位
    length(database())=8        判断数据库database()名的长度
    ord(a)或ascii(a)        得到a的ascii码（ASCII码加上数值比较（<>），可以快速缩小所查字符范围）
    ```

2. SQL DNSlog注入：高权限但有限制条件的情况下使用，有DnslogSqlinj等工具（解决盲注不能回显数据，效率低的问题）

## SQLi BypassWAF：
1. 编码绕过：`%23（注释符#）`,`%0A（换行符）`等
    ```
    如 id=-1 union%23a%0Aselect 1,2,3#
    这样等同于
    union #a
    select 1,2,3#
    （a是用作干扰字符）
    ```

2. 大小写

3. 加密解密

4. 等价函数

5. 特殊符号

6. 反序列化

7. 注释符混用

8. 提交方式：`更改提交方式/变异`

9. 其他：`Fuzz/数据库特性/垃圾数据溢出/HTTP参数污染 等`

10. HTTP参数污染：
    ```
    如 ?id=1/**&id=-1 union select 1,2,database()--+*/
    利用服务器接收参数的特性和注释符，使得真正的payload绕过WAF被服务器接收
    ```

## SQLMAP bypass：
1. SQLMAP默认的UA会暴露自身，可以使用`--random-agent`将UA随机化

2. 当目标开启了CC防护，sqlmap会因为速度过快而被拦截，这时可以使用 `代理池/降低速度/修改特征为搜索引擎爬虫` 等方法绕过拦截 如： `--user-agent="Mozilla/5.0 ...（省略）... /spider.html)"`
