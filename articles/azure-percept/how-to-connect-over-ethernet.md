---
title: 通过以太网连接到 Azure Percept DK
description: 本指南向用户介绍如何在通过以太网连接的情况下连接到 Azure Percept DK 设置体验。
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ffc2a2d55bdad670c8323dfc57efad17dec1930
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227083"
---
# <a name="connect-to-azure-percept-dk-over-ethernet"></a>通过以太网连接到 Azure Percept DK

本操作指南将讲解如何通过以太网连接启动 Azure Percept DK 设置体验。 它是[快速入门：设置 Azure Percept DK 并部署第一个 AI 模型](./quickstart-percept-dk-set-up.md)指南的姊妹篇。 请查看下面概述的每个选项，并选择最适合你的环境的选项。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK（[在此处获取](https://go.microsoft.com/fwlink/?linkid=2155270)）
- 基于 Windows、Linux 或 OS X 的主机，具有 Wi-Fi 或以太网功能和 Web 浏览器
- 网络电缆

## <a name="identify-your-dev-kits-ip-address"></a>识别开发工具包的 IP 地址

通过以太网连接运行 Azure Percept DK 设置体验的关键是查找开发工具包的 IP 地址。 本文介绍三个选项：
1. 使用网络路由器
1. 通过 SSH
1. 通过 Nmap 工具

### <a name="from-your-network-router"></a>使用网络路由器
识别开发工具包 IP 地址的最快方式是在网络路由器上查找该地址。
1. 将以太网电缆的一端插入开发工具包，将另一端插入路由器。
1. 打开 Azure Percept DK。
1. 在网络路由器上查找指定访问说明的标签

    以下是路由器不干胶标签的示例

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-01.png" alt-text="网络路由器的示例不干胶标签":::

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-02.png" alt-text="网络路由器的另一个示例不干胶标签":::

1. 在连接到以太网或 Wi-Fi 的计算机上，打开 Web 浏览器。
1. 键入在标签上找到的路由器的浏览器地址。
1. 当系统提示时，输入不干胶标签上所示的路由器的名称和密码。
1. 进入路由器界面后，选择“我的设备”（或根据你的路由器选择类似选项）。
1. 在设备列表中查找 Azure Percept 开发工具包
1. 复制 Azure Percept 开发工具包的 IP 地址

### <a name="via-ssh"></a>通过 SSH
通过 SSH 连接到开发工具包，可以找到开发工具包的 IP 地址。

> [!NOTE]
> 使用 SSH 方法识别开发工具包的 IP 地址需要能够连接到开发工具包的 Wi-Fi 接入点。 如果无法连接，请使用其他方法。

1. 将以太网电缆的一端插入开发工具包，将另一端插入路由器
1. 打开 Azure Percept 开发工具包
1. 通过 SSH 连接到开发工具包。 如需详细了解如何通过 SSH 连接到开发工具包，请参阅[通过 SSH 连接到 Azure Percept DK](./how-to-ssh-into-percept-dk.md)。
1. 若要列出以太网本地网络 IP 地址，请在 SSH 终端窗口中键入以下命令：

    ```bash
    ip a | grep eth1
    ```

    :::image type="content" source="media/how-to-connect-over-ethernet/ssh-local-network-address.png" alt-text="在 SSH 终端中识别本地网络 IP 的示例":::


1. 开发工具包的 IP 地址显示在“inet”之后。 复制 IP 地址。

### <a name="using-the-nmap-tool"></a>使用 Nmap 工具
还可使用在 Web 上找到的免费工具来识别开发工具包的 IP 地址。 在这些说明中，我们将介绍名为 Nmap 的工具。
1. 将以太网电缆的一端插入开发工具包，将另一端插入路由器。
1. 打开 Azure Percept 开发工具包。
1. 在主机上，下载并安装平台 (Windows/Mac/Linux) 所需的[免费 Nmap 安全扫描器](https://nmap.org/download.html)。
1. 获取计算机的“默认网关”- [如何查找默认网关](https://www.noip.com/support/knowledgebase/finding-your-default-gateway/)
1. 打开 Nmap 应用程序 
1. 在“Target（目标）”框中输入默认网关，并在末尾追加 **/24**。 将“Profile（配置文件）”更改为“Quick scan（快速扫描）”，然后选择“Scan（扫描）”按钮。
    
    :::image type="content" source="media/how-to-connect-over-ethernet/nmap-tool.png" alt-text="Nmap 工具输入的示例":::
 
1. 在结果中，找到设备列表中的 Azure Percept 开发工具包 - 类似于 apd-xxxxxxxx
1. 复制 Azure Percept 开发工具包的 IP 地址 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>启动 Azure Percept DK 设置体验
1. 将以太网电缆的一端插入开发工具包，将另一端插入路由器。
1. 打开 Azure Percept 开发工具包。
1. 打开 Web 浏览器，然后粘贴开发工具包的 IP 地址。 系统应该会在浏览器中启动设置体验。

## <a name="next-steps"></a>后续步骤
- [完成设置体验](./quickstart-percept-dk-set-up.md)