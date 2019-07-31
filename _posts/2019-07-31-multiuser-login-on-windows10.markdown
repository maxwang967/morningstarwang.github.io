---
layout: post
title:  "使Windows10支持同时多个用户进行远程桌面连接"
date:   2019-07-31 22:28:28 +0800
categories: solution
---
## 前提条件
- 一个供远程连接的用户(不同于宿主机正在使用的用户)
- 宿主机允许该用户远程访问宿主机
创建新用户的操作在这里不在赘述，允许用户远程连接的操作如下图所示：![](https://res.cloudinary.com/do3aoy3xf/image/upload/v1535694286/morningstarwang.com/%E4%BD%BFWindows10%E6%94%AF%E6%8C%81%E5%90%8C%E6%97%B6%E5%A4%9A%E4%B8%AA%E7%94%A8%E6%88%B7%E8%BF%9B%E8%A1%8C%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E8%BF%9E%E6%8E%A5/TIM%E6%88%AA%E5%9B%BE20180831134312.png)

## 组策略设置
- Win+Q组合键呼出Win10全局搜索，输入“组策略”，选择“编辑组策略”进入“本地组策略编辑器”。然后依次进入“计算机配置->管理模板->Windows组件->远程桌面服务->远程桌面会话主机->连接”菜单。
- 将“将远程桌面服务用户限制到单独的远程桌面服务会话”设置为“已禁用”
- 将“限制连接的数量”设置为“已启用”，连接数设置为2
- 刷新组策略。以管理员身份打开“Windows PowerShell终端”，输入并执行命令：gpupdate /force

## 安装RDPWrap补丁
- 下载RDPWrap补丁：[RDPWrap-v1.6.2.zip](https://github.com/stascorp/rdpwrap/releases/download/v1.6.2/RDPWrap-v1.6.2.zip "https://github.com/stascorp/rdpwrap/releases/download/v1.6.2/RDPWrap-v1.6.2.zip")
- 解压并依次运行install.bat和update.bat
- 运行“RDPConf.exe”，出现如下界面说明已经可以进行多用户同时远程桌面连接：
![](https://res.cloudinary.com/do3aoy3xf/image/upload/v1535695042/morningstarwang.com/%E4%BD%BFWindows10%E6%94%AF%E6%8C%81%E5%90%8C%E6%97%B6%E5%A4%9A%E4%B8%AA%E7%94%A8%E6%88%B7%E8%BF%9B%E8%A1%8C%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E8%BF%9E%E6%8E%A5/TIM%E6%88%AA%E5%9B%BE20180831135710.png)

## 总结
至此，已经完成了所需要的全部配置。Chrome浏览器和Windows Defender可能会误报RDPWrap为恶意文件，请大家放行即可。