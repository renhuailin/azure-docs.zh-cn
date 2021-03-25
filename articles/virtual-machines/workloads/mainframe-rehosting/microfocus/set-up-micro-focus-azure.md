---
title: 在 Azure 上安装 Micro Focus Enterprise Server 5.0 和 Enterprise Developer 5.0 | Microsoft Docs
description: 本文介绍如何在 Microsoft Azure 上安装 Micro Focus Enterprise Server 5.0 和 Enterprise Developer 5.0。
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 0d0a7d9060d751dbf05d39545b332c13010cbfae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553572"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>在 Azure 上安装 Micro Focus Enterprise Server 5.0 和 Enterprise Developer 5.0

本文介绍如何在 Microsoft Azure 上安装 [Micro Focus Enterprise Server 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) 和 [Micro Focus Enterprise Developer 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/)。

Azure 上的常见工作负荷是开发和测试环境。 这种情况很常见，因为它非常经济高效且易于部署和拆卸。 与 Enterprise Server 配合工作，Micro Focus 创建了可供使用的最大的大型机重新托管平台之一。 你可以使用 Windows 或 Linux 虚拟机 (VM) 在 Azure 中更经济实惠的 x86 平台上运行 z/OS 工作负荷。

此安装程序使用已安装 Microsoft SQL Server 2017 的 Azure VM，该 VM 运行 Azure 市场提供的 Windows Server 2016 映像。 此安装程序还适用于 Azure Stack。

Enterprise Server 的相应开发环境是 Enterprise Developer，后者在 Microsoft Visual Studio 2017 或更高版本、Visual Studio Community（免费下载）或 Eclipse 上运行。 本文介绍如何使用安装了 Visual Studio 2017 或更高版本的 Windows Server 2016 虚拟机来部署它。

## <a name="prerequisites"></a>先决条件

在开始之前，请先查看以下先决条件：

-   Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

