> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/Qe-8yHFaWOcsdbphydt-CQ)

一个每日分享渗透小技巧的公众号![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPTQKiaXksbZia7PmHLPX2vnCWsznInTj3b9TFYtTDIYG6lDGJZYYSv72NsVWF24Kjlo4MT29tEOQSg/640?wx_fmt=png)

  

  

大家好，这里是 **大余安全** 的第 **158** 篇文章，本公众号会每日分享攻防渗透技术给大家。

靶机地址：https://www.hackthebox.eu/home/machines/profile/192

靶机难度：初级（4.5/10）

靶机发布日期：2019 年 6 月 12 日

靶机描述：

Writeup is an easy difficulty Linux box with DoS protection in place to prevent brute forcing. A CMS is found, and contains a SQL injection vulnerability, which is leveraged to gain user credentials. The user is found to be in a non-default group, which gives him write access to part of the PATH. A path hijacking results in escalation of privileges to root.

请注意：对于所有这些计算机，我是通过平台授权允许情况进行渗透的。我将使用 Kali Linux 作为解决该 HTB 的攻击者机器。这里使用的技术仅用于学习教育目的，如果列出的技术用于其他任何目标，我概不负责。

![](https://mmbiz.qpic.cn/mmbiz_png/YK9e7vHy9IQATwibKVicOpXZibX8VOvBrnF8UXRGvcibFy79c4NzQ5qiaZYAialtVicUHCxUcIPzXM0K4aziaQHEPjTDIw/640?wx_fmt=png)

![](https://mmbiz.qpic.cn/mmbiz_png/ZrqZaezpWclmao6Vp2LSrkuD0NTO9TiclXmiaWSh0NibqeKL1xJ4qBoJbPODkzJ3g0OvTdUGll3Otz9978tOYib32Q/640?wx_fmt=png)

一、信息收集

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WvGB9PMibFEvjM2XUL6JIqgux34N2MqpgWYKGP0UDic9vH6v1licNmjicibQ/640?wx_fmt=png)

可以看到靶机的 IP 是 10.10.10.138...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WuWcz2RBVQqNZMPJTmpKchsgUbsdkiakzhiaXibRFmxqdjNgrXL76UsVAQ/640?wx_fmt=png)

nmap 发现开放了 apache 和 SSH 服务...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WDVW4BP4TTthskibAP17rTxYppN5h5dorAj8mz2yYzCQACdlq3mg91CQ/640?wx_fmt=png)

浏览到端口 80，可看到一个复古风格的页面...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WlvcUZibI8eq8ADibgXfz99B2sAcMSPuU6HiaA6dBRsw2snOWuzPwhd7UQ/640?wx_fmt=png)

每次 apache 我都会查看 robots 文本，提示了 writeup 目录...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7W11kyn2NJ57m256YBKA2DJm2UvBnVts5iaSnS8sZ0yDqIZJvcZuArubg/640?wx_fmt=png)

进入该目录，开始枚举信息...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WLHofN7uHJYCrqUjzK9wsawmQH3gxY7xgLDbkbFiaBtWOCloic12kcsdA/640?wx_fmt=png)

查看前段源码，发现了这是 CMS Made Simple 框架的服务，版本最新 2019 年...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WjVK57bC2Hyic8KutadbFH18amLR40QFmQM9bmiarACNKoYAOHjYbF79Q/640?wx_fmt=png)

根据提示直接开始 google，查找到了相关的源码？

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WOTibPZibDqoh6wjeRqbS5cUDYKOdn9eZSh8sko2LkhY9ZdsDic0EddP5w/640?wx_fmt=png)

到下载页面，果然有源码包....

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WxpWhKQic96gpFCPjbCPBe3WGVTg6muYNvHX1IiaFDIfgIfh8HcjCWOGg/640?wx_fmt=png)

往下滑，有源码包部署好的页面地址，省的我下载包部署，我只需要查看下即可...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7W1ublbksgUyO5x78H9SichBoeEp6CibtMGjnic1wbFPriaOzY9Jh5ibFN89Q/640?wx_fmt=png)

访问后，发现底层存在很多目录，doc 中发现了 CHANGELOG 页面日志信息....

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WLMPVPUVafSZ3nDiawpCH4YYQ6ZVicj9O5c6P8rJ7reIs1xWN0F2W0TGQ/640?wx_fmt=png)

查看到了版本信息...2.2.9.1 的

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WmEEToyUmkLJRnTiaBRE2JheIwnJzUQniamZibnTmLiaUnMn0zbmzhib8C2A/640?wx_fmt=png)

通过本地查找相关的漏洞信息... 可利用 CVE-2019-9053 漏洞 46635EXP 进行提权...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WqkV9KicBJj9paP19DaPg48AoB76DYkaWO5Bpkf0bx6j8GdSZjM8PwaQ/640?wx_fmt=png)

