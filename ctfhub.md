# 															Web

------

## WEB前置技能

### HTTP协议

### (302跳转)

```
HTTP 302 Found 是一种常见的 临时重定向 状态码，表示请求的资源暂时位于响应头 Location 指定的新 URL，下次请求仍应使用原始地址。浏览器或客户端接收到该响应后，会自动向新地址发起请求。

核心特性

临时性：资源位置只是暂时改变，未来可能恢复原地址。
搜索引擎行为：通常不会将原 URL 的 SEO 权重传递给新 URL。
方法转换：多数浏览器会将原请求方法（如 POST）转换为 GET，这也是后来引入 307 Temporary Redirect 的原因。

典型应用场景

网站维护：将所有请求临时跳转到维护页面。
A/B 测试或流量分流：部分用户被导向测试环境。
HTTP→HTTPS 过渡：短期内强制使用 HTTPS，后续可改为 301。
未登录跳转：访问受限页面时临时跳转到登录页，登录后返回原页面。

响应示例

HTTP/1.1 302 Found
Location: https://www.example.com/new-page
Content-Length: 0

复制客户端会立即访问 https://www.example.com/new-page。

Nginx 配置示例
server {
listen 80;
server_name example.com;
return 302 https://$host$request_uri;
}

Apache 配置示例
Redirect 302 / https://example.com/maintenance.html

与其他状态码对比

301 Moved Permanently：永久重定向，搜索引擎会更新索引并传递权重。
303 See Other：重定向到另一个资源，并强制使用 GET 方法。
307 Temporary Redirect：与 302 类似，但保留原请求方法，避免 POST→GET 转换。

最佳实践
确认是临时重定向时使用 302，长期迁移应改为 301 或 307。
避免滥用 302，以防 URL 劫持 等 SEO 风险。

调试可用 curl -I 或浏览器开发者工具查看 Status 与 Location 头
```



### (Cookie)

​		在网站中，http请求是无状态的。也就是说即使第一次和服务器连接后并且登录成功后，第二次请求服务器依然不能知道当前请求是哪个用户。cookie的出现就是为了解决这个问题，第一次登录后服务器返回一些数据（cookie）给浏览器，然后浏览器保存在本地，当该用户发送第二次请求的时候，就会自动的把上次请求存储的cookie数据自动的携带给服务器，服务器通过浏览器携带的数据就能判断当前用户是哪个了。cookie存储的数据量有限，不同的浏览器有不同的存储大小，但一般不超过4KB。因此使用cookie只能存储一些小量的数据。

​		session和cookie的作用有点类似，都是为了存储用户相关的信息。不同的是，cookie是存储在本地浏览器，而session存储在服务器。存储在服务器的数据会更加的安全，不容易被窃取。但存储在服务器也有一定的弊端，就是会占用服务器的资源，但现在服务器已经发展至今，一些session信息还是绰绰有余的。

