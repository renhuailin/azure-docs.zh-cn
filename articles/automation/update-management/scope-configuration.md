---
title: 限制 Azure 自动化更新管理部署范围
description: 本文介绍如何使用范围配置来限制更新管理部署的范围。
services: automation
ms.date: 06/03/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 230be9fcdb30e51802a2e288cde86fd8047f70f0
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854947"
---
# <a name="limit-update-management-deployment-scope"></a>限制更新管理的部署范围

本文介绍使用[更新管理](overview.md)功能在计算机上部署更新和补丁时，如何使用范围配置。 有关详细信息，请参阅[在 Azure Monitor 中设定监视解决方案的目标（预览版）](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>关于范围配置

范围配置是包含一个或多个已保存搜索（查询）的组，用于将更新管理的范围限制为特定计算机。 更新管理在 Log Analytics 工作区中使用范围配置来确定要启用的计算机。 添加计算机以接收更新管理中的更新时，还会将该计算机添加到工作区中的已保存搜索。 

默认情况下，更新管理会创建一个名为 Updates__MicrosoftDefaultComputerGroup 的计算机组，具体取决于如何使用更新管理功能启用计算机：

* 在自动化帐户中，选择“+ 添加 Azure VM”。
* 在自动化帐户中，选择“管理计算机”，然后选择“在所有可用计算机上启用”选项，或选择“在所选计算机上启用”。

如果选择上述方法之一，系统会将此计算机组添加到“MicrosoftDefaultScopeConfig-Updates”范围配置中。 还可以在此范围中添加一个或多个自定义计算机组，以满足管理需求，并控制特定计算机启用方式，以便使用更新管理功能进行管理。

若要从 Updates__MicrosoftDefaultComputerGroup 中删除一个或多个计算机，以停止使用更新管理功能管理这些计算机，请参阅[从“更新管理”中删除 VM](remove-vms.md)。

## <a name="set-the-scope-limit"></a>设置范围限制

限制更新管理部署的范围：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，导航到 Log Analytics 工作区。 从列表中选择你的工作区。

3. 在 Log Analytics 工作区中，从左侧菜单中范围配置(预览)”。

4. 选择“MicrosoftDefaultScopeConfig-Updates”范围配置右侧的省略号，并选择“编辑”。

5. 在“编辑”窗格中，展开“选择计算机组”。 “计算机组”窗格将显示已添加到范围配置中且已保存的搜索。 更新管理使用的已保存的搜索是：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

6. 如果已添加自定义组，则列表中会显示此内容。 若要取消选择此项，请清除其左侧的复选框。 若要在范围中添加自定义组，请选择此组，然后在完成更改后，单击“选择”。

7. 在“编辑范围配置”页上，单击“确定”以保存更改内容。

## <a name="next-steps"></a>后续步骤

可[查询 Azure Monitor 日志](query-logs.md)来分析更新评估、部署和其他相关管理任务。 它包含预定义的查询，可帮助你入门。
