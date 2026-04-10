# 															Web

------

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
