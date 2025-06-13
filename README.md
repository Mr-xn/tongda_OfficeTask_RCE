# tongda_OfficeTask_RCE
通达OA OfficeTask udp 2397 端口远程代码执行[RCE](https://mrxn.net/tag/rce)检测工具

tongda_udp_2397_[rce](https://mrxn.net/tag/rce) 工具使用说明如下图所示

![](https://img.mrxn.net/b295d83b42264666bd62f0bc7c599b26.webp)

# 影响版本

影响目前最新版本 13.3.25.416

![](https://img.mrxn.net/4a0ff497b04e4c01a38d943b847bef28.webp)

# 漏洞简述

这个洞已经出来了一段时间了，相关详细分析可以看参考部分的文章，很详细。这里仅仅记录复现并开发工具过程中的部分要点，
工具仅供甲方或蓝方自查检测(根目录生成 login_check_rce_随机字符串.php 打印123456的md5后删除自身)。

总体流程就是向特定端口2397 发送特制UDP报文触发命令执行php代码，从而写入文件造成[RCE](https://mrxn.net/tag/rce)（因为协议端口特殊，大部分WAF都是没有拦截的）。

先看下 Office_Task 服务的进程 `C:\MYOA\bin\OfficeTask.exe`（根据安装磁盘不同路径不同）会打开 `C:\MYOA\logs\Office_Task.log` 日志文件进行读写。

![](https://img.mrxn.net/38518f7d7dc743ac8b4120b9715c0f52.webp)

![](https://img.mrxn.net/304c8c807b734849abcd6c485b202313.webp)

然后在看下 OfficeTask.exe 进程网络相关信息

![](https://img.mrxn.net/f6169390295841188406ddf904440ad0.webp)

可以看到其默认监听在UDP的 `2397` 端口

工具实现也很简单，构造payload向目标特定udp 2397 端口发送即可验证，只是需要注意构造payload的时候需要注意闭合注释相关如完整的 `<?php xxx;/* */?> ` 防止出现影响多次利用（实战很重要）。

工具检测过程中，可以查看安装目录 logs 目录下的 `Office_Task.log` 日志文件

以及生成的测试文件

![](https://img.mrxn.net/56e27ba0536a43fcaf7e039c875a3f14.webp)

访问文件，成功打印 123456 的md5值 并删除自身

![](https://img.mrxn.net/b02b7691a7a6468f94b7337f6c90a344.webp)


# 参考

- https://mp.weixin.qq.com/s/SkUghnlVQNd6Z65Ubc-KwA
- https://mrxn.net/hacktools/tongda-OfficeTask-RCE-tools.html
