# SQL注入技术基础

## Mysql SQL注入
1. SQL注入产生条件：可控变量，带入数据库查询，变量未存在过滤或过滤不严谨

2. MYSQL注入信息收集：操作系统`@@version_compile_os`，数据库名`database()`，数据库用户`user()`，数据库版本`version()`，其他（网站路径等）

3. MYSQL数据注入：低版本下 暴力查询或结合读取查询；高版本下 `information_schema`有据查询

4. MYSQL高权限注入：常规查询（数据注入），跨库查询，文件读写

### note：
1. 判断SQL注入点，可以直接在参数后面加随机数据，由于只有带入数据库查询才会导致页面不正常，故而可以判断是否存在注入（备注：有时这种情况会被检测，往往发生404以及跳转，此时一般是没有注入的）；

2. 在`Mysql5.0`以上的版本中加入了一个`information_schema`这个系统表，这个系统表中包含了该数据库的所有数据库名、表名、列表，可以通过SQL注入来拿到用户的账号和口令，而`Mysql5.0`以下的只能暴力跑表名；`5.0 以下是多用户单操作，5.0 以上是多用户多操作`；
数据库中符号"`.`"代表下一级，如`xiao.user`表示`xiao`数据库下的`user`表名；

3. `information_schema.tables`：记录所有表名信息的表；

4. `information_schema.columns`：记录所有列名信息的表；

5. `information_schema.schemata`：记录所有库名信息的表；

6. `table_name`：表名；

7. `column_name`：列名；

8. `schema_name`：库名；

9. `table_schema`：`information_schema.tables`表中记录数据库名的字段；

10. `group_concat(table_name)`：全部表名；

## MYSQL文件读写：
1. `select load_file('C:/pwd.txt');`    读取指定文件，可能由于权限问题而返回`NULL`

2. `show global variables like '%secure%';    或 select @@secure_file_priv`    查看`secure-file-priv`参数的值，默认为`NULL`，此时修改`mysql.ini`文件（也可能是`my.ini`文件），在`[mysqld]`下加入`secure-file-priv = `保存重启mysql即可解决`load_file`函数权限问题；

3. `select 'x' into outfile 'c:/x.txt';    或 select 'x' into dumpfile 'c:/x.txt'; `   写入文件

4. （outfile函数可以导出多行，而dumpfile只能导出一行数据 outfile函数在将数据写到文件里时有特殊的格式转换，而dumpfile则保持原数据格式）

5. PHP魔术引号开关：`magic_quotes_gpc`（或`addslashes()`函数），该选项启用时将自动使用"`\`"转义字符，以阻止SQL注入，可以用编码方式绕过
