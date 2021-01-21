---
title: 将 Azure Stack 集线器虚拟机载入 Azure Sentinel |Microsoft Docs
description: 本文介绍如何在 Azure Stack 中心虚拟机上预配 Azure Monitor、更新和配置管理虚拟机扩展，并通过 Sentinel 开始对其进行监视。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 068296a02af0abd2fb18d284fdaf01b7c1b198fa
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631136"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>将 Azure Stack 集线器虚拟机连接到 Azure Sentinel

利用 Azure Sentinel，你可以在一个位置监视在 Azure 和 Azure Stack 集线器上运行的 Vm。 若要将 Azure Stack 计算机加入 Azure Sentinel，首先需要将虚拟机扩展添加到现有的 Azure Stack 中心虚拟机。 

连接 Azure Stack 集线器计算机后，从基于数据的仪表板库中进行选择。 可以轻松地根据需要对这些仪表板进行定制。

## <a name="add-the-virtual-machine-extension"></a>添加虚拟机扩展 

将 **Azure Monitor、更新和配置管理** 虚拟机扩展添加到 Azure Stack 中心运行的虚拟机。 

1. 在新的浏览器选项卡中，登录到 [Azure Stack 中心门户](/azure-stack/user/azure-stack-use-portal#access-the-portal)。

1. 请在 " **虚拟机** " 页上，选择要用 Azure Sentinel 保护的虚拟机。 有关如何在 Azure Stack 集线器上创建虚拟机的信息，请参阅使用 [Azure Stack 中心门户创建 Windows SERVER vm](/azure-stack/user/azure-stack-quick-windows-portal) 或 [使用 Azure Stack 集线器门户创建 Linux 服务器 vm](/azure-stack/user/azure-stack-quick-linux-portal)。

1. 选择“扩展”。 此时将显示此虚拟机上安装的虚拟机扩展列表。

1. 单击“添加”  选项卡。此时会打开“新建资源”菜单边栏选项卡，其中显示了可用虚拟机扩展的列表。 

1. 选择 **Azure Monitor、更新和配置管理** 扩展，并单击 " **创建**"。 此时将打开 " **安装扩展** 配置" 窗口。

   ![Azure Monitor、更新和配置管理设置](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 如果你没有看到在 marketplace 中列出的 **Azure Monitor、更新和配置管理** 扩展，请联系你的 Azure Stack 中心运营商以使其可用。

1. 在 Azure Sentinel 菜单中，选择 " **工作区设置** "，然后选择 " **高级**"，并将 **工作区 ID** 和 **工作区密钥复制 (主键)**。 

1. 在 "Azure Stack 集线器 **安装扩展** " 窗口中，将它们粘贴到指定字段中，然后单击 **"确定"**。

1. 扩展安装完成后，其状态将显示为 " **预配已成功**"。 虚拟机可能需要长达一小时的时间才能显示在 Azure Sentinel 门户中。

有关安装和配置 Windows 代理的详细信息，请参阅 [连接 windows 计算机](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)。

有关如何排查 Linux 代理问题，请参阅[排查 Azure Log Analytics Linux 代理问题](../azure-monitor/platform/agent-linux-troubleshoot.md)。

在 Azure 上的 Azure Sentinel 门户的 " **虚拟机**" 下，你可以大致了解所有 vm 和计算机及其状态。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要该扩展，可以通过 Azure Stack 中心门户从虚拟机中删除该扩展。

删除扩展：

1. 打开 **Azure Stack 集线器门户**。

1. 转到“虚拟机”页，选择要从中删除扩展的虚拟机。

1. 选择“扩展”，然后选择“Microsoft.EnterpriseCloud.Monitoring”扩展。

1. 单击 " **卸载**"，然后确认你的选择。

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- 将数据从[通用事件格式设备](connect-common-event-format.md)流式传输到 Azure Sentinel。
