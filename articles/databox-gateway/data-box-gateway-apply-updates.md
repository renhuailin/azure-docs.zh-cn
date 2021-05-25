---
title: 在 Azure Data Box Gateway 系列设备上安装更新 | Microsoft Docs
description: 介绍如何使用 Azure 门户和本地 Web UI 为 Azure Data Box Gateway 系列设备应用更新
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 7fda10310a4bac085b248248d80d708dfa7f3ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581611"
---
# <a name="update-your-azure-data-box-gateway"></a>更新 Azure Data Box Gateway

本文介绍通过本地 Web UI 和 Azure 门户在 Azure Data Box Gateway 上安装更新所需的步骤。 应用软件更新或修补程序，使 Data Box Gateway 设备保持最新。

> [!IMPORTANT]
>
> - Update 1911 对应于设备上的 1.6.1049.786 软件版本 。 有关此更新的信息，请转到[发行说明](data-box-gateway-1911-release-notes.md)。
>
> - 请记住，安装更新或修补程序会重新启动设备。 假设 Data Box Gateway 是一个单节点设备，任何正在进行的 I/O 都会被中断，并且你的设备将经历长达 30 分钟的设备软件更新停机时间。

后续部分将介绍其中的每个步骤。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

建议通过 Azure 门户安装更新。 设备每天自动扫描一次更新。 当更新可用时，你将在门户中看到通知。 然后可以下载并安装更新。

> [!NOTE]
> 在继续安装更新之前，请确保设备运行状况良好并且状态显示为“联机”。

