---
title: 删除 Azure 自动化帐户
description: 本文介绍了如何删除不同配置方案中的自动化帐户。
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe2d99a610be3877b4a347e4bd0dd17df53ba326
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834149"
---
# <a name="how-to-delete-your-azure-automation-account"></a>如何删除 Azure 自动化帐户

首先启用 Azure 自动化帐户以自动执行 IT 或业务流程，或启用其他功能以支持 Azure 和非 Azure 计算机的操作管理（如“更新管理”），随后可决定停止使用自动化帐户。 若已启用功能依赖于与 Azure Monitor Log Analytics 工作区的集成，还需几步才能完成操作。

可根据受支持部署模型选择下列方法之一删除自动化帐户：

* 删除包含自动化帐户的资源组。
* 如有下列情况，删除包含自动化帐户的资源组以及与之相链接的 Azure Monitor Log Analytics 工作区：

    * 帐户和工作区专用于支持“更新管理”、“更改跟踪和清单”和/或“在空闲时间启动/停止 VM”。
    * 帐户专用于流程自动化，并通过与工作区集成发送 runbook 作业状态和作业流。

* 取消 Log Analytics 工作区与自动化帐户的链接，并删除自动化帐户。
* 从链接的工作区中删除该功能，取消该帐户与工作区的链接，然后删除自动化帐户。

本文介绍如何通过 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 彻底删除自动化帐户。

> [!NOTE]
> 在继续之前，请确认订阅、资源组或资源上没有应用任何[资源管理器锁](../azure-resource-manager/management/lock-resources.md)，以防止意外删除或修改关键资源。 如果已部署“在空闲时间启动/停止 VM”解决方案，该解决方案将针对自动化帐户中的多个从属资源（特别是其 runbook 和变量）将锁定级别设置为“CanNotDelete”。 删除自动化帐户之前，需要删除任何锁。

## <a name="delete-the-dedicated-resource-group"></a>删除专用资源组

若要删除自动化帐户与 Log Analytics 工作区（如果工作区与该帐户相链接且创建于该帐户专用的同一资源组），请遵循[Azure 资源管理器资源组和资源删除](../azure-resource-manager/management/delete-resource-group.md)一文所述步骤。

## <a name="delete-a-standalone-automation-account"></a>删除独立自动化帐户

如果自动化帐户未与 Log Analytics 工作区相链接，请执行以下步骤将其删除。

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

1. 通过[https://portal.azure.com](https://portal.azure.com)登录 Azure。

2. 在 Azure 门户导航到“自动化账户”。

3. 打开自动化帐户并从菜单中选择“删除”。

验证信息和删除账户时可选中菜单中的“通知”跟踪进度。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用此命令删除自动化帐户，无验证提示。

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>删除与工作区相链接的独立自动化帐户

如果你已将自动化帐户与 Log Analytics 工作区相链接，以便收集作业流和作业日志，请执行以下步骤删除帐户。

有两个选项可用于取消 Log Analytics 工作区与自动化帐户的链接。 可以在自动化帐户或与之链接的工作区中执行此过程。

若要取消与自动化帐户的链接，请执行以下步骤。

1. 在 Azure 门户导航到“自动化账户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在“取消链接工作区”页面选择“取消链接工作区”并根据提示进行响应。

   ![“取消链接工作区”页](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    当它尝试取消关联 Log Analytics 工作区时，你可在菜单中的“通知”下跟踪进度。

若要取消链接工作区，请执行以下步骤。

1. 在 Azure 门户中，导航到 Log Analytics 工作区。

2. 在工作区中，选择“相关资源”下的“自动化帐户” 。

3. 在“自动化帐户”页面选择“取消链接帐户”并根据提示进行响应。

当它尝试取消关联自动化时，你可在菜单中的“通知”下跟踪进度。

成功取消自动化帐户与工作区的链接后，请执行“[独立自动化帐户](#delete-a-standalone-automation-account)”部分所述步骤以删除帐户。

## <a name="delete-a-shared-capability-automation-account"></a>删除共享功能自动化帐户

若要删除链接至 Log Analytics 工作区的自动化帐户（链接目的可能是支持“更新管理”、“更改跟踪和清单”和/或“在空闲时间启动/停止 VM”），请执行以下步骤。

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>步骤 1。 从相链接的工作区中删除解决方案

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

1. 通过[https://portal.azure.com](https://portal.azure.com)登录 Azure。

2. 导航到自动化帐户并选择 "**相关资源** " 下的 "**链接的工作区**"。

3. 选择“转到工作区”。

4. 单击“常规”下的“解决方案” 。

5. 在“解决方案”页面，根据帐户中部署的功能选择以下项之一：

    * 对于“在空闲时间启动/停止 VM”，请选择“开始-停止-VM [工作区名称]”。
    * 对于“更新管理”，请选择“更新（工作区名称）”。
    * 对于“更改跟踪和清单”，请选择“更改跟踪”（工作区名称）。

6. 在“解决方案”页面从菜单中选择“删除”。 如果自动化帐户和链接的工作区部署了多个上述功能，则需在继续操作之前将其选中并删除。

7. 在验证信息和删除此功能时，可以在菜单中的“通知”下面跟踪操作进度。 删除后，将返回到“解决方案”页。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 删除已安装的解决方案，请使用 [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet。

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>步骤 2。 取消工作区与自动化帐户的链接

有两个选项可用于取消 Log Analytics 工作区与自动化帐户的链接。 可以在自动化帐户或与之链接的工作区中执行此过程。

若要取消与自动化帐户的链接，请执行以下步骤。

1. 在 Azure 门户导航到“自动化账户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在“取消链接工作区”页面选择“取消链接工作区”并根据提示进行响应。

   ![“取消链接工作区”页](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    当它尝试取消关联 Log Analytics 工作区时，你可在菜单中的“通知”下跟踪进度。

若要取消链接工作区，请执行以下步骤。

1. 在 Azure 门户中，导航到 Log Analytics 工作区。

2. 在工作区中，选择“相关资源”下的“自动化帐户” 。

3. 在“自动化帐户”页面选择“取消链接帐户”并根据提示进行响应。

当它尝试取消关联自动化时，你可在菜单中的“通知”下跟踪进度。

### <a name="step-3-delete-automation-account"></a>步骤 3。 删除自动化帐户

成功取消自动化帐户与工作区的链接后，请执行“[独立自动化帐户](#delete-a-standalone-automation-account)”部分所述步骤以删除帐户。

## <a name="next-steps"></a>后续步骤

若要从 Azure 门户创建自动化帐户，请参阅[创建独立的 Azure 自动化帐户](automation-create-standalone-account.md)。 如果你喜欢使用模板创建帐户，请参阅[使用 Azure 资源管理器模板创建自动化帐户](quickstart-create-automation-account-template.md)。
