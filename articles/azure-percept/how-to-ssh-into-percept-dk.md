---
title: 通过 SSH 连接到 Azure Percept 深色
description: 了解如何通过 PuTTY 通过 SSH 连接到 Azure Percept 深色
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8d150228be2cf6deff3bc2fd0a0599cca70d24ac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661877"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>通过 SSH 连接到 Azure Percept 深色

按照以下步骤设置与 Azure Percept 深色到 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)的 SSH 连接。

## <a name="prerequisites"></a>先决条件

- 具有 Wi-Fi 功能的基于 Windows、Linux 或 OS X 的主机计算机
- SSH 客户端
    - 如果您的主计算机运行的是 Windows，则 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 是有效的 SSH 客户端，并将在本指南中使用。
    - 如果您的主计算机运行 Linux 或 OS X，则 SSH 服务将包含在这些操作系统中，并且无需单独的客户端应用程序即可运行。 有关如何运行 SSH 服务的详细信息，请查看操作系统产品文档。
- Azure Percept 深色

## <a name="initiate-the-ssh-connection"></a>启动 SSH 连接

1. 打开 Azure Percept 深色 (开发工具包) 

1. 如果开发工具包已通过以太网或 Wi-fi 连接到网络，请跳到下一步。 否则，请将主机计算机直接连接到开发工具包的 Wi-Fi 访问点，就像连接到任何其他 Wi-Fi 网络一样：
    - **网络名称**：scz-xxxx（其中，“xxxx”是开发工具包的 MAC 网络地址的最后四位数）
    - **密码**：可在开发工具包随附的“欢迎使用”卡上找到

    > [!WARNING]
    > 连接到 Azure Percept 深色 Wi-Fi 访问点时，主计算机将暂时失去与 Internet 的连接。 活动视频会议呼叫、web 流式处理或其他基于网络的体验将中断，直到完成 Azure Percept 深色的第3步。

1. 打开 PuTTY。 输入以下内容，并单击 " **打开** " 以通过 SSH 连接到 devkit：

    1. 主机名：10.1.1。1
    1. 端口：22
    1. 连接类型： SSH

    > [!NOTE]
    > **主机名称** 是设备的 IP 地址。 如果开发工具包已连接到开发工具包的 Wi-Fi 访问点，则 IP 地址将为10.1.1.1。 如果开发工具包通过以太网连接，请使用设备的本地 IP 地址，该地址可从以太网路由器或集线器获取。 如果你的设备通过 Wi-fi 进行连接，则必须使用在 [Azure PERCEPT 深色的](./quickstart-percept-dk-set-up.md)使用中提供的 IP 地址。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="影像.":::

1. 登录到 PuTTY 终端。 如果在 OOBE 期间设置了 SSH 用户名和密码，请在出现提示时输入这些登录凭据。 否则，请输入以下内容：  

    1. 登录身份： root
    1. 密码：p@ssw0rd

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/putty-terminal.png" alt-text="PuTTY 终端窗口。":::  

## <a name="next-steps"></a>后续步骤

成功通过 SSH 连接到 Azure Percept 深色后，你可以执行各种任务，包括故障排除、USB 更新以及运行 DiagTool 或 SoftAP 工具。


