---
title: 删除 Azure 自动化在空闲时间启动/停止 VM 概述
description: 本文介绍如何删除在空闲时间启动/停止 VM 功能，以及如何从 Log Analytics 工作区取消关联自动化帐户。
services: automation
ms.subservice: process-automation
ms.date: 02/04/2021
ms.topic: conceptual
ms.openlocfilehash: bed9cd23abc96c51cad0a13e81ee0b64f0d433b6
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012290"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>从自动化帐户中删除在空闲时间启动/停止 VM

启用在空闲时间启动/停止 VM 功能以管理 Azure Vm 的运行状态后，你可以决定停止使用。 若要删除此功能，可以使用以下方法之一（基于支持的部署模型）：

* 删除包含自动化帐户的资源组，并链接 Azure Monitor Log Analytics 工作区，每个资源组都专用于支持此功能。
* 将 Log Analytics 工作区与自动化帐户取消链接，并删除专用于此功能的自动化帐户。
* 从自动化帐户和链接的工作区中删除该功能，该功能支持其他管理和监视目标。

删除此功能只会删除关联的 runbook，它不会删除部署期间创建的计划或变量，也不会删除在之后创建的任何自定义定义。

## <a name="delete-the-dedicated-resource-group"></a>删除专用资源组

1. 登录到 Azure [https://portal.azure.com](https://portal.azure.com) 。

2. 导航到自动化帐户，并在 "**相关资源**" 下选择 "**链接的工作区**"。

3. 选择“转到工作区”。

4. 单击“常规”下的“解决方案” 。

5. 在“解决方案”页上，选择“Start-Stop-VM[Workspace]”。

6. 在 " **VMManagementSolution [Workspace]** " 页上，从菜单中选择 " **删除** "。

    ![删除 VM 管理功能](media/automation-solution-vm-management/vm-management-solution-delete.png)

7. 若要删除创建的只支持在空闲时间启动/停止 VM 的资源组，请按照 [Azure 资源管理器资源组和资源删除](../azure-resource-manager/management/delete-resource-group.md) 一文中所述的步骤进行操作。

## <a name="delete-the-automation-account"></a>删除 Automation 帐户

若要删除专用于在空闲时间启动/停止 VM 的自动化帐户，请执行以下步骤。

1. 登录到 Azure [https://portal.azure.com](https://portal.azure.com) 。

2. 导航到自动化帐户，并在 "**相关资源**" 下选择 "**链接的工作区**"。

3. 选择“转到工作区”。

4. 单击“常规”下的“解决方案” 。

5. 在“解决方案”页上，选择“Start-Stop-VM[Workspace]”。

6. 在 " **VMManagementSolution [Workspace]** " 页上，从菜单中选择 " **删除** "。

7. 在验证信息和删除此功能时，可以在菜单中的“通知”下面跟踪操作进度。 删除后，将返回到“解决方案”页。

### <a name="unlink-workspace-from-automation-account"></a>取消工作区与自动化帐户的链接

有两个选项可用于取消将 Log Analytics 工作区与自动化帐户的链接。 可以从自动化帐户或链接的工作区执行此过程。

若要取消与自动化帐户的链接，请执行以下步骤。

1. 在 Azure 门户中选择“自动化帐户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在“取消关联工作区”页面上，单击“取消关联工作区”，然后根据提示进行响应 。

   ![“取消链接工作区”页](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    当它尝试取消关联 Log Analytics 工作区时，你可在菜单中的“通知”下跟踪进度。

若要取消链接工作区，请执行以下步骤。

1. 在 Azure 门户中，选择 " **Log Analytics 工作区**"。

2. 在工作区中，选择“相关资源”下的“自动化帐户” 。

3. 在 "自动化帐户" 页上，选择 " **取消链接帐户** 并响应提示"。

当它尝试取消关联自动化时，你可在菜单中的“通知”下跟踪进度。

### <a name="delete-automation-account"></a>删除自动化帐户

1. 在 Azure 门户中选择“自动化帐户”。

2. 打开自动化帐户，并从菜单中选择 " **删除** "。

验证信息并删除帐户时，可以从菜单中选择 " **通知**" 下的进度。

## <a name="delete-the-feature"></a>删除功能

若要从你的自动化帐户中删除在空闲时间启动/停止 VM，请执行以下步骤。 此进程不会删除自动化帐户和 Log Analytics 工作区。 如果不想保留 Log Analytics 工作区，则必须手动将其删除。 有关删除工作区的详细信息，请参阅 [删除和恢复 Azure Log Analytics 工作区](../azure-monitor/platform/delete-workspace.md)。

1. 导航到自动化帐户，并在 "**相关资源**" 下选择 "**链接的工作区**"。

2. 选择“转到工作区”。

3. 单击“常规”下的“解决方案” 。

4. 在“解决方案”页上，选择“Start-Stop-VM[Workspace]”。

5. 在 " **VMManagementSolution [Workspace]** " 页上，从菜单中选择 " **删除** "。

    ![删除 VM 管理功能](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在“删除解决方案”窗口中，确认要删除此功能。

7. 在验证信息和删除此功能时，可以在菜单中的“通知”下面跟踪操作进度。 删除后，将返回到“解决方案”页。

8. 如果您不想保留该功能创建的资源或随后 (如、变量、计划等 ) ，则必须从帐户中手动删除这些 [资源](automation-solution-vm-management.md#components) 。

## <a name="next-steps"></a>后续步骤

若要重新启用此功能，请参阅 [在非工作时间启用启动/停止](automation-solution-vm-management-enable.md)。