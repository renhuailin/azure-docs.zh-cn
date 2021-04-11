---
title: 在 Azure 上的 Docker 容器中运行 Micro Focus Enterprise Server 5.0 | Microsoft Docs
description: 本文介绍如何在 Microsoft Azure 上的 Docker 容器中运行 Micro Focus Enterprise Server 5.0。
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
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 146fc59704719fd3aebb03b9b90a176221beea10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950682"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>在 Azure 上的 Docker 容器中运行 Micro Focus Enterprise Server 5.0

可在 Azure 上的 Docker 容器中运行 Micro Focus Enterprise Server 5.0。 本教程将告诉你方法。 它使用 Windows CICS（客户信息控制系统）acctdemo 演示 Enterprise Server。

Docker 增加了应用程序的可迁移性和隔离性。 例如，可以将 Docker 映像从一个 Windows 虚拟机 (VM) 导出到另一个虚拟机上运行，或从存储库导出到带有 Docker 的 Windows Server。 Docker 映像以相同的配置在新位置运行，而无需安装 Enterprise Server。 它是映像的一部分。 许可注意事项仍适用。

本教程从 Azure 市场安装带容器的 Windows Server 2016 Datacenter VM。 此 VM 包含 Docker 18.09.0。 下面的步骤演示了如何部署并运行容器，然后使用 3270 仿真器连接到该容器。

## <a name="prerequisites"></a>先决条件

在开始之前，请先查看以下先决条件：

-   Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

