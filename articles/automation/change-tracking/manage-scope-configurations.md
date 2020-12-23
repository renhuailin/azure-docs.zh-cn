---
title: 限制 Azure 自动化更改跟踪和清单部署范围
description: 本文介绍如何使用作用域配置来限制更改跟踪和清单部署的范围。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209450"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>限制更改跟踪和清单部署范围

本文介绍如何使用 [更改跟踪和清单](overview.md) 功能将更改部署到 vm 时使用作用域配置。 有关详细信息，请参阅[在 Azure Monitor 中设定监视解决方案的目标（预览版）](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>关于范围配置

作用域配置是一个或多个已保存搜索的组， (查询) 用于将更改跟踪和清单的作用域限制为特定的计算机。 更新管理在 Log Analytics 工作区中使用范围配置来确定要启用的计算机。 将计算机添加到功能的更改时，还会将计算机添加到工作区中的已保存搜索。

## <a name="set-the-scope-limit"></a>设置范围限制

限制更改跟踪和清单部署的作用域：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“自动化”****。 当你开始键入时，列表中会根据输入筛选建议。 选择“自动化帐户”****。

3. 在自动化帐户列表中，选择启用更改跟踪和清单时选择的帐户。

4. 在自动化帐户中，选择“相关资源”下的“链接的工作区” 。

5. 单击 " **前往工作区**"。

6. 在“工作区数据源”下，选择“范围配置(预览)” 。

7. 选择  `MicrosoftDefaultScopeConfig-ChangeTracking` 作用域配置右侧的省略号，然后单击 " **编辑**"。

8. 在“编辑”窗格中，选择“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。 更改跟踪和清单使用的已保存搜索如下：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. 选择已保存的搜索来查看和编辑用于填充组的查询。 下图显示了查询及其结果：

    ![保存的搜索](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>后续步骤

* 要使用更改跟踪和库存，请参阅[管理更改跟踪和库存](manage-change-tracking.md)。
* 要排查常见功能问题，请参阅[排查更改跟踪和库存问题](../troubleshoot/change-tracking.md)。
