#openwrt插件收集

2019.11.29更新

全过程命令，默认全新安装
ssh登入后(shellbox亦可)

cd /tmp
wget https://github.com/pymumu/smartdns/releases/download/release27/smartdns.1.2019.11.02-1102.x86_64-openwrt-all.ipk
wget https://github.com/pymumu/smartdns/releases/download/release27/luci-app-smartdns.1.2019.11.02-1102.all-luci-all.ipk

opkg install smartdns.1.2019.11.02-1102.x86_64-openwrt-all.ipk
opkg install luci-app-smartdns.1.2019.11.02-1102.all-luci-all.ipk

复制代码
上面需要的都安装好了，然后进入服务找到smartdns，重定向选中作为dnsmasq的上游服务器

上游服务器填入你需要的服务器
这里我填的全部是tls服务器，参考https://www.233py.com/service.html等
最后勾上启用
最后通过
nslookup www.baidu.com 192.168.1.1
复制代码
检查是否生效192.168.1.1改为你的lede地址，如果多次检查只有一个IP地址，则为生效，非常简单
2019.4.4 测试https失败，tls服务器只能填IP（不能填域名）



推荐的一键安装脚本：
root 账号

sudo -i

bash <(curl -s -L https://git.io/v2ray.sh)

LinuxCopy
如果提示 curl: command not found
请安装 Curl
ubuntu/debian 系统安装 Curl 方法: apt-get update -y && apt-get install curl -y
centos 系统安装 Curl 方法: yum update -y && yum install curl -y
协议很多，可以搜索了解下，也可以直接用默认的。有的需要有域名（websocket tls），有的就不用（tcp，mkcp）

没啥需求就用 TCP
追求更加安全就用 WS + TLS
ISP 多作怪用动态端口
VPS 网络不好就用 mKCP

基本上按照提示一步步选择，回车就可以搞定了。

配置及管理
V2Ray 配置文件路径：/etc/v2ray/config.json

手动配置打开这个文件就好了。至于输入输出怎么填我就不写了，官方文档能看懂的就自己填，看不懂的就用一键脚本了。

快速管理命令：
v2ray info 查看 V2Ray 配置信息

v2ray config 修改 V2Ray 配置

v2ray link 生成 V2Ray 配置文件链接

v2ray infolink 生成 V2Ray 配置信息链接

v2ray qr 生成 V2Ray 配置二维码链接

v2ray ss 修改 Shadowsocks 配置

v2ray ssinfo 查看 Shadowsocks 配置信息

v2ray ssqr 生成 Shadowsocks 配置二维码链接

v2ray status 查看 V2Ray 运行状态

v2ray start 启动 V2Ray

v2ray stop 停止 V2Ray

v2ray restart 重启 V2Ray

v2ray log 查看 V2Ray 运行日志

v2ray update 更新 V2Ray

v2ray update.sh 更新 V2Ray 管理脚本

v2ray uninstall 卸载 V2Ray

2. 客户端配置
各平台客户端介绍和下载

V2RayW Win
下载：https://github.com/Cenmrev/V2RayW

V2RayN Win
下载：https://github.com/2dust/v2rayN

V2RayX MAC
下载：https://github.com/Cenmrev/V2RayX

考虑到部分电脑软件只支持http协议，本地监测的端口有socks和http这俩。

127.0.1.1+1081是socks。
127.0.2.1+1082是http。
使用方法：

手机配置更简单，我用的 v2rayNG 直接扫描生成的二维码就好了。

一般协议的安装配置到此就结束了，可以直接去使用了。

3.使用 Cloudflare 中转 V2Ray WebSocket 的流量
首先你得有域名，免费的可以到这里申请http://www.freenom.com/zh/index.html

会更改DNS，设置A记录就好了，很久之前我也写过介绍的文章，这里就不重复了，搜索到处都有。

安装时传输协议选择 WebSocket (即选择 3 )，V2Ray 端口选择 80，其他就默认。等待安装完成之后生成二维码或者配置参数。

输入 v2ray status 看看 V2Ray 有没有在运行，如果没有，使用 netstat -lp 看看是什么程序占用了 80 / http ，解决占用程序，然后使用 v2ray start 启动 V2Ray。

在 Cloudflare 注册登录加入域名解析，Crypto 那里设置 SSL 为 Flexible，DNS 那里添加一个 A 记录指向服务器 IP ，Status 那里的图标务必为橙色状态，即是 DNS and HTTP proxy(CDN)。

V2Ray 配置信息

地址 (Address) = 域名
端口 (Port) = 443
用户ID (User ID) = 用户ID
额外ID (Alter Id) = 233
传输协议 (Network) = ws (WebSocket)
伪装类型 (header type) = none
TLS = 打开
LinuxCopy
备注：
1. 以上使用 Cloudflare 中转 V2Ray 的 WebSocket 流量，由于 Cloudflare 实现了 TLS ，所以 V2Ray 配置信息请以上面的内容为准。
这种方式比较简单的利用了 Cloudflare 的 TLS，服务器和客户端两端的配置也相对很简单，而且，可以用多个域名来指向一个服务器，那就不怕封了。
2. 在服务器端也可以实现 TLS。先在 DNS 那里修改之前添加的 IP 记录，将 Status 那里的图标点一下让它变成灰色状态，即是 DNS only. 然后在 Crypto 那里设置 SSL 为 Full。
如果当前 V2Ray 端口为 80，输入 v2ray config 回车，选择修改端口即可，修改的端口随便输入，只要不是 80 或者 443 即可。
再次输入 v2ray config 回车，选择修改传输协议，选择 WebSocket + TLS ，即是选择 4。按提示输入域名，域名解析 Y ，自动配置 TLS 也是 Y ，后面还会安装 Caddy 按回车继续，等待配置自动完成，生成配置二维码。
等配置完成后，Cloudflare 的 DNS，将 Status 那里的图标点一下让它变成橙色状态，即是 DNS and HTTP proxy(CDN)。
这样全部实现 TLS 配置就结束了，可以打开客户端试试。这种方式是两端都是 TLS ，但域名只能绑定一个。
如有问题可以先用命令: systemctl status caddy 或者 service caddy status，检查一下 Caddy 的运行状态。
参考： 最稳的上网方式：v2ray+ws+tls一键脚本
以上两种方法各有优缺点，我选择的是第一种。
3. 这两种方法都适合在极端网络环境下使用，速度较慢，基本上就是满足看看图片和文字了。