1. 当更新可用于设备时，你将看到通知。 选择通知，或从顶部命令栏中选择“更新设备”。 这将允许你应用设备软件更新。

    ![Azure Data Box Gateway 主页的屏幕截图，其中突出显示了“概述”和“更新设备”选项。](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. 在“设备更新”边栏选项卡中，检查你是否已查看了发行说明中与新功能关联的许可条款。

    你可以选择“下载”并安装更新，也可以只“下载”更新。 然后可以选择以后安装这些更新。

    ![“设备更新”对话框的屏幕截图，其中突出显示了“接受”和“理解”选项以及“下载并安装”选项。](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    如果要下载并安装更新，请选中在下载完成后自动安装更新的选项。

    ![“设备更新”对话框的屏幕截图，其中突出显示了“接受”选项和“下载”选项。](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. 开始下载更新。 你会看到一条通知，指示正在下载。

    ![通知的屏幕截图，显示“正在下载并安装更新”。](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    通知横幅也会显示在 Azure 门户中。 这指示下载进度。

    ![Azure Data Box Gateway 主页的屏幕截图，其中突出显示了“概述”选项和“正在下载更新”的通知横幅。](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    你可以选择此通知或选择“更新设备”以查看更新的详细状态。

    ![“下载并安装更新”对话框的屏幕截图，其中突出显示了“状态为正在进行”消息和“下载更新”消息。](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. 下载完成后，通知横幅会更新以指示完成。 如果选择下载并安装更新，则将自动开始安装。

    ![Azure Data Box Gateway 主页的屏幕截图，其中突出显示了“概述”选项和“已下载更新”的通知横幅。](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    如果选择仅下载更新，则选择该通知以打开“设备更新”边栏选项卡。 选择“安装”。
  
    ![“设备更新”对话框的屏幕截图，其中突出显示了“安装”选项。](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. 你会看到一条通知，指示正在安装。

    ![Azure Data Box Gateway 主页的屏幕截图，其中突出显示了“概述”选项以及下载和安装进度的消息。](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    门户还会显示信息警报，指示正在安装。 <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. 因为这是一个单节点设备，所以设备在安装更新后将重启。 重启过程中的关键警报指示设备检测信号丢失。

    ![Azure Data Box Gateway 主页的屏幕截图，其中突出显示了“概述”选项和关键警报通知横幅。](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    选择警报以查看相应的设备事件。

    ![“设备事件”部分屏幕截图，其中突出显示了“丢失了设备的检测信号”事件。](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. 安装更新后，设备状态将更新为“联机”。

    ![Azure Data Box Gateway 主页的屏幕截图，其中突出显示了“概述”选项和“联机”状态。](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    在顶部命令栏中，选择“设备更新”。 验证是否已成功安装更新，以及设备软件版本是否反映了此情况。

    ![“设备更新”对话框的屏幕截图，其中突出显示了“已安装的软件版本”部分。](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

- 下载更新或修补程序
- 安装更新或修补程序

以下部分详细介绍了这些步骤。

### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

若要下载更新，请执行以下步骤。 你可以从 Microsoft 提供的位置或从 Microsoft 更新目录下载更新。

执行以下步骤，从 Microsoft 更新目录下载更新。

1. 启动浏览器并导航到 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)。

   ![浏览器窗口中 Microsoft 更新目录的屏幕截图，其中在搜索文本框中键入了 Data Box Gateway。 浏览器的地址栏和“搜索”文本框均被突出显示。](./media/data-box-gateway-apply-updates/download-update-1.png)

2. 在 Microsoft 更新目录的搜索框中，输入要下载的更新的修补程序或条款的知识库 (KB) 编号。 例如，输入“Azure Data Box Gateway”，然后单击“搜索” 。

   更新列表显示为“Azure Data Box Gateway 1911”。

   ![浏览器窗口中 Microsoft 更新目录的屏幕截图，其中突出显示了 Data Box Gateway 搜索结果。](./media/data-box-gateway-apply-updates/download-update-2.png)

3. 选择“下载”。 有一个要下载的单独文件（名为 SoftwareUpdatePackage.exe），它对应于设备软件更新。 将该文件下载到本地系统上的某个文件夹中。 也可以将该文件夹复制到可通过设备访问的网络共享。

   ![“下载”对话框的屏幕截图，其中突出显示了指向 SoftwareUpdatePackage.exe 文件的链接。](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

安装更新或修补程序之前，请确保：

- 更新或修补程序已本地下载到主机上，或可通过网络共享访问。
- 你的设备状态为“正常”，如本地 Web UI 的“概述”页中所示。

   ![Data Box Gateway 本地 Web UI 的屏幕截图，其中突出显示了“仪表板”选项和“软件状态为正常”消息。](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

此过程大约需要 20 分钟来完成。 执行以下步骤，安装更新或修补程序。

1. 在本地 Web UI 中，转到“维护” > “软件更新”。 记下运行的软件版本。

   ![Data Box Gateway 本地 Web UI 的屏幕截图，其中突出显示了“软件更新”选项和当前软件版本的消息。](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. 提供更新文件的路径。 也可以浏览到网络共享上的更新安装文件。 选择包含 SoftwareUpdatePackage.exe 后缀的软件更新文件。

   ![文件资源管理器的屏幕截图，其中突出显示了 SoftwareUpdatePackage.exe 文件。](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. 选择“应用”。

   ![Data Box Gateway 本地 Web UI 的屏幕截图，其中突出显示了“软件更新”选项、“更新文件路径”文本框和“应用更新”选项。](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. 出现确认提示时，选择“是”以继续执行下一步操作。 假定设备是单节点设备，应用更新后，设备将重新启动并且会出现停机。

   ![“软件更新”对话框的屏幕截图，其中突出显示了“是”选项。](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. 更新开始。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。

6. 重新启动完成后，会转到“登录”页。 若要验证设备软件是否已更新，请在本地 Web UI 中，转到“维护” > “软件更新” 。 本示例中显示的软件版本为“1.6.1049.786”。

   ![Data Box Gateway 本地 Web UI 的屏幕截图，其中突出显示了“软件更新”选项和更新后的当前软件版本的消息。](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>后续步骤

了解有关[管理 Azure Data Box Gateway](data-box-gateway-manage-users.md) 的详细信息。