-   Micro Focus 软件和有效许可证（或试用许可证）。 如果你是现有的 Micro Focus 客户，请联系你的 Micro Focus 代表。 否则，请[请求试用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

-   获取 [Enterprise Server 和 Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/) 的文档。

    > [!Note]
    > 有几个选项可用于控制对 VM 的访问：
    > -   最佳做法是安装 [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/)。
    > -   [站点到站点虚拟专用网络 (VPN)](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) 隧道。
    > -   jumpbox VM。

## <a name="install-enterprise-server"></a>安装 Enterprise Server

1.  为了提高安全性和可管理性，请考虑创建仅适用于此项目的新资源组，例如 **RGMicroFocusEntServer**。 使用 Azure 中的名称的第一部分来选择资源类型，使之更易于在列表中被发现。

2.  创建虚拟机。 从 Azure 市场中选择所需的虚拟机和操作系统。 下面是建议的安装程序：

    -   **Enterprise Server：** 选择安装了 Windows Server 2016 和 SQL Server 2017 的 **ES2 v3 VM**（带有 2 个 vCPU 和 16 GB 内存）。 此映像在 Azure 市场中提供。 Enterprise Server 还可以使用 Azure SQL 数据库。

    -   **Enterprise Developer：** 选择安装了 Windows 10 和 Visual Studio 的 **B2ms VM**（带有 2 个 vCPU 和 8 GB 内存）。 此映像在 Azure 市场中提供。

3.  在“基本信息”部分输入用户名和密码。 选择要用于 VM 的“订阅”和“位置/区域”。 选择 **RGMicroFocusEntServer** 作为资源组。

4.  将两个 VM 放入同一虚拟网络中，使它们可以相互通信。

5.  接受其余设置的默认值。 请记住为这些 VM 的管理员创建的用户名和密码。

6.  创建虚拟机后，在 Enterprise Server 计算机上打开适用于 HTTP 的入站端口 **9003、86**、**80** 和适用于远程桌面协议 (RDP) 的端口 **3389**，并在 Developer 计算机上打开端口 **3389**。

7.  若要登录到 Enterprise Server 虚拟机，请在 Azure 门户中选择“ES2 v3 VM”。 转到“概述”部分，选择“连接”以启动 RDP 会话。 使用为 VM 创建的凭据登录。

8.  从 RDP 会话加载以下两个文件。 由于你在使用 Windows，因此可以将文件拖放到 RDP 会话中：

    -   `es\_50.exe`，这是 Enterprise Server 安装文件。

    -   `mflic`，这是相应的许可证文件 - Enterprise Server 在没有它的情况下不会进行加载。

9.  双击此文件开始安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。

    ![屏幕截图显示了“Micro Focus Enterprise Server”对话框，你可以在该对话框中开始安装。](media/install-image-1.png)

    安装完成后，会显示以下消息：

    ![屏幕截图显示“Micro Focus Enterprise Server”对话框中的成功消息。](media/install-image-2.png)

 ### <a name="check-for-updates"></a>检查更新

安装后，请务必检查是否有其他更新，因为许多必备组件（例如 Microsoft C++ Redistributable 和 .NET Framework）随 Enterprise Server 一起安装。

### <a name="upload-the-license"></a>上传许可证

1.  启动“Micro Focus 许可证管理”。

2.  选择“开始”\>“Micro Focus 许可证管理器”\>“许可证管理”，然后单击“安装”选项卡。   选择要上传的许可证格式类型：许可证文件或 16 个字符的许可证代码。 例如，对于文件，请在“许可证文件”中浏览到以前上传到 VM 的 *`mflic` 文件，然后选择“安装许可证”。 

    ![屏幕截图显示了“Micro Focus 许可证管理”对话框，你可以在该对话框中选择“安装许可证”。](media/install-image-3.png)

3.  验证 Enterprise Server 是否已加载。 尝试使用以下 URL 在浏览器中启动“Enterprise Server 管理”站点：`http://localhost:86/`。 此时会显示“Enterprise Server 管理”页，如下所示。

    ![“Enterprise Server 管理”页](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>在开发人员计算机上安装 Enterprise Developer

1.  选择以前创建的资源组（例如 **RGMicroFocusEntServer**），然后选择开发人员映像。

2.  若要登录到虚拟机，请转到“概述”部分，选择“连接”。 此登录会启动 RDP 会话。 使用为 VM 创建的凭据登录。

3.  从 RDP 会话中加载以下两个文件（根据需要进行拖放）：

    -   `edvs2017.exe`，这是 Enterprise Server 安装文件。

    -   `mflic`，这是相应的许可证文件（Enterprise Developer 在没有它的情况下不会进行加载）。

4.  双击 **edvs2017.exe** 文件开始安装。 在第一个窗口中，选择安装位置并接受最终用户许可协议。 根据需要选择“安装 Rumba 9.5”以安装此终端仿真器。

    ![“Micro Focus Enterprise Developer for Visual Studio 2017 安装”对话框](media/install-image-5.png)

5.  安装完成后，会显示以下消息：

    ![“安装成功”消息](media/install-image-6.png)

6.  启动 Micro Focus 许可证管理器，像对 Enterprise Server 所做的那样。 选择“开始”\>“Micro Focus 许可证管理器”\>“许可证管理”，单击“安装”选项卡。   

7.  选择要上传的许可证格式类型：许可证文件或 16 个字符的许可证代码。 例如，对于文件，请在“许可证文件”中浏览到以前上传到 VM 的 `mflic` 文件，然后选择“安装许可证”。 

    ![“Micro Focus 许可证管理”对话框](media/install-image-7.png)

如果 Enterprise Developer 已加载，则表明在 Azure 上部署 Micro Focus 开发和测试环境的工作已完成！

后续步骤

-   [设置 BankDemo 应用程序](./demo.md)

-   [在 Docker 容器中运行 Enterprise Server](./run-enterprise-server-container.md)

-   [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)