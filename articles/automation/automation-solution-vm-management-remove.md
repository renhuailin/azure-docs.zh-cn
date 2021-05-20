---
title: 删除 Azure 自动化“在空闲时间启动/停止 VM”的概述
description: 本文介绍如何删除“在空闲时间启动/停止 VM”功能，以及如何取消自动化帐户与 Log Analytics 工作区的链接。
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122079"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>从自动化账户中删除“在空闲时间启动/停止 VM”功能

在启用“在空闲时间启动/停止 VM”功能来管理 Azure VM 的运行状态后，可能会决定停止使用该功能。 可根据受支持的部署模型选择下列方法之一来删除此功能：

* 删除包含自动化帐户和链接的 Azure Monitor Log Analytics 工作区的资源组，每个资源组都专用于支持此功能。
* 取消 Log Analytics 工作区与自动化帐户的链接，并删除专用于此功能的自动化帐户。
* 从支持其他管理和监视目标的自动化帐户和链接的工作区中删除该功能。

删除此功能只会删除关联的 runbook，不会删除部署期间创建的计划或变量，也不会删除之后创建的任何自定义计划或变量。

## <a name="delete-the-dedicated-resource-group"></a>删除专用资源组

若要删除资源组，请按照 [Azure 资源管理器、资源组和资源删除](../azure-resource-manager/management/delete-resource-group.md)一文所述的步骤进行操作。

## <a name="delete-the-automation-account"></a>删除自动化帐户

若要删除专用于“在空闲时间启动/停止 VM”功能的自动化帐户，请执行以下步骤。

1. 通过 [https://portal.azure.com](https://portal.azure.com) 登录 Azure。

2. 导航到自动化帐户，选择“相关资源”下的“链接的工作区” 。

3. 选择“转到工作区”。

4. 单击“常规”下的“解决方案” 。

5. 在“解决方案”页上，选择“Start-Stop-VM[Workspace]”。

6. 在“VMManagementSolution[Workspace]”页上，从菜单中选择“删除” 。

7. 在验证信息和删除此功能时，可以在菜单中的“通知”下面跟踪操作进度。 删除后，将返回到“解决方案”页。

### <a name="unlink-workspace-from-automation-account"></a>取消工作区与自动化帐户的链接

有两个选项可用于取消 Log Analytics 工作区与自动化帐户的链接。 可以在自动化帐户或与之链接的工作区中执行此过程。

若要取消与自动化帐户的链接，请执行以下步骤。

1. 在 Azure 门户中选择“自动化帐户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在“取消关联工作区”页面上，单击“取消关联工作区”，然后根据提示进行响应 。

   ![“取消链接工作区”页](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    当它尝试取消关联 Log Analytics 工作区时，你可在菜单中的“通知”下跟踪进度。

若要取消与工作区的链接，请执行以下步骤。

1. 在 Azure 门户中，选择“Log Analytics 工作区”。

2. 在工作区中，选择“相关资源”下的“自动化帐户” 。

3. 在“自动化帐户”页上，选择“取消链接帐户”并根据提示进行响应。

当它尝试取消关联自动化时，你可在菜单中的“通知”下跟踪进度。

### <a name="delete-automation-account"></a>删除自动化帐户

1. 在 Azure 门户中选择“自动化帐户”。

2. 打开自动化帐户并从菜单中选择“删除”。

在验证信息和删除帐户时，可选中菜单中的“通知”以跟踪进度。

## <a name="delete-the-feature"></a>删除功能

若要从自动化账户中删除“在空闲时间启动/停止 VM”，请执行以下步骤。 此进程不会删除自动化帐户和 Log Analytics 工作区。 如果不想保留 Log Analytics 工作区，则必须手动删除它。 有关删除工作区的详细信息，请参阅[删除和恢复 Azure Log Analytics 工作区](../azure-monitor/logs/delete-workspace.md)。

1. 导航到自动化帐户，选择“相关资源”下的“链接的工作区” 。

2. 选择“转到工作区”。

3. 单击“常规”下的“解决方案” 。

4. 在“解决方案”页上，选择“Start-Stop-VM[Workspace]”。

5. 在“VMManagementSolution[Workspace]”页上，从菜单中选择“删除” 。

    ![删除 VM 管理功能](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在“删除解决方案”窗口中，确认要删除此功能。

7. 在验证信息和删除此功能时，可以在菜单中的“通知”下面跟踪操作进度。 删除后，将返回到“解决方案”页。

8. 如果不想保留由该功能或之后由你创建的[资源](automation-solution-vm-management.md#components)（例如变量、计划等），则必须手动从帐户中删除这些资源。

## <a name="next-steps"></a>后续步骤

若要重新启用此功能，请参阅[启用“在空闲时间启动/停止 VM”](automation-solution-vm-management-enable.md)。