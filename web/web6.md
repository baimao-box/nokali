反射型xss和存储型xss区别之一是有无写入数据库（这两种漏洞在后端）

DOM型和前面两种的区别是DOM型问题出在前端代码（此类可以直接前端代码审计）

比较隐蔽的探测xss的方法
```
<script>console.log(document.cookie)</script>
```
在控制台输出当前cookie

关于利用，在有公网服务器的情况下可以架设XSS平台或者BEEF

没有的情况下使用公开xss平台（不重要的测试中），或者上述探测方式（有回显的情况下）

存储型XSS常见出现点：`留言板/评论区/订单系统/反馈条件等`

不常见的出现点：参数传递（有些传参结果会在后台显示，黑盒大概率只能盲打）

HTTP（S）数据包提交工具：Postman（感觉不如burpsuite）



网站判断用户身份常用的方法有cookie和session

cookie：存储在本地，存活时间较长，一般是中小型网站

session（会话）：存储在服务器，存活时间较短，常见于大型网站

有时窃取cookie并不能成功登录，原因可能就是目标启用了session验证

常见的情况是凭据里有`PHPSESSID=`

session由于存储在服务器，并不能直接被js代码窃取

一种理论上可以窃取session的方式（非常鸡肋）：

通过js进行浏览器劫持，跳转到phpinfo页面，再通过js读取页面内容获取cookie部分，其中就有可能存在session



关于HttpOnly：

通过在cookie中设置该属性，通过js脚本将无法读取到cookie信息，能够在一定程度上防止xss攻击



关于启用了httponly的目标：

由于不能获取cookie，需要利用表单劫持或者社工认证等方式直接截取用户名和密码登录后台。



相关资源：

`xss-labs`	靶场

`xss绕过技术.pdf`	xss绕过手册
