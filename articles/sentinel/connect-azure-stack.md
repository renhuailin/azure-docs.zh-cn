---
title: 将 Azure Stack Hub 虚拟机载入 Azure Sentinel | Microsoft Docs
description: 本文介绍如何在 Azure Stack Hub 虚拟机上预配 Azure Monitor、更新和配置管理虚拟机扩展，以及如何通过 Azure Sentinel 开始对其进行监视。
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
ms.openlocfilehash: 38b595e4f9b2582f3baefecabf6285ee36436ad6
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260059"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>将 Azure Stack Hub 虚拟机连接到 Azure Sentinel

利用 Azure Sentinel，你可以在一个位置集中监视在 Azure 和 Azure Stack Hub 上运行的 VM。 若要将 Azure Stack 计算机载入 Azure Sentinel，首先需要将虚拟机扩展添加到现有的 Azure Stack Hub 虚拟机。 

连接 Azure Stack Hub 计算机后，从仪表板块库中进行选择，这仪表板基于你的数据呈现见解。 可以根据需要轻松地自定义这些仪表板。

## <a name="add-the-virtual-machine-extension"></a>添加虚拟机扩展 

将“Azure Monitor、更新和配置管理”虚拟机扩展添加到 Azure Stack Hub 上运行的虚拟机。 

1. 在新的浏览器标签页中，登录到 [Azure Stack Hub](/azure-stack/user/azure-stack-use-portal#access-the-portal) 门户。

1. 转到“虚拟机”页，选择要使用 Azure Sentinel 保护的虚拟机。 有关如何在 Azure Stack Hub 上创建虚拟机的信息，请参阅[使用 Azure Stack Hub 门户创建 Windows 服务器 VM](/azure-stack/user/azure-stack-quick-windows-portal) 或[使用 Azure Stack Hub 门户创建 Linux 服务器 VM](/azure-stack/user/azure-stack-quick-linux-portal)。

1. 选择“扩展”。 此时将显示此虚拟机上安装的虚拟机扩展列表。

1. 选择“添加”选项卡。此时会打开“新建资源”菜单边栏选项卡，其中显示了可用虚拟机扩展的列表。 

1. 选择“Azure 监视、更新和配置管理”扩展，然后选择“创建”。  此时会打开“安装扩展”配置窗口。

   ![“Azure Monitor、更新和配置管理”设置](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 如果在市场中没有看到“Azure Monitor、更新和配置管理”扩展，请联系 Azure Stack Hub 操作员，让该操作员将其列出。

1. 在 Azure Sentinel 菜单上，选择“工作区设置”，然后选择“高级”，复制“工作区 ID”和“工作区密钥(主密钥)”。    

1. 在 Azure Stack Hub 的“安装扩展”窗口中，将其粘贴到指示的字段中，然后选择“确定” 。

1. 扩展安装完成后，其状态显示为“预配成功”。 最长可能需要经过一小时，该虚拟机才会显示在 Azure Sentinel 门户中。

有关安装和配置 Windows 代理的详细信息，请参阅[连接 Windows 计算机](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)。

有关如何排查 Linux 代理问题，请参阅[排查 Azure Log Analytics Linux 代理问题](../azure-monitor/agents/agent-linux-troubleshoot.md)。

在 Azure 上的 Azure Sentinel 门户中的“虚拟机”下面，可以概览所有 VM 和计算机及其相关状态。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要，可以通过 Azure Stack Hub 门户删除虚拟机中的扩展。

删除扩展：

1. 打开“Azure Stack Hub 门户”。

1. 转到“虚拟机”页，选择要从中删除扩展的虚拟机。

1. 选择“扩展”，然后选择“Microsoft.EnterpriseCloud.Monitoring”扩展。

1. 选择“卸载”，然后确认所做的选择。

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- 将数据从[通用事件格式设备](connect-common-event-format.md)流式传输到 Azure Sentinel。
