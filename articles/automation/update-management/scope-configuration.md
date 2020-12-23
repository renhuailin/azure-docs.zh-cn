---
title: 限制 Azure 自动化更新管理部署范围
description: 本文介绍如何使用范围配置来限制更新管理部署的范围。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221981"
---
# <a name="limit-update-management-deployment-scope"></a>限制更新管理的部署范围

本文介绍在 VM 上使用[更新管理](overview.md)功能部署更新和修补程序时，如何使用范围配置。 有关详细信息，请参阅[在 Azure Monitor 中设定监视解决方案的目标（预览版）](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>关于范围配置

范围配置是包含一个或多个已保存搜索（查询）的组，用于将更新管理的范围限制为特定计算机。 更新管理在 Log Analytics 工作区中使用范围配置来确定要启用的计算机。 添加计算机以接收更新管理中的更新时，还会将该计算机添加到工作区中的已保存搜索。

## <a name="set-the-scope-limit"></a>设置范围限制

限制更新管理部署的范围：

1. 在自动化帐户中，选择“相关资源”下的“链接的工作区” 。

2. 选择“转到工作区”。

3. 在“工作区数据源”下，选择“范围配置(预览)” 。

4. 选择 `MicrosoftDefaultScopeConfig-Updates` 范围配置右侧的省略号，然后选择“编辑”。

5. 在“编辑”窗格中，展开“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。 更新管理使用的已保存的搜索是：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

6. 选择已保存的搜索来查看和编辑用于填充组的查询。 下图显示了查询及其结果：

    [![保存的搜索](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>后续步骤

可[查询 Azure Monitor 日志](query-logs.md)来分析更新评估、部署和其他相关管理任务。 它包含预定义的查询，可帮助你入门。
