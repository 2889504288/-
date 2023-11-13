# 小米路由器刷机经验分享
咸鱼收了个小米路由器青春版，用于实现校园网多设备在线.刷的op  用的UA2F在此分享一下经验

所需材料：

windows 电脑一台  ，miwifi nano一台，网线一根，取卡针一根

putty winscp breed op固件      miwifinano开发 固件

软件工具链接：

https://wwma.lanzouj.com/iBVbi1eq6xcd

刷机过程:

1、刷入开发版本ROM

插电开机，进入后台 ，在系统界面 选择 手动升级miwifi_r1cl_all_59371_2.1.26.bin

等路由器自动重启后再次进入后台

- 此时浏览器地址应该类似如下：[http://192.168.31.1/cgi-bin/luci ... 1d9/web/home#router](http://192.168.31.1/cgi-bin/luci/;stok=075a9192918557c27cdbcae2175281d9/web/home#router)

- 将“web/home#router”替换为（一定要登录进入后台之后再替换）： 
- ```/api/xqsystem/set_name_password?oldPwd=当前路由的密码&newPwd=admin```
  /api/xqsystem/set_name_password?oldPwd=当前路由的密码&amp;newPwd=admin
  ```
  

- 最后组合：
- ```http://192.168.31.1/cgi-bin/luci/;stok=075a9192918557c27cdbcae2175281d9/api/xqsystem/set_name_password?oldPwd=当前路由的密码&newPwd=admin
  http://192.168.31.1/cgi-bin/luci/;stok=075a9192918557c27cdbcae2175281d9/api/xqsystem/set_name_password?oldPwd=当前路由的密码&amp;newPwd=admin
  ```
  

- 在浏览器上访问后，网页返回 {“code”:0} 即可。
- 正常情况下此时已经可以通过是ssh链接路由器如果不能，参照 如下链接里的方法
- [小米路由器青春版(mini) 开启SSH 小米路由计算&更改官方root&SSH密码-小米无线路由器以及小米无线相关的设备-恩山无线论坛 (right.com.cn)](https://www.right.com.cn/forum/thread-183266-1-3.html)

**使用另一种方法开启telnet**  
(这种方法支持稳定版，支持mini和青春版)：

和上面一样登陆路由器，把你浏览器中的/web/home#router替换成  
```/api/xqnetwork/set_wifi_ap?ssid=tianbao&encryption=NONE&enctype=NONE&channel=1%3B%2Fusr%2Fsbin%2Ftelnetd```

然后等一会浏览器返回：  
{"msg":"未能连接到指定WiFi(Probe timeout)","code":1616}

这时候应该已经启动telnet了，

此时用putty 选择tenlnet协议就可以链接路由器了

地址：192.168.31.1

端口：23

用户: root

密码： admin

输入然后输入：

然后输入：

```sed
sed -i ":x;N;s/if \[.*\; then\n.*return 0\n.*fi/#tb/;b x" /etc/init.d/dropbear
/etc/init.d/dropbear start
```

随后输入：```nvram set ssh_en=1; nvram commit```

这样就可以ssh登陆了

**3、通过putty登录到路由器**

打开putty中文版1.0v，在主机名称：192.168.31.1  端口：22

**4、putty终端登录**

- 在putty中输入用户名和密码，用户名：root 密码：admin
- 输入命令：

        cat/proc/mtd

        ddif=/dev/mtd0 of=//tmp/all.bin

**5、备份路由器原固件“all.bin”**

- 使用WINSCP，选择SCP链接模式，主机名：192.168.31.1   端口：22   用户名：root    密码：admin；
- 进入tmp目录，将all.bin下载到本地。备份过程相当重要，不要跳过此过程。

**6、刷入breed固件**

- 用WINSCP将压缩包内的breed.bin文件拖到tmp目录；
- 然后在putty终端执行：```mtd -r write /tmp/breed.bin Bootloader；```

- 等待写入完成（出现Rebooting的时候可以多等一下）。大约 5-10秒
  - **7、进入Breed固件**
    
    - 写入完成后，拔掉电源线，把网线一端插入路由器wlan口上，一端插入电脑，使用取卡针按住“复原键”的同时插入电源，等待5-10秒，松开。
    - 在浏览器中输入 192.168.1.1 出现如下界面：（注意：如若长时间登录不上192.168.1.1 ，就把网线插在lan口上，我就是一直不出现恢复控制台，最后插在lan口上就可以使用了）
    
    ![](https://www.right.com.cn/forum/data/attachment/forum/202203/26/151634uiee07o6erbvg0r8.png)**8、刷入新固件**
    
    - 选择“固件更新”，勾选固件，选择压缩包内或者自己下载的固件，点击上传即可。
    
    ![](https://www.right.com.cn/forum/data/attachment/forum/202203/26/151744f334mmu8y92u3mnl.png)等待路由器变蓝色，则路由器已经成功运行，已刷入了相应固件。[小米路由器青春版刷机教程（搬运）-小米无线路由器以及小米无线相关的设备-恩山无线论坛 (right.com.cn)](https://www.right.com.cn/forum/thread-8213443-1-1.html)[小米路由器青春版刷breed后再刷入华硕固件教程 – 威武中国 (v5china.com)](https://www.v5china.com/xiaomi-router-youth-version-of-breed-after-the-brush-brush-into-asus-firmware-tutorial.html)[小米路由器青春版刷华硕固件 – 设计笔记 (biji.io)](https://biji.io/2017/4963.html)[小米路由器青春版刷老毛子 padavan 系列教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1i4411U7Uv/)

刷机过程参考以上文章

以下为固件配置

[SunBK201# 关于某大学校园网共享上网检测机制的研究与解决方案](https://blog.sunbk201.site/posts/crack-campus-network)

[OpenWrt 编译与防检测部署教程 (notion.site)](https://sunbk201public.notion.site/sunbk201public/OpenWrt-f59ae1a76741486092c27bc24dbadc59)

[OpenWrt软路由固件下载与在线定制编译](https://openwrt.ai/?target=ramips%2Fmt76x8&id=xiaomi_miwifi-nano)

```
# 通过 rkp-ipid 设置 IPID
# 若没有加入 rkp-ipid 模块，此部分不需要加入
iptables -t mangle -N IPID_MOD
iptables -t mangle -A FORWARD -j IPID_MOD
iptables -t mangle -A OUTPUT -j IPID_MOD
iptables -t mangle -A IPID_MOD -d 0.0.0.0/8 -j RETURN
iptables -t mangle -A IPID_MOD -d 127.0.0.0/8 -j RETURN
# 由于本校局域网是 A 类网，所以我将这一条注释掉了，具体要不要注释结合你所在的校园网内网类型
# iptables -t mangle -A IPID_MOD -d 10.0.0.0/8 -j RETURN
iptables -t mangle -A IPID_MOD -d 172.16.0.0/12 -j RETURN
iptables -t mangle -A IPID_MOD -d 192.168.0.0/16 -j RETURN
iptables -t mangle -A IPID_MOD -d 255.0.0.0/8 -j RETURN
iptables -t mangle -A IPID_MOD -j MARK --set-xmark 0x10/0x10


# 防时钟偏移检测
iptables -t nat -N ntp_force_local
iptables -t nat -I PREROUTING -p udp --dport 123 -j ntp_force_local
iptables -t nat -A ntp_force_local -d 0.0.0.0/8 -j RETURN
iptables -t nat -A ntp_force_local -d 127.0.0.0/8 -j RETURN
iptables -t nat -A ntp_force_local -d 192.168.0.0/16 -j RETURN
iptables -t nat -A ntp_force_local -s 192.168.0.0/16 -j DNAT --to-destination 192.168.1.1

# 通过 iptables 修改 TTL 值
iptables -t mangle -A POSTROUTING -j TTL --ttl-set 64

```
