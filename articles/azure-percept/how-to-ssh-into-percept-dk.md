---
title: 通过 SSH 连接到 Azure Percept DK
description: 了解如何使用 PuTTY 通过 SSH 连接到 Azure Percept DK
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: b22a359e112d7d0d3caae76acd20a7e3fd3ada01
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226235"
---
# <a name="connect-to-azure-percept-dk-over-ssh"></a>通过 SSH 连接到 Azure Percept DK

按照以下步骤通过 OpenSSH 或 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 设置与 Azure Percept DK 的 SSH 连接。

## <a name="prerequisites"></a>先决条件

- 基于 Windows、Linux 或 OS X 的主计算机，具有 Wi-Fi 功能
- SSH 客户端（请参阅安装指南的下一部分）
- Azure Percept DK（开发工具包）
- 在 [体验 Azure Percept DK 设置](./quickstart-percept-dk-set-up.md) 过程中创建的 SSH 帐户

## <a name="install-your-preferred-ssh-client"></a>安装首选 SSH 客户端

如果主计算机运行 Linux 或 OS X，则 SSH 服务将包含在这些操作系统中，无需单独的客户端应用程序即可运行。 有关如何运行 SSH 服务的详细信息，请查看操作系统产品文档。

如果主计算机运行的是 Windows，则有两个 SSH 客户端选项供你选择：OpenSSH 和 PuTTY。

### <a name="openssh"></a>OpenSSH

Windows 10 包括名为 OpenSSH 的内置 SSH 客户端，可以在命令提示符中使用简单的命令来运行它。 如果 OpenSSH 可用，建议将其与 Azure Percept 配合使用。 若要检查 Windows 计算机是否已安装 OpenSSH，请按照以下步骤操作：

1. 请转到“开始” -> “设置” 。

1. 选择“应用”  。

1. 在“应用和功能”下，选择“可选功能” 。

1. 在“已安装的功能”搜索栏中键入“OpenSSH 客户端” 。 如果显示 OpenSSH，则表示客户端已安装，你可以前往下一节。 如果未看到 OpenSSH，请选择“添加功能”。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="显示 OpenSSH 安装状态的设置的屏幕截图。":::

1. 选择“OpenSSH 客户端”并选择“安装” 。 现在，你可以前往下一节。 如果无法在计算机上安装 OpenSSH，请按照以下步骤安装 PuTTY（第三方 SSH 客户端）。

### <a name="putty"></a>PuTTY

如果 Windows 计算机不包括 OpenSSH，建议使用 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)。 若要下载并安装 PuTTY，请完成以下步骤：

1. 请转到 [PuTTY 下载页](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)。

1. 在“程序包文件”下，选择 32 位或 64 位 .msi 文件以下载安装程序。 如果不确定要选择哪个版本，请查看[常见问题解答](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit)。

1. 选择安装程序以开始执行安装过程。 根据需要，按照提示操作。

1. 恭喜！ 你已成功安装 PuTTY SSH 客户端。

## <a name="initiate-the-ssh-connection"></a>启动 SSH 连接

1. 打开 Azure Percept DK。

1. 如果开发工具包已通过以太网或 Wi-Fi 连接到网络，请跳到下一步骤。 如果未连接网络，请将主计算机直接连接到开发工具包的 Wi-Fi 接入点。 就像连接到任何其他 Wi-Fi 网络一样，打开计算机上的“网络和 Internet 设置”，选择以下网络，并在出现提示时输入网络密码：

    - **网络名称**：根据开发工具包的操作系统版本，Wi-Fi 接入点的名称为 scz-xxxx 或 apd-xxxx（其中“xxxx”是开发工具包的 MAC 地址的最后四位数） 
    - **密码**：可在开发工具包随附的欢迎卡上找到

    > [!WARNING]
    > 连接到 Azure Percept DK Wi-Fi 接入点时，主计算机将暂时断开与 Internet 的连接。 正在进行的视频会议通话、Web 流式处理或其他基于网络的体验将中断。

1. 根据 SSH 客户端完成 SSH 连接过程。

### <a name="using-openssh"></a>使用 OpenSSH

1. 打开命令提示符（“开始” -> “命令提示符”）。 

1. 在命令提示符中输入以下内容：

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    如果计算机连接到开发工具包的 Wi-Fi 接入点，则 IP 地址将为 10.1.1.1。 如果开发工具包通过以太网连接，请使用设备的本地 IP 地址，可以从以太网路由器或中心获取该地址。 如果开发工具包是通过 Wi-Fi 连接的，则必须使用在 [Azure Percept DK 安装体验](./quickstart-percept-dk-set-up.md)期间分配给开发工具包的 IP 地址。

    > [!TIP]
    > 如果开发工具包已连接到 Wi-Fi 网络但你不知道其 IP 地址，请转到 Azure Percept Studio 并[打开设备的视频流](./how-to-view-video-stream.md)。 视频流浏览器标签页中的地址栏将显示设备的 IP 地址。

1. 根据提示输入 SSH 密码。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="OpenSSH 命令提示符登录的屏幕截图。":::

1. 如果这是你首次通过 OpenSSH 连接到开发工具包，系统可能还会提示你接受主机的密钥。 输入“是”以接受密钥。

1. 恭喜！ 你已成功通过 SSH 连接到开发工具包。

### <a name="using-putty"></a>使用 PuTTY

1. 打开 PuTTY。 在“PuTTY 配置”窗口中输入以下内容，然后选择“打开”以通过 SSH 连接到开发工具包 ：

    1. 主机名：[IP 地址]
    1. 端口：22
    1. 连接类型：SSH

    主机名是开发工具包的 IP 地址。 如果计算机连接到开发工具包的 Wi-Fi 接入点，则 IP 地址将为 10.1.1.1。 如果开发工具包通过以太网连接，请使用设备的本地 IP 地址，可以从以太网路由器或中心获取该地址。 如果开发工具包是通过 Wi-Fi 连接的，则必须使用在 [Azure Percept DK 安装体验](./quickstart-percept-dk-set-up.md)期间分配给开发工具包的 IP 地址。

    > [!TIP]
    > 如果开发工具包已连接到 Wi-Fi 网络但你不知道其 IP 地址，请转到 Azure Percept Studio 并[打开设备的视频流](./how-to-view-video-stream.md)。 视频流浏览器标签页中的地址栏将显示设备的 IP 地址。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="“PuTTY 配置”窗口的屏幕截图。":::

1. 将打开 PuTTY 终端。 出现提示时，请在终端中输入 SSH 用户名和密码。

1. 恭喜！ 你已成功通过 SSH 连接到开发工具包。

## <a name="next-steps"></a>后续步骤

通过 SSH 连接到 Azure Percept DK 后，可以执行各种任务，包括[设备故障排除](./troubleshoot-dev-kit.md)和 [USB 更新](./how-to-update-via-usb.md)。