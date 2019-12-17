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
