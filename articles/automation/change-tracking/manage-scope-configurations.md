---
title: 限制 Azure 自动化更改跟踪和清单部署范围
description: 本文介绍如何使用范围配置来限制更改跟踪和清单部署的范围。
services: automation
ms.subservice: change-inventory-management
ms.date: 05/27/2021
ms.topic: conceptual
ms.openlocfilehash: 6812a0b0688efdb75d847a36d661ba87017a8b9d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110653188"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>限制更改跟踪和清单部署范围

本文介绍在 VM 上使用[更改跟踪和清单](overview.md)功能部署更改时，如何使用范围配置。 有关详细信息，请参阅[在 Azure Monitor 中设定监视解决方案的目标（预览版）](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>关于范围配置

范围配置是包含一个或多个已保存搜索（查询）的组，用于将更改跟踪和清单的范围限制为特定计算机。 更新管理在 Log Analytics 工作区中使用范围配置来确定要启用的计算机。 添加计算机以从该功能接收更新时，还会将该计算机添加到工作区中的已保存搜索。

默认情况下，更改跟踪和清单会根据你启用计算机的方式，创建一个名为“ChangeTracking__MicrosoftDefaultComputerGroup”的计算机组：

* 在自动化帐户中，选择“+ 添加 Azure VM”。
* 在自动化帐户中，选择“管理计算机”，然后选择“在所有可用计算机上启用”选项，或选择“在所选计算机上启用”。

如果选择上述其中一种方法，则会将此计算机组添加到“MicrosoftDefaultScopeConfig-ChangeTracking”范围配置中。 还可以在此范围中添加一个或多个自定义计算机组，以满足管理需求，并控制特定计算机的启用方式，以便使用“更改跟踪和清单”进行管理。

若要从“ChangeTracking__MicrosoftDefaultComputerGroup”中删除一台或多台计算机，以停止使用“更改跟踪和清单”对其进行管理，请参阅[从更改跟踪和清单中删除 VM](remove-vms-from-change-tracking.md)。

## <a name="set-the-scope-limit"></a>设置范围限制

限制更改跟踪和清单部署的范围：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，导航到 Log Analytics 工作区。 从列表中选择你的工作区。

3. 在 Log Analytics 工作区中，从左侧菜单中范围配置(预览)”。

4. 选择“MicrosoftDefaultScopeConfig-ChangeTracking”范围配置右侧的省略号，并选择“编辑”。

5. 在“编辑”窗格中，展开“选择计算机组”。 “计算机组”窗格将显示已添加到范围配置中且已保存的搜索。 更新管理使用的已保存的搜索是：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | ChangeTracking        | ChangeTracking__MicrosoftDefaultComputerGroup         |

6. 如果已添加自定义组，则列表中会显示此内容。 若要取消选择此项，请清除其左侧的复选框。 若要在范围中添加自定义组，请选择此组，然后在完成更改后，单击“选择”。

7. 在“编辑范围配置”页上，单击“确定”以保存更改内容。

## <a name="next-steps"></a>后续步骤

* 要使用更改跟踪和库存，请参阅[管理更改跟踪和库存](manage-change-tracking.md)。
* 要排查常见功能问题，请参阅[排查更改跟踪和库存问题](../troubleshoot/change-tracking.md)。
