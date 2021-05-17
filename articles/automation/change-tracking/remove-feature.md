---
title: 删除 Azure 自动化更改跟踪和清单功能
description: 本文介绍了如何停止使用更改跟踪和清单，以及如何从 Log Analytics 工作区中取消链接自动化帐户。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209585"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>从自动化帐户删除更改跟踪和清单

使用 Azure 自动化更改跟踪和清单启用对虚拟机的管理后，可以决定停止使用该功能，并从帐户和链接的 Log Analytics 工作区中删除配置。 本文介绍如何从托管 VM、自动化帐户和 Log Analytics 工作区中完全删除“更改跟踪和清单”。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="remove-management-of-vms"></a>删除 VM 的管理

删除“更改跟踪和清单”之前，首先需要停止管理 VM。 要从功能中取消注册 VM，请参阅[从更改跟踪中删除 VM](remove-vms-from-change-tracking.md)。

## <a name="remove-changetracking-solution"></a>删除“ChangeTracking”解决方案

你需要执行以下步骤来完全删除“更改跟踪和清单”，然后才能取消自动化帐户与工作区的关联。 你个将从工作区中删除“ChangeTracking”解决方案。

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 当你开始键入时，列表中会根据输入筛选建议。 选择“Log Analytics”。

2. 在 Log Analytics 工作区列表中，选择启用“更改跟踪和清单”时所选的工作区。

3. 在左侧选择“解决方案”。  

4. 在解决方案列表中，选择“ChangeTracking(工作区名称)”。 在解决方案的“概述”页上选择“删除”。  出现确认提示时，请选择“是”。

## <a name="unlink-workspace-from-automation-account"></a>取消工作区与自动化帐户的链接

1. 在 Azure 门户中选择“自动化帐户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在“取消关联工作区”页面上，单击“取消关联工作区”，然后根据提示进行响应 。

   ![“取消链接工作区”页](media/remove-feature/automation-unlink-workspace-blade.png)

当它尝试取消关联 Log Analytics 工作区时，你可在菜单中的“通知”下跟踪进度。

或者，你也可在 Log Analytics 工作区内取消该工作区与自动化帐户的关联：

1. 在 Azure 门户中，选择“Log Analytics”。

2. 在工作区中，选择“相关资源”下的“自动化帐户” 。

3. 在“自动化帐户”页上，选择“取消链接帐户”。

当它尝试取消关联自动化时，你可在菜单中的“通知”下跟踪进度。

## <a name="next-steps"></a>后续步骤

要重新启用此功能，请参阅[从自动化帐户启用更改跟踪和清单](enable-from-automation-account.md)、[通过浏览 Azure 门户启用更改跟踪和清单](enable-from-portal.md)、[从 runbook 启用更改跟踪和清单](enable-from-runbook.md)或者[从 Azure VM 启用更改跟踪和清单](enable-from-vm.md)。
