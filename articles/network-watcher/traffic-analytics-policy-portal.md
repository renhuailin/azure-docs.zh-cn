---
title: 使用 Azure Policy 部署和管理流量分析
titleSuffix: Azure Network Watcher
description: 本文介绍如何使用内置策略来管理流量分析的部署
services: network-watcher
documentationcenter: na
author: moagra
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2021
ms.author: moagra
ms.openlocfilehash: 3be6d5b30d270c9687b7100c07ee675268cee5c0
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609146"
---
# <a name="deploy-and-manage-traffic-analytics-using-azure-policy"></a>使用 Azure Policy 部署和管理流量分析 

Azure Policy 可帮助实施组织标准并大规模评估合规性。 Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本和管理方面的要求。 本文介绍三个适用于[流量分析](./traffic-analytics.md)的用于管理设置的内置策略。

如果你是首次创建 Azure Policy 定义，可仔细阅读： 
- [Azure Policy 概述](../governance/policy/overview.md) 
- [有关创建 Azure Policy 分配的教程](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)。


## <a name="locate-the-policies"></a>查找策略
1. 转到 Azure 门户 – [portal.azure.com](https://portal.azure.com) 

通过在的顶部搜索栏中搜索“策略”，导航到 Azure Policy 页面![策略主页](./media/network-watcher-builtin-policy/1_policy-search.png)

2. 转到左侧窗格中的“分配”选项卡

![“分配”选项卡](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. 单击“分配策略”按钮 

![“分配策略”按钮](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. 单击“策略定义”下的三点菜单，查看可用策略

5. 使用“类型”筛选器并选择“内置”。 然后搜索“流量分析”

应会看到三个内置策略 ![流量分析的策略列表](./media/traffic-analytics/policy-filtered-view.png)

6. 选择要分配的策略

- “网络观察程序流日志应启用流量分析”是审核策略，它可以标记不合规的流日志，即未启用流量分析的流日志
- “配置网络安全组以使用特定工作区进行流量分析”和“配置网络安全组以启用流量分析”是包含部署操作的策略 。 这些策略在所有 NSG 上启用流量分析，覆盖/不覆盖已配置的设置（具体取决于已启用的策略）。

下面是每条策略的单独说明。  

## <a name="audit-policy"></a>审核策略 

### <a name="network-watcher-flow-logs-should-have-traffic-analytics-enabled"></a>网络观察程序流日志应启用流量分析

该策略审核“Microsoft.Network/networkWatchers/flowLogs”类型的所有现有 Azure 资源管理器对象，并检查是否已通过流日志资源的“networkWatcherFlowAnalyticsConfiguration.enabled”属性启用流量分析。 它会标记该属性设置为 false 的流日志资源。

如果你要查看该策略的完整定义，可访问[“定义”选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)，并搜索“流量分析”找到该策略

### <a name="assignment"></a>分配

1. 填写策略详细信息

- 范围：可以是订阅或资源组。 对于后一种范围，请选择包含流日志资源的资源组（而不是网络安全组）
- 策略定义：应按照“查找策略”部分所示进行选择。
- 分配名称：选择一个描述性的名称 

2. 单击“查看 + 创建”以查看分配

此策略不需要任何参数。 分配审核策略时，无需在“修正”选项卡中填写详细信息。  

![审核策略 - 查看流量分析](./media/traffic-analytics/policy-one-assign.png)

### <a name="results"></a>结果

若要检查结果，请打开“合规性”选项卡，然后搜索分配名称。
策略运行后，显示的内容应该类似于以下屏幕截图。 如果策略未运行，请稍等一会儿。 

![审核策略结果 - 流量分析](./media/traffic-analytics/policy-one-results.png)

## <a name="deploy-if-not-exists-policy"></a>Deploy-If-not-exists 策略 

### <a name="configure-network-security-groups-to-use-specific-workspace-for-traffic-analytics"></a>配置网络安全组以使用特定工作区进行流量分析 

该策略会标记未启用流量分析的 NSG。 这意味着，对于已标记的 NSG，相应的流日志资源不存在，或者流日志资源存在，但未对其启用流量分析。 如果你希望该策略影响现有资源，可以创建修正任务。
网络观察程序是一个区域性的服务，因此，此策略只会应用到属于选定范围内特定区域的 NSG。 （若要在其他区域中应用，请创建另一个策略分配。）
 
可以在分配策略时或者在分配并评估策略后分配修正。 修正将使用提供的参数对所有已标记的资源启用流量分析。 请注意，如果 NSG 已在特定存储 ID 中启用流日志，但它未启用流量分析，则修正将使用提供的参数在此 NSG 上启用流量分析。 如果对于已标记的 NSG，参数中提供的存储 ID 与为流日志启用的存储 ID 不同，则会在修正任务中，将后者的存储 ID 覆盖为提供的存储 ID。 如果你不想要覆盖，请使用下面所述的“配置网络安全组以启用流量分析”策略。

如果你要查看该策略的完整定义，可访问[“定义”选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)，并搜索“流量分析”找到该策略。 

### <a name="configure-network-security-groups-to-enable-traffic-analytics"></a>配置网络安全组以启用流量分析

该策略与上述策略类似，唯一的差别在于，在修正期间，它不会使用策略分配中提供的参数，覆盖已启用流日志、但已禁用流量分析的已标记 NSG 上的流日志设置。

### <a name="assignment"></a>分配

1. 填写策略详细信息

- 范围：可以是订阅或资源组  
- 策略定义：应按照“查找策略”部分所示进行选择。
- 分配名称：选择一个描述性的名称 

2. 添加策略参数 

- NSG 区域：策略的目标 Azure 区域
- 存储 ID：存储帐户的完整资源 ID。 此存储帐户应与 NSG 位于同一区域。
- 网络观察程序 RG：包含网络观察程序资源的资源组的名称。 如果尚未对其进行重命名，可输入默认值“NetworkWatcherRG”。
- 网络观察程序名称：区域网络观察程序服务的名称。 格式：NetworkWatcher_RegionName。 示例：NetworkWatcher_centralus。
- 工作区资源 ID：必须在其中启用流量分析的工作区的资源 ID。 格式为“/subscriptions/<SubscriptionID>/resourceGroups/<ResouceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>”
- WorkspaceID：工作区 GUID
- WorkspaceRegion：工作区的区域（请注意，它不需要与 NSG 的区域相同）
- TimeInterval：将处理的日志推送到工作区的频率。 当前允许的值为 60 分钟和 10 分钟。 默认值为 60 分钟。
- 效果：DeployIfNotExists（已分配值）

3. 添加修正详细信息

- 如果希望策略影响现有资源，请勾选“创建修正任务”
- “创建托管标识”应已选中
- 已选择托管标识之前所在的位置
- 你将需要参与者或所有者权限才能使用此策略。 如果你具有这些权限，则不会显示任何错误。

4. 单击“查看 + 创建”以查看分配。显示的内容应该类似于以下屏幕截图。

![DINE 策略 - 查看流量分析](./media/traffic-analytics/policy-two-review.png) 


### <a name="results"></a>结果

若要检查结果，请打开“合规性”选项卡，然后搜索分配名称。
策略运行后，显示的内容应如以下屏幕截图所示。 如果策略未运行，请稍等一会儿。

![DINE 策略结果 - 流量分析](./media/traffic-analytics/policy-two-results.png)  

### <a name="remediation"></a>补救

若要手动修正，请在上面显示的合规性选项卡上选择“创建修正任务”

![DINE 策略修正 - 流量分析](./media/traffic-analytics/policy-two-remediate.png) 


## <a name="troubleshooting"></a>疑难解答

### <a name="remediation-task-fails-with-policyauthorizationfailed-error-code"></a>修正任务失败并出现“PolicyAuthorizationFailed”错误代码。

示例错误“策略分配 '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' 资源标识没有所需的权限，无法创建部署。”

在这种情况下，必须手动为分配的托管标识授予访问权限。 转到相应的订阅/资源组（包含策略参数中提供的资源），并为策略创建的托管标识授予参与者访问权限。 在以上示例中，“b67334e8770a4afc92e7a929”必须是参与者。


## <a name="next-steps"></a>后续步骤 

-   了解 [NSG 流日志内置策略](./nsg-flow-logs-policy-portal.md)
-   详细了解[流量分析](./traffic-analytics.md)
-   详细了解[网络观察程序](./index.yml)
