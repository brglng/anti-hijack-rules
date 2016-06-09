# anti-hijack-rules
本 repo 用于记录我个人所使用的用于拦截中国电信 http 劫持的 iptables 规则列表。此规则列表仅用于拦截劫持数据包，并给运营商返回 REJECT，并不能用于恢复原始 http 数据，因此如果应用此规则列表，遇到劫持时将会出现网页无法打开的情况，这时仍需要手动刷新。如需恢复原始 http 数据，一般需要自行搭建代理，实行起来比较麻烦，因此我采用了这样一个简单方法。

## 关于中国电信的 http 劫持
在浏览购物网站如淘宝、京东，以及一些其他网站如 Bilibili 时，经常会遇到中国电信的 http 劫持，表现为网页右下角出现矩形广告，原始网页被放入一个 iframe 中。此劫持与 DNS 无关，即使将 DNS 改为非中国电信 DHCP 分配的 DNS，依然会遇到此劫持。在这里对中国电信的这种流氓行径表示强烈鄙视。

## 使用方法
如果是 OpenWrt，需要系统上安装 kmod-ipt-u32 以及 iptables-mod-u32 这两个软件包。如果需要记录日志，则还需要 kmod-ipt-ulog 和 iptables-mod-ulog。

其他系统则安装相应的 iptables 字符串匹配以及日志记录模块。

将下面规则列表中的规则加入自定义防火墙规则即可。如无需记录日志，则将带有 `-j LOG` 的规则去掉。

## 规则列表
```
iptables -I FORWARD -p tcp --sport 80 --tcp-flags ACK ACK -m string --algo bm --string "<div id=\"ad\"" -j REJECT
iptables -I FORWARD -p tcp --sport 80 --tcp-flags ACK ACK -m string --algo bm --string "<div id=\"ad\"" -j LOG --log-level info --log-prefix="hijack1: "
iptables -I FORWARD -p tcp --sport 80 --tcp-flags ACK ACK -m string --algo bm --string "<div id=\"ad_id\"" -j REJECT
iptables -I FORWARD -p tcp --sport 80 --tcp-flags ACK ACK -m string --algo bm --string "<div id=\"ad_id\"" -j LOG --log-level info --log-prefix="hijack2: "
iptables -I FORWARD -p tcp --sport 80 --tcp-flags ACK ACK -m string --algo bm --string "120.27.28.39/hw/kd580088.html" -j REJECT
iptables -I FORWARD -p tcp --sport 80 --tcp-flags ACK ACK -m string --algo bm --string "120.27.28.39/hw/kd580088.html" -j LOG --log-level info --log-prefix="hijack3: "
```