首先放到本地目录下，执行后发现了需要 termcolor 模块执行.....PIP 下载即可...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WTtdviaDDJcyVYlHc5APN7ka0LpWv06H7EAqfWMFiazmCHVfBpic7gOHhQ/640?wx_fmt=png)

```
python 46635.py -u http://10.10.10.138/writeup/ --crack -w /opt/dayuHTB/dayuHackTheBox/dayuAriekei/rockyou.txt
```

按照脚本执行即可... 通过 EXP 和 rockyou 密码本，爆破获得了用户名密码...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WWOnrbKDbrpc8a4SRjvicZJS2L3p8YRK7vq4bPw6ia7oHC3nPoKcbBPhg/640?wx_fmt=png)

SSH 服务成功登录了 jkr 用户，并获得了 user_flag 信息...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7WewGGWF83Q2C0hu8EtcBHeS8ktm1tCeTkAicrviaESB5Ghbx5XnPeuSWw/640?wx_fmt=png)

我这里上传了 pspy 枚举目前靶机进程状态... 成功上传

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7W49SgnDsn4bMW6zNpPdgB2LFCpQM56dmlFfSBveQKicKRORYicWvubVYg/640?wx_fmt=png)

开始查看发现 sshd？？，我重新开启另外窗口登录 ssh 后，发现每次登录都会运行一次 bin 目录下的 run-parts 文件... 以 root 权限执行... 那就简单了...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7Ws9cLo1jBBYtfY8GJibOnqjk1WyoRDUR0ndXwsQY7oJWUmyV1LqMaecA/640?wx_fmt=png)

可看到 jkr 在一个名为 staff 的组中...staff 成员可以写 / usr/local/bin...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7W6ib5gZo61bV5YNVxzfG5GUznluJd1h4uqattQVvcja3NPy5YW3OGtnQ/640?wx_fmt=png)

默认情况下 run-parts 位于 / bin 中...

创建 run-parts，并写入简单的 shell，在执行后写入 / etc/passwd 新的用户名密码具有 root 权限...

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPBAYicTIN9j00KYB6kXnL7W0icJn07cmQkwonv6yYQNLHVTXDRmwdyK5GJ2audkvTiasYVfSk00Nh6g/640?wx_fmt=png)

通过成功写入的新用户... 直接 su 成功登录，获得了 root 权限....

读取到了 root_flag 信息...

这台靶机简单常规...

获取 apache 框架信息 -- 寻找相关的 EXP-- 利用 EXP 获得用户名密码 --SSH 登录后 pspy32 枚举进程 -- 最后写 shell 提权...

由于我们已经成功得到 root 权限查看 user 和 root.txt，因此完成这台初级的靶机，希望你们喜欢这台机器，请继续关注大余后期会有更多具有挑战性的机器，一起练习学习。

如果你有其他的方法，欢迎留言。要是有写错了的地方，请你一定要告诉我。要是你觉得这篇博客写的还不错，欢迎分享给身边的人。

![](https://mmbiz.qpic.cn/mmbiz_png/YK9e7vHy9IQATwibKVicOpXZibX8VOvBrnF8UXRGvcibFy79c4NzQ5qiaZYAialtVicUHCxUcIPzXM0K4aziaQHEPjTDIw/640?wx_fmt=png)

如果觉得这篇文章对你有帮助，可以转发到朋友圈，谢谢小伙伴~

![](https://mmbiz.qpic.cn/mmbiz_png/c5xrRn4430AnqkfAJc38Vpnc5XiaADLTjiciciaibYU4EHw3Nuh7YMtuB0hz3sb8Em9iatt5skAsibuuysPLdLY5LtWOw/640?wx_fmt=png)

![](https://mmbiz.qpic.cn/mmbiz_png/p3lIbvldZiabdI5iaCb3icRhtygUuo2sp6Hcdq0ANlpy5W3gL628uq032jsoVnGnl6HdGrgDXjfazFtkp6IInibDdQ/640?wx_fmt=png)

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPqjaFWwyrrhiciahSpOibxqKvSIFX0iaPcG00CjYIwQDwIDeIicmFMlOVNyhWYVSE8pJK566UK3YOUNWQ/640?wx_fmt=png)

随缘收徒中~~ **随缘收徒中~~** **随缘收徒中~~**

欢迎加入渗透学习交流群，想入群的小伙伴们加我微信，共同进步共同成长！

![](https://mmbiz.qpic.cn/mmbiz_png/ndicuTO22p6ibN1yF91ZicoggaJJZX3vQ77Vhx81O5GRyfuQoBRjpaUyLOErsSo8PwNYlT1XzZ6fbwQuXBRKf4j3Q/640?wx_fmt=png)  

大余安全

一个全栈渗透小技巧的公众号

![](https://mmbiz.qpic.cn/mmbiz_png/O7dWXt4o5KPTQKiaXksbZia7PmHLPX2vnCSsnsc7MHh257oYRic1MOT8qibABNUEnTq9DUL7QBwnS52EheJf4m8iaTQ/640?wx_fmt=png)