-   Micro Focus 软件和有效许可证（或试用许可证）。 如果你是现有的 Micro Focus 客户，请联系你的 Micro Focus 代表。 否则，请[请求试用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

    > [!Note] 
    > Docker 演示文件包含在 Enterprise Server 5.0 中。 本教程使用 ent\_server\_dockerfiles\_5.0\_windows.zip。 从访问 Enterprise Server 安装文件的同一位置访问它，或者转到 Micro Focus 开始。

-   [Enterprise Server 和 Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22) 的文档。

## <a name="create-a-vm"></a>创建 VM

1.  保护 ent\_server\_dockerfiles\_5.0\_windows.zip 文件中的媒体。 保护 ES-Docker-Prod-XXXXXXXX.mflic 许可文件（生成 Docker 映像所需）。

2.  创建 VM。 为此，请打开 Azure 门户，从左上角菜单中选择“创建资源”，然后按 Windows Server 操作系统进行筛选。 在结果中，选择“Windows Server”。 在下一屏幕中，选择“带容器的 Windows Server 2016 Datacenter”。

    ![Azure 门户搜索结果屏幕截图](./media/run-image-1.png)

3.  若要配置 VM 的属性，请选择实例详细信息：

    1.  选择 VM 大小。 对于本教程，请考虑使用标准 DS2\_v3 VM，其中包含 2 个 vCPU 和 16 GB 内存。

    2.  选择要部署到的区域和资源组 。

    3.  对于“可用性选项”，请使用默认设置。

    4.  对于“用户名”，请键入要使用的管理员帐户和密码。

    5.  请确保打开端口 3389 RDP。 仅此端口需要公开，因此你可以登录 VM。 然后，接受所有默认值，然后单击“查看 + 创建”。

    ![“创建虚拟机”窗格的屏幕截图](./media/run-image-2.png)

4.  等待部署完成（几分钟）。 将出现一条消息，指出已创建 VM。

5.  选择“转到资源”以转到 VM 的“概述”边栏选项卡 。

6.  在右侧选择“连接”。 右侧将显示“连接到虚拟机”选项。

7.  选择“下载 RDP 文件”按钮下载允许你附加到 VM 的远程桌面协议 (RDP) 文件。

8.  下载完文件后，打开它并键入为 VM 创建的用户名和密码。

    > [!Note]    
    > 不要使用企业凭据登录。 （RDP 客户端假设你可能想要使用它们。 但请勿使用。）

9.  选择“更多选择”，然后选择 VM 凭据。

此时，VM 正在运行，并通过 RDP 附加。 你已登录并准备好进行下一步。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>创建沙盒目录并上传 zip 文件

1.  在 VM 上创建一个目录，你可以在其中上传演示和许可证文件。 例如 C:\\Sandbox。

2.  将 ent\_server\_dockerfiles\_5.0\_windows.zip 和 ES-Docker-Prod-XXXXXXXX.mflic 文件上传到你创建的目录 。

3.  将 zip 文件的内容提取到提取过程创建的 ent\_server\_dockerfiles\_5.0\_windows 目录中。 该目录包括一个自述文件（.html 和 .txt 文件）和两个子目录 EnterpriseServer 和 Examples 。

4.  将 ES-Docker-Prod-XXXXXXXX.mflic 复制到 C:\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\EnterpriseServer and C:\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\Examples\\CICS 目录。  
      
    > [!Note]
    > 请确保将许可文件复制到这两个目录。 它们是 Docker 生成步骤所必需的，以确保映像获得了正确的许可。

## <a name="check-docker-version-and-create-base-image"></a>检查 Docker 版本并创建基础映像

> [!Important]  
> 创建相应的 Docker 映像需要两个步骤。 首先，创建 Enterprise Server 5.0 基础映像。 然后为 x64 平台创建另一个映像。 尽管可以创建 x86（32 位）映像，但请使用 64 位映像。

1.  打开命令提示符。

2.  检查是否已安装 Docker。 在命令提示符处，键入：docker 版本  
    例如，编写时的版本为 18.09.0。

3.  若要更改目录，请键入：  
    cd \\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\EnterpriseServer。

4.  键入“bld.bat IacceptEULA”开始初始基础映像的生成过程。 请等待几分钟，以运行此进程。 在结果中，请注意已创建的两个映像，一个用于 x64，另一个用于 x86：

    ![显示映像的命令窗口](./media/run-image-3.png)

5.  若要为 CICS 演示创建最终映像，请通过键入 cd\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\Examples\\CICS 切换到目录。

6.  若要创建映像，请键入 bld.bat x64。 请等待几分钟，让进程运行，并显示一条消息，表明已创建该映像。

7.  键入 docker 映像以显示 VM 上安装的所有 Docker 映像的列表。 请确保 microfocus/es-acctdemo 是其中之一。

    ![显示 es-acctdemo 映像的命令窗口](./media/run-image-4.png)

## <a name="run-the-image"></a>运行映像

1.  若要启动 Enterprise Server 5.0 和 acctdemo 应用程序，请在命令提示符处键入：

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  安装 3270 终端仿真器，例如 [x3270](http://x3270.bgp.nu/)，并使用它通过端口 9040 附加到正在运行的映像。

2.  获取 acctdemo 容器的 IP 地址，以便 Docker 可以充当其管理的容器的动态主机配置协议 (DHCP) 服务器：

    1.  获取正在运行的容器的 ID。 在命令提示符处键入“Docker ps”并记下 ID（在本例中为 22a0fe3159d0） 。 保存以进行下一步。

    2.  若要获取 acctdemo 容器的 IP 地址，请使用上一步中的容器 ID，如下所示：

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    例如：

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. 请注意 acctdemo 映像的 IP 地址。 例如，以下输出中的地址为 172.19.202.52。

    ![显示 IP 地址的命令窗口屏幕截图](./media/run-image-5.png)

5. 使用仿真器装载映像。 配置仿真器以使用 acctdemo 映像的地址和端口 9040。 此处为 172.19.202.52:9040。 你的也类似于这样。 “登录到 CICS”屏幕随即打开。

    ![登录到 CICS 的屏幕截图](./media/run-image-6.png)

6. 通过输入“SYSAD”作为“用户 ID”，输入“SYSAD”作为“密码”，登录到 CICS 区域   。

7. 使用仿真器的键映射清除屏幕。 对于 x3270，选择“键映射”菜单选项。

8. 若要启动 acctdemo 应用程序，请键入“ACCT”。 应用程序的初始屏幕随即显示。

     ![屏幕截图显示了一个控制台窗口，其中显示了该应用程序。](./media/run-image-7.png)

9. 试用显示帐户类型。 例如，键入“D”作为“请求”，“11111”作为“帐户”  。 要尝试的其他帐号为 22222、33333 等。

    ![屏幕截图显示在应用程序中编辑不同的值。](./media/run-image-8.png)

10. 若要显示 Enterprise Server 管理控制台，请转到命令提示符，然后键入“start http:172.19.202.52:86”。

    ![Enterprise Server 管理控制台](media/run-image-9.png)

就这么简单！ 现在你正在 Docker 容器中运行和管理 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

-   [在 Azure 上安装 Micro Focus Enterprise Server 5.0 和 Enterprise Developer 5.0](./set-up-micro-focus-azure.md)

-   [大型机应用程序迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