[一文彻底搞清session、cookie、token的区别 - 知乎](https://zhuanlan.zhihu.com/p/631349844)



### (基础认证)

​		在HTTP中，基本认证（英语：Basic access authentication）是允许http用户代理（如：网页浏览器）在请求时，提供 用户名 和 密码 的一种方式。

```
BP爆破
狙击手模式（Sniper）——它使用一组Payload集合，依次替换Payload位置上（一次攻击只能使用一个Payload位置）被§标志的文本（而没有被§标志的文本将不受影响），对服务器端进行请求，通常用于测试请求参数是否存在漏洞。

攻城锤模式（Battering ram）——它使用单一的Payload集合，依次替换Payload位置上被§标志的文本（而没有被§标志的文本将不受影响），对服务器端进行请求，与狙击手模式的区别在于，如果有多个参数且都为Payload位置标志时，使用的Payload值是相同的，而狙击手模式只能使用一个Payload位置标志。

草叉模式（Pitchfork ）——它可以使用多组Payload集合，在每一个不同的Payload标志位置上（最多20个），遍历所有的Payload。举例来说，如果有两个Payload标志位置，第一个Payload值为A和B，第二个Payload值为C和D，则发起攻击时，将共发起两次攻击，第一次使用的Payload分别为A和C，第二次使用的Payload分别为B和D。

集束炸弹模式（Cluster bomb） ——它可以使用多组Payload集合，在每一个不同的Payload标志位置上（最多20个），依次遍历所有的Payload。它与草叉模式的主要区别在于，执行的Payload数据Payload组的乘积。举例来说，如果有两个Payload标志位置，第一个Payload值为A和B，第二个Payload值为C和D，则发起攻击时，将共发起四次攻击，第一次使用的Payload分别为A和C，第二次使用的Payload分别为A和D，第三次使用的Payload分别为B和C，第四次使用的Payload分别为B和D。
```

[Burp Suite-Intruder功能详解与使用实例 - 墨天轮](https://www.modb.pro/db/190929)

[ctfhub技能树—web前置技能—http协议—基础认证 - anweilx - 博客园](https://www.cnblogs.com/anweilx/p/12410622.html)

（响应包源代码）





## RCE















## 信息泄露

### phpinfo

​	在该页面用Ctrl+F 搜索flag 即可



### 备份文件下载

#### （网站源码）

​	用dirsearch 扫描目录

*--format*：指定输出格式（如 *--format json*）

 	扫描目标 URL：

```
python3 dirsearch.py -u http://example.com
```

​	指定文件扩展名：		*-e*：指定文件扩展名（如 *-e php,html,js*）

```
python3 dirsearch.py -u http://example.com -e php,html
```

​	使用自定义字典：  	 *-w*：使用自定义字典文件（如 *-w /path/to/wordlist.txt*）

```
python3 dirsearch.py -u http://example.com -w custom-wordlist.txt
```

​	设置线程数：			  *-t*：设置线程数以提高扫描速度（如 *-t 20*）

```
python3 dirsearch.py -u http://example.com -t 20
```

​	排除特定状态码：  	 *-x*：排除特定 HTTP 状态码（如 *-x 404,403*）

```
python3 dirsearch.py -u http://example.com -x 404,403
```

​	保存结果到文件：		*-o*：将结果保存到文件（如 *-o results.txt*）

```
python3 dirsearch.py -u http://example.com -o output.txt
```

​	**递归扫描**：启用递归扫描并限制深度：

```
python3 dirsearch.py -u http://example.com -r -R 5
```

​	**代理支持**：通过代理进行扫描：

```
python3 dirsearch.py -u http://example.com -p http://127.0.0.1:8080
```

​	**自定义请求头**：添加自定义 HTTP 头：

```
python3 dirsearch.py -u http://example.com -H "Authorization: Bearer token"
```

​	-i 200: 这是一个过滤器（--filter-code的缩写），告诉工具在输出结果时只显示那些响应状态码为200（即请求成功）的路径，屏蔽404错误等其他无效信息，使结果更清晰。



#### （bak文件）

[CTFHub 信息泄露通关笔记4：备份文件下载 bak文件 - 教程 - tlnshuju - 博客园](https://www.cnblogs.com/tlnshuju/p/19151035)

​	dirsearch 扫描指定文件 dirsearch -u http://target.com -i 200   

- **文本编辑器自动备份**：部分编辑器（如 Notepad++、Sublime Text）可配置 “保存时自动生成.bak 文件”，例如 Notepad++ 开启 “备份” 功能后，编辑 `index.php` 并保存时，会自动生成 `index.php.bak`；

- **服务器管理工具备份**：如宝塔面板、cPanel 等可视化管理工具，在 “文件修改”“网站迁移” 功能中，会自动生成 `.bak` 备份（如修改 `nginx.conf` 时生成 `nginx.conf.bak`）；

- **脚本 / 程序自动备份**：开发者编写的自动化脚本（如数据库备份脚本、代码更新脚本），可能将 `.bak` 作为备份文件的统一后缀（如 `bash backup.sh` 脚本执行时生成 `data.sql.bak`）

  

  易猜测性：文件名可预测

- 针对首页文件：`index.html.bak`、`index.php.bak`、`index.jsp.bak`；
- 针对配置文件：`config.php.bak`、`database.ini.bak`、`appsettings.json.bak`；
- 针对敏感文件：`flag.txt.bak`、`backup.sql.bak`、`admin_login.php.bak`；
- 针对特殊功能文件：`upload.php.bak`（上传功能）、`pay.php.bak`（支付逻辑）。

​	其他目录爆破工具：dirmap、gobuster



#### （vim缓存）

方法一：直接访问 /.index.php.swp 文件可以看到为二进制文件

方法二：下载该vim缓存文件

注意：vim缓存文件ls指令是不显示的，直接使用kali的vim编辑器恢复该vim缓存文件

```linux
vim -r .index.php.swp
```

方法三：直接使用curl访问

```
curl target.com/.index.php.swp -o -
```

​		

​		在编辑文件的过程中，Vim将会在当前目录中自动生成一个以.swp结尾的临时交换文件，用于备份缓冲区中的内容，以便在意外退出时可以恢复之前编辑的内容。
​		当完成编辑并保存退出后，临时交换文件将会被删除；

​		但如果Vim意外退出，那么这个临时文件就会留在硬盘中。当Vim再次启动时，会检查当前目录中是否存在交换文件。如果存在，则意味着Vim正在编辑此文件，或者在上次编辑过程中意外退出，这时Vim就会给出警告信息，并要求我们在以下四个选项中做出选择：

    Open Read -Only（以只读方式打开）：如果我们想要查看文件内容或是有另一个编辑过程正在运行，那么可以选择此选项；
    Edit  anyway（编辑文件）：请尽量不要选择此选项。因为如果同时有两个或是多个编辑过程同时编辑一个文件，那么只有最后一个保存的编辑过程有效；
    Recover（恢复）：如果在编辑过程中vim意外退出，那么可以选择此选项尝试从交换文件恢复文档；
    Quit（退出）：选择此选项，将取消对此文件的修改。

​		在使用vim时会创建临时缓存文件，关闭vim时缓存文件则会被删除，当vim异常退出后，因为未处理缓存文件，导致可以通过缓存文件恢复原始文件内容
以 index.php 为例：第一次产生的交换文件名为       .index.php.swp
​		再次意外退出后，将会产生名为 .index.php.swo 的交换文件
​		第三次产生的交换文件则为 .index.php.swn



#### （.DS_Store）

访问 /.DS_Store  

​		.DS_Store 是 Mac OS 保存文件夹的自定义属性的隐藏文件 通过.DS_Store可以知道这个目录里面所有文件的清单。

​	**.DS_Store的禁用与启用：**

**1.**禁用 .DS_Store，重启生效

```
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
```

**2.**启用 .DS_Store

```
defaults delete com.apple.desktopservices DSDontWriteNetworkStores
```



### Git泄露

#### （log）

​		当前大量开发人员使用git进行版本控制，对站点自动部署。如果配置不当,可能会将.git文件夹直接部署到线上环境。这就引起了git泄露漏洞。请尝试使用BugScanTeam的GitHack完成本题

- **部署失误**：开发或运维人员在部署 Web 项目时，未排除 `.git` 目录，导致其被上传至生产服务器的 Web 根目录（如 `wwwroot`、`public` 等）。

- **权限配置不当**：Web 服务器（如 Nginx、Apache）对 `.git` 目录未做访问限制，允许外部直接访问。

- **目录遍历**：服务器存在目录遍历安全风险时，攻击者可通过构造路径（如 `../.git/HEAD`）绕过限制访问 `.git` 内容。

  

​	`.git` 目录的作用与组成

Git 作为分布式版本控制系统，会在项目根目录生成 `.git` 目录，用于存储版本控制所需的所有元数据，包括：

- **版本历史**：所有提交记录（`commits`）、分支（`branches`）、标签（`tags`）等。
- **文件快照**：项目文件的不同版本数据（存储在 `objects` 目录）。
- **配置信息**：仓库配置（`config`）、当前分支指针（`HEAD`）、索引文件（`index`）等。

```
下载安装 GitHack
sudo git clone https://github.com/BugScanTeam/GitHack

#使用方法：
cd GitHack
sudo python2 GitHack.py http://www.example.com/.git/
执行该命令后，工具会尝试从目标 URL 还原 Git 仓库内容，还原后的文件会保存在当前目录下的 dist/ 目录中

查看git log
记录log id   commit的值

获取flag   (git log 和 git reset法)
(1)执行git log diff，比较add flag版本的代码修改日志
sudo git log diff 351bb3e9683eaaaff31b27cfb4de54a7b3bb0463
(2)执行git reset将代码切换为add flag版本的code
sudo git reset --hard e231ebc4862d09d20a47a9b48925886d1b7eb958
```













#### (Stash)





#### (Index)
