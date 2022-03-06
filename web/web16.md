# Web漏洞发现


## 已知CMS：

### 漏洞平台：cnvd，seebug，1337day，exploit-db，Packetstorm Security

### 工具框架：cmsscan，wpscan，joomscan，drupalscan

### 代码审计：函数点挖掘，功能点挖掘，框架类挖掘


## 开发框架：

### PHP：Yii，Laravel，thinkphp

### Java：Shiro，Struts，Spring，Maven

### Python：Flask，Django，Tornado


## 未知CMS：

### 工具框架：xray，awvs，appscan，企业公司内部产品

### 人工探针：应用功能，URL参数，盲猜测试



### 已知CMS测试：
如常见的dedecms，discuz，wordpress等源码结构，这种一般采用非框架类开发，但也有少部分采用的是框架类开发，针对此类源码程序的安全检测，应当利用公开的漏洞进行测试，如不存在可以白盒代码审计自行挖掘。

### 开发框架测试：
如常见的thinkphp，spring，flask等开发的源码程序，这种源码程序正常的安全测试思路：先获取对应的开发框架信息（名字，版本），通过公开的框架类安全问题进行测试，如不存在可采用白盒代码审计自行挖掘。

### 未知CMS测试：
如常见的企业或个人内部程序源码，也可以是某CMS二次开发的源码结构，针对此类的源码程序测试思路：能识别二次开发就按已知CMS思路进行，不能确定二次开发的话可以采用常规综合类扫描工具或脚本进行探针，也可以采用人工探针（功能点，参数，盲猜），同样在有源码的情况下也可以进行代码审计自行挖掘。
























