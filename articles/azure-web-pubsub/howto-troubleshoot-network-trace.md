---
title: 如何收集网络跟踪
description: 了解如何获取网络跟踪来进行故障排除
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 6e92ab3c27113aad44b7bed2815b9c5050afd0ac
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166860"
---
# <a name="how-to-collect-a-network-trace"></a>如何收集网络跟踪

遇到问题时，网络跟踪有时可以提供很多有用的信息。 本操作指南介绍了收集网络跟踪的选项。

> [!WARNING]
> 网络跟踪包含应用发送的每条消息的全部内容。 切勿将来自生产应用的原始网络跟踪发布到 GitHub 等公共论坛。

## <a name="collect-a-network-trace-with-fiddler"></a>使用 Fiddler 收集网络跟踪

Fiddler 是一个功能强大的工具，用于收集 HTTP 跟踪。 从 [telerik.com/fiddler](https://www.telerik.com/fiddler) 安装并启动该工具，然后运行你的应用程序，重现此问题。 Fiddler 适用于 Windows、macOS 和 Linux。 

如果使用 HTTPS 进行连接，则需执行一些额外的步骤来确保 Fiddler 可以解密 HTTPS 流量。 有关详细信息，请参阅 [Fiddler 文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)。

收集了跟踪后，可以通过从菜单栏中选择“文件” > “保存” > “所有会话”来导出跟踪。

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>使用 tcpdump 收集网络跟踪（仅限 macOS 和 Linux）

此方法适用于所有应用程序。

可以使用 tcpdump 收集原始 TCP 跟踪，具体方法是在命令行界面中运行以下命令。 如果出现权限错误，你可能需要 `root` 权限或为命令添加前缀 `sudo`：

```console
tcpdump -i [interface] -w trace.pcap
```

将 `[interface]` 替换为要捕获的网络接口。 通常，这类似于 `/dev/eth0`（对于标准以太网接口）或 `/dev/lo0`（对于 localhost 流量）。 有关详细信息，请参阅主机系统上的 `tcpdump` 手册页。

```console
man tcpdump
```

## <a name="collect-a-network-trace-in-the-browser-browser-based-apps-only"></a>在浏览器中收集网络跟踪（仅限基于浏览器的应用程序）

大多数浏览器开发人员工具都有一个“网络”选项卡，你可以使用该选项卡捕获浏览器与服务器之间的网络活动。 

### <a name="microsoft-edge-chromium"></a>Microsoft Edge (Chromium)

1. 打开 [DevTools](https://docs.microsoft.com/microsoft-edge/devtools-guide-chromium/)
    * 选择 `F12` 
    * 选择 `Ctrl`+`Shift`+`I` \(Windows/Linux\) 或 `Command`+`Option`+`I` \(macOS\)
    * 依次选择 `Settings and more`、`More Tools > Developer Tools`  
1. 选择 `Network` 选项卡
1. 如果需要，请刷新页面，并重现此问题
1. 在工具栏中选择 `Export HAR...` 以将跟踪导出为“HAR”文件

    :::image type="content" source="./media/howto-troubleshoot-network-trace/edge-export-network-trace.png" alt-text="使用 Microsoft Edge 收集网络跟踪":::

### <a name="google-chrome"></a>Google Chrome

1. 打开 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)
    * 选择 `F12` 
    * 选择 `Ctrl`+`Shift`+`I` \(Windows/Linux\) 或 `Command`+`Option`+`I` \(macOS\)  
    * 依次选择 `Customize and control Google Chrome`、`More Tools > Developer Tools`
1. 选择 `Network` 选项卡
1. 如果需要，请刷新页面，并重现此问题
1. 在工具栏中选择 `Export HAR...` 以将跟踪导出为“HAR”文件

    :::image type="content" source="./media/howto-troubleshoot-network-trace/chrome-export-network-trace.png" alt-text="使用 Google Chrome 收集网络跟踪":::

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. 打开 [Firefox 开发人员工具](https://developer.mozilla.org/en-US/docs/Tools)
    * 选择 `F12`
    * 选择 `Ctrl`+`Shift`+`I` \(Windows/Linux\) 或 `Command`+`Option`+`I` \(macOS\) 
    * 依次选择 `Open menu`、`Web Developer > Toggle Tools`
1. 选择 `Network` 选项卡
1. 如果需要，请刷新页面，并重现此问题
1. 右键单击请求列表中的任意位置，然后选择“全部保存为 HAR”（Save All As HAR）

    :::image type="content" source="./media/howto-troubleshoot-network-trace/firefox-export-network-trace.png" alt-text="使用 Mozilla Firefox 收集网络跟踪":::

### <a name="safari"></a>Safari

1. 打开 [Web 开发工具](https://developer.apple.com/safari/tools/)
    * 选择 `Command`+`Option`+`I`
    * 选择 `Developer` 菜单，然后选择 `Show Web Inspector` 
1. 选择 `Network` 选项卡
1. 如果需要，请刷新页面，并重现此问题
1. 右键单击请求列表中的任意位置，然后选择“全部保存为 HAR”（Save All As HAR）