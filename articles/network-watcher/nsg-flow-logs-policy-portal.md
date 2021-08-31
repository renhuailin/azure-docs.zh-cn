---
title: 快速入门 - 使用 Azure Policy 部署和管理 NSG 流日志
titleSuffix: Azure Network Watcher
description: 本文介绍如何使用内置策略来管理 NSG 流日志的部署
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 2e0ab4a22665741abb7ed4ab2569d1b55e090457
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688535"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>快速入门：使用 Azure Policy 部署和管理 NSG 流日志 

## <a name="overview"></a>概述
Azure Policy 可帮助实施组织标准并大规模评估合规性。 Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本和管理方面的要求。 在本文中，我们将使用两个可用于 NSG 流日志的内置策略来管理流日志设置。 第一个策略对未启用流日志的所有 NSG 进行标记。 第二个策略为未启用流日志的 NSG 自动部署流日志。 

如果是首次创建 Azure Policy，可仔细阅读： 
- [Azure Policy 概述](../governance/policy/overview.md) 
- [策略创建教程](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)。


## <a name="locate-the-policies"></a>查找策略
1. 转到 Azure 门户 – [portal.azure.com](https://portal.azure.com) 

通过在的顶部搜索栏中搜索“策略”，导航到 Azure Policy 页面![策略主页](./media/network-watcher-builtin-policy/1_policy-search.png)

2. 转到左侧窗格中的“分配”选项卡

![“分配”选项卡](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. 单击“分配策略”按钮 

![“分配策略”按钮](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. 单击“策略定义”下的三点菜单，查看可用策略

5. 使用“类型”筛选器并选择“内置”。 然后搜索“流日志”

此时会看到两个用于流日志的内置策略![策略列表](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. 选择要分配的策略

- 名为“应为每个网络安全组配置流日志”的审核策略将对不合规 NSG（即未启用流日志记录的 NSG）进行标记
- 名为“使用目标网络安全组部署流日志资源”的策略包含一个部署操作，它会对没有流日志的所有 NSG 启用流日志

下面是每条策略的单独说明。  

## <a name="audit-policy"></a>审核策略 

### <a name="how-the-policy-works"></a>策略的工作原理

此策略检查“Microsoft.Network/networkSecurityGroups”类型的所有现有 ARM 对象（即查看给定范围内的所有 NSG），并通过 NSG 的“流日志”属性检查是否存在链接的流日志。 如果该属性不存在，则会对 NSG 进行标记。

若要查看策略的完整定义，可访问[“定义”选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)，并搜索“流日志”来查找策略

### <a name="assignment"></a>分配

1. 填写策略详细信息

- 范围：订阅是一种常见选择，你也可选择与你相关的管理组或资源组。  
- 策略定义：应按照“查找策略”部分所示进行选择。
- 分配名称：选择一个描述性的名称 

2. 单击“查看 + 创建”以查看分配

此策略不需要任何参数。 分配审核策略时，无需在“修正”选项卡中填写详细信息。  

![审核策略评审](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>结果

若要检查结果，请打开“合规性”选项卡，然后搜索分配名称。
策略运行后，显示的内容应该类似于以下屏幕截图。 如果策略未运行，请稍等一会儿。 

![审核策略结果](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Deploy-If-not-exists 策略 

### <a name="policy-structure"></a>策略结构

此策略检查“Microsoft.Network/networkSecurityGroups”类型的所有现有 ARM 对象（即查看给定范围内的所有 NSG），并通过 NSG 的“流日志”属性检查是否存在链接的流日志。 如果该属性不存在，策略将部署流日志。 

若要查看策略的完整定义，可访问[“定义”选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)，并搜索“流日志”以查找该策略。 

### <a name="assignment"></a>分配

1. 填写策略详细信息

- 范围：订阅是一种常见选择，你也可选择与你相关的管理组或资源组。  
- 策略定义：应按照“查找策略”部分所示进行选择。
- 分配名称：选择一个描述性的名称 

2. 添加策略参数 

网络观察程序服务是一项区域性服务。 通过这些参数可执行部署流日志的策略操作。 
- NSG 区域：策略的目标 Azure 区域
- 存储 ID：存储帐户的完整资源 ID。 注意：此存储帐户应与 NSG 位于同一区域。 
- 网络观察程序 RG：包含网络观察程序资源的资源组的名称。 如果尚未对其进行重命名，可输入默认值“NetworkWatcherRG”。
- 网络观察程序名称：区域网络观察程序服务的名称。 格式：NetworkWatcher_RegionName。 示例：NetworkWatcher_centralus。 请查看完整列表。

![DINE 策略参数](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. 添加修正详细信息

- 如果希望策略影响现有资源，请勾选“创建修正任务” 
- “创建托管标识”应已选中
- 已选择托管标识之前所在的位置 
- 你将需要参与者或所有者权限才能使用此策略。 如果你具有这些权限，则不会显示任何错误。

![DINE 策略修正](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. 单击“查看 + 创建”以查看分配。显示的内容应该类似于以下屏幕截图。

![DINE 策略评审](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>结果

若要检查结果，请打开“合规性”选项卡，然后搜索分配名称。
策略运行后，显示的内容应如以下屏幕截图所示。 如果策略未运行，请稍等一会儿。

![DINE 策略结果](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>后续步骤 

-   了解[流量分析内置策略](./traffic-analytics-policy-portal.md)
-   使用此[教程](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md)深入了解如何使用 ARM 模板部署流日志和流量分析。
-   详细了解[网络观察程序](./index.yml)