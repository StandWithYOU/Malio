# 不能删改页面底部的 Powered by SSPANEL，不能删改staff页面的任何信息。
## 删除Powered by SSPANEL可能会导致你的网站被D，现在 Theme by editXY 可以随意去掉

本主题包含了SSPANEL的完整代码，需要删除原来的面板，然后全新安装（数据库通用），操作前请备份面板

## 安装步骤



# 安装宝塔面板

使用SSH登录你的VPS，根据你VPS的系统选择下面的安装命令

宝塔面板 Centos 一键安装脚本👇

```
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

宝塔面板 Ubuntu/Debian 一键安装脚本👇

```
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh
```

输入 `y` 回车安装

执行完毕后，会显示类似下面的宝塔面板的登录信息，请保存好此信息

```
Bt-Panel: http://xxx.xxx.xx.xx:8888/xxxxxxx
username: xxxxxxx
password: xxxxxxxx
```

# 安装LNMP

登录宝塔面板后，宝塔面板会提示你安装 PHP, MySQL, Nginx 等环境。

推荐使用 PHP 7.1、MySQL 5.6、Nginx任意版本。

需要一段时间才能安装完成，请等待完成。

# 配置PHP

打开 `宝塔面版 > 软件商店 > 你安装的PHP > 设置`，在禁用函数一栏删除 `system`、`proc_open`、`proc_get_status`、`putenv`、`pcntl_signal`。

# 安装Malio主题

打开 `宝塔面版 > 网站 > 添加站点`，输入你的域名，点击提交

------

在SSH里执行下面命令👇👇👇

下载SSPANEL+Malio的代码。如果你的这个网站文件夹包含原版的SSPANEL代码，请删除原版SSPANEL代码或者换个目录

```
cd /www/wwwroot/你的网站文件夹名
git clone -b malio https://gitlab.com/maxitio/malio-theme-for-sspanel.git tmp && mv tmp/.git . && rm -rf tmp && git reset --hard
```

然后Git会要求你输入Gitlab的账号密码

使用composer安装依赖

```
wget https://getcomposer.org/installer -O composer.phar
php composer.phar
php composer.phar install
```

用composer安装依赖时如果内存不够的话，可以使用 `宝塔面版 > 首页 > Linux工具箱 > Swap/虚拟内存` 增加虚拟内存

复制配置文件

```
cp config/.malio_config.example.php config/.malio_config.php
cp config/.config.example.php config/.config.php
cp config/.i18n.example.php config/.i18n.php
```

------

打开 `宝塔面版 > 网站 > 你的网站`

在 `网站目录` 里取消勾选 `防跨站攻击`，运行目录里面选择 `/public`，点击保存。

在 `伪静态` 中填入下面内容，然后保存

```
location / {
    try_files $uri /index.php$is_args$args;
}
```

------

在SSH里的网站目录下执行，给网站文件755权限

```
cd ../
chmod -R 755 你的文件夹名/
chown -R www:www 你的文件夹名/
```

# 数据库操作

打开 `宝塔面版 > 数据库`，选择添加数据库，数据库名字和用户名任意写，密码建议使用随机生成的密码，点击提交即可添加数据库

点击数据库名字旁边的 `导入 > 本地上传`，即可上传并导入sql文件到数据库

如果你是全新安装面板，请上传并导入网站目录下的 `/sql/glzjin_all.sql`、`/sql/malio_all.sql`

如果你是从其他主题迁移到Malio主题，请上传并导入网站目录下的 `/sql/malio_all.sql`、`/sql/user_subscribe_log.sql`、`/sql/detect_ban_log.sql`

如果没有导入 `/sql/malio_all.sql` 的话会导致无法注册新用户

将数据库user表里的全部用户的theme列改为malio，使用phpmyadmin执行这条sql语句👉 `UPDATE user SET theme='malio'`

# 编辑 config.php

将你的数据库名字，用户名和密码填入.config.php里，类似下面这样

```
$_ENV['db_database'] = 'sspanel';			//数据库名
$_ENV['db_username'] = 'sspanel';		//数据库用户名
$_ENV['db_password'] = 'sspanel_password';			//用户名对应的密码
```

# 其他操作

在SSH里的网站目录下执行 `php xcat createAdmin`，即可创建管理员用户。（如果你已经有了管理员用户就不用创建了）

在SSH里的网站目录下执行 `php xcat initQQWry `，即可下载IP数据库（必须要执行这个）

下载通知频道里的 `GeoLite2-City.mmdb` 文件放入网站目录下的 `/storage/` 目录下

为Crisp和Chatra的跨设备恢复会话功能生成UUID，在SSH里网站目录下执行👉 `php xcat GenerateUUIDforAllUsers`

# 配置定时任务

执行 `crontab -e` 命令，添加以下：

```
0 0 * * * php -n /www/wwwroot/你的文件夹名/xcat dailyjob
*/1 * * * * php /www/wwwroot/你的文件夹名/xcat checkjob
*/1 * * * * php /www/wwwroot/你的文件夹名/xcat syncnode
```

# 客户端下载

客户端的安装包需要自行下载到 `/public/client-download/` 目录，安装包名字参考同目录下的 apps.txt 文件

另外在此目录下还提供了 [download.sh](https://github.com/sspanel-uim/ssr-download-updater) 脚本，可自动下载部分客户端。

# 请仔细阅读 .malio_config.php 和 .config.php 的全部注释，进一步了解SSPANEL






## 贡献

[提出新想法 & 提交 Bug](https://github.com/Cadwalader307/Malio-Theme-for-SSPANEL/issues/new) | [改善文档 & 投稿](https://github.com/Cadwalader307/Malio-Theme-for-SSPANEL/Wiki) | [Fork & Pull Request](https://github.com/Cadwalader307/Malio-Theme-for-SSPANEL/pulls)

由于原开发团队已经放弃Malio主题，因此此项目欢迎各种贡献，包括但不限于改进，新功能，文档和代码改进，问题和错误报告。

## 注意事项
.malio_config.php 文件里的商品id必须设置好，不然在商店plans模式下没办法购买。

在.config.php里设置新用户注册等级为-1，如果不设置为-1的话，就没有新手引导教程，同时需要在.config.php里设置用户等级过期时间，建议设置为超过一个月的时间(720小时)。

安装完成后如果旧用户无法登录的话，检查下 .config.php 里面的 salt 和 pwdMethod 的值是否跟原来的 .config.php 一致。

端口偏移的说明查看 [端口偏移的食用方法](https://678765.xyz/2019/2.html)，根据群友说支持普通端口和单端口，格式跟uim原版的偏移不一样

如果需要配置Stripe支付接口的话，请看本仓库的wiki

需要去.config.php打开订阅记录的开关才会记录订阅

https://t.me/woaizyg)

## CREDIT
基于 [Anankke](https://github.com/Anankke)，[galaxyq](https://github.com/galaxychuck)，[dumplin](https://github.com/dumplin233)，[Rin SAMA](https://github.com/mxihan)，[Miku](https://github.com/xcxnig)，[Tony Zou](https://github.com/ZJY2003)，[Nymph](https://github.com/laurieryayoi) 维护的 [SSPanel-Uim](https://github.com/Anankke/SSPanel-Uim) 修改

