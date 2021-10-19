---
title: 为 Azure Monitor 代理（预览版）配置数据收集
description: 介绍如何使用 Azure Monitor 代理创建数据收集规则以从虚拟机收集数据。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/16/2021
ms.openlocfilehash: 26aa625d7ada93c4524276ee195fd279d155e8c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710312"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent"></a>为 Azure Monitor 代理配置数据收集

数据收集规则 (DCR) 定义传入 Azure Monitor 的数据，并指定应发送数据的位置。 本文介绍如何使用 Azure Monitor 代理创建数据收集规则以从虚拟机收集数据。

有关数据收集规则的完整说明，请参阅 [Azure Monitor 中的数据收集规则](data-collection-rule-overview.md)。

> [!NOTE]
> 本文介绍如何仅使用 Azure Monitor 代理为虚拟机配置数据。

## <a name="data-collection-rule-associations"></a>数据收集规则关联

若要将 DCR 应用于虚拟机，请为虚拟机创建关联。 虚拟机可能与多个 DCR 存在关联，而 DCR 可能有多个虚拟机与之关联。 这使你可以定义一组 DCR（每个都与特定要求匹配），并只将它们应用于它们适用的虚拟机。 

例如，请考虑一个环境，其中包含一组运行业务线应用程序的虚拟机，而其他虚拟机运行 SQL Server。 你可能具有一个应用于所有虚拟机的默认数据收集规则，以及专门为业务线应用程序和 SQL Server 收集数据的单独数据收集规则。 虚拟机与数据收集规则的关联类似于下图。

![该图显示托管业务线应用程序和 SQL Server 的虚拟机，这些虚拟机与名为 central-it-default 和 lob-app（用于业务线应用程序）的数据收集规则和名为 central-it-default 和 sql（用于 SQL Server）的数据收集规则关联。](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>在 Azure 门户中创建规则和关联

可以使用 Azure 门户创建数据收集规则并将订阅中的虚拟机关联到该规则。 系统会自动安装 Azure Monitor 代理，并为尚未安装它的任何虚拟机创建托管标识。

> [!IMPORTANT]
> 使用门户创建数据收集规则，还可以对目标资源启用系统分配的托管标识，以及现有的用户分配的标识（如果有）。 对于现有应用程序，除非在请求中指定用户分配的标识，否则计算机将默认使用系统分配的标识。 [了解详细信息](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)

                    

> [!NOTE]
> 如果要将数据发送到 Log Analytics，则必须在 Log Analytics 工作区所在的同一区域中创建数据收集规则。 该规则可关联到其他受支持的区域中的计算机。

在 Azure 门户的“Azure Monitor”菜单中，从“设置”部分中选择“数据收集规则”  。 单击“创建”，以创建新的数据收集规则和分配。

[![数据收集规则](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png#lightbox)

单击“添加”以创建新规则和关联集。 提供“规则名称”并指定“订阅”、“资源组”和“区域”   。 这会指定将在其中创建 DCR 的位置。 虚拟机及其关联可以处于租户中的任何订阅或资源组中。
此外，请选择相应的“平台类型”，该类型指定此规则可应用到的资源的类型。 “自定义”将允许 Windows 和 Linux 两种类型。 这样就会允许预先特选的创建体验，具有针对所选平台类型确定范围的选项。

[![数据收集规则基本信息](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png#lightbox)

在“资源”选项卡中，添加应该已经应用了数据收集规则的资源（虚拟机、虚拟机规模集、适用于服务器的 Arc）。 Azure Monitor 代理将会安装在尚未安装该代理的资源上，并且还将启用 Azure 托管标识。

[![数据收集规则虚拟机](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png#lightbox)

在“收集和传递”选项卡上，单击“添加数据源”以添加数据源和目标集 。 选择“数据源类型”，将显示供选择的对应详细信息。 对于性能计数器，可以从一组预定义对象及其采样速率中进行选择。 对于事件，可以从一组日志或设施和严重性级别中进行选择。 

[![数据源基本信息](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png#lightbox)


若要从[当前支持的数据源](azure-monitor-agent-overview.md#data-sources-and-destinations)中指定其他日志和性能计数器，或是若要使用 XPath 查询筛选事件，请选择“自定义”。 随后可以为要收集的任何特定值指定 [XPath](https://www.w3schools.com/xml/xpath_syntax.asp)。 有关示例，请参阅[示例 DCR](data-collection-rule-overview.md#sample-data-collection-rule)。

[![数据源自定义](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png#lightbox)

在“目标”选项卡上，为数据源添加一个或多个目标。 Windows 事件和 Syslog 数据源只能发送到 Azure Monitor 日志。 性能计数器可以发送到 Azure Monitor 指标和 Azure Monitor 日志。

[![目标](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

单击“添加数据源”，然后单击“查看 + 创建”以查看数据收集规则的详细信息以及与 VM 集的关联 。 单击“创建”以进行创建。

> [!NOTE]
> 创建数据收集规则和关联后，可能需要长达 5 分钟的时间才能将数据发送到目标。

## <a name="limit-data-collection-with-custom-xpath-queries"></a>使用自定义 XPath 查询限制数据收集
由于你需要为 Log Analytics 工作区中收集的任何数据付费，因此只应收集需要的数据。 使用 Azure 门户中的基本配置时，只具有筛选要收集的事件的有限能力。 对于应用程序和系统日志，这是具有特定严重性的所有日志。 对于安全日志，这是所有审核成功或所有审核失败日志。

若要指定其他筛选器，必须使用自定义配置，并指定用于筛选出不需要的事件的 XPath。 XPath 条目以 `LogName!XPathQuery` 形式进行编写。 例如，你可能只希望从应用程序事件日志中返回事件 ID 为 1035 的事件。 这些事件的 XPathQuery 为 `*[System[EventID=1035]]`。 由于要从应用程序事件日志中检索事件，因此 XPath 为 `Application!*[System[EventID=1035]]`

请参阅 [XPath 1.0 限制](/windows/win32/wes/consuming-events#xpath-10-limitations)以了解 Windows 事件日志支持的 XPath 中的限制列表。

> [!TIP]
> 将 PowerShell cmdlet `Get-WinEvent` 与 `FilterXPath` 参数一起使用，可测试 XPathQuery 的有效性。 以下脚本显示了一个示例。
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - 在上面的 cmdlet 中，“-LogName”参数的值是 XPath 查询的初始部分到“!”，只有 XPath 查询的其余部分进入 $XPath 参数。
> - 如果返回事件，则查询有效。
> - 如果收到消息“找不到任何与指定的选择条件匹配的事件。”，则查询可能有效，但在本地计算机上没有匹配的事件。
> - 如果收到消息“指定的查询无效”，则查询语法无效。 

下表显示了使用自定义 XPath 筛选事件的示例。

| 说明 |  XPath |
|:---|:---|
| 仅收集事件 ID = 4648 的系统事件 |  `System!*[System[EventID=4648]]`
| 仅收集事件 ID = 4648 且进程名称为 consent.exe 的系统事件 | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| 从系统事件日志中收集所有严重、错误、警告和信息事件，事件 ID 6（驱动程序已加载）除外 |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| 收集所有成功和失败安全事件，事件 ID 4624（成功登录）除外 |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>使用 REST API 创建规则和关联

按照以下步骤使用 REST API 创建数据收集规则和关联。

> [!NOTE]
> 如果要将数据发送到 Log Analytics，则必须在 Log Analytics 工作区所在的同一区域中创建数据收集规则。 该规则可关联到其他受支持的区域中的计算机。

1. 使用[示例 DCR](data-collection-rule-overview.md#sample-data-collection-rule) 中所示的 JSON 格式手动创建 DCR 文件。

2. 使用 [REST API](/rest/api/monitor/datacollectionrules/create#examples) 创建规则。

3. 使用 [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples) 创建每个虚拟机到数据收集规则的关联。


## <a name="create-rule-and-association-using-resource-manager-template"></a>使用资源管理器模板创建规则和关联

> [!NOTE]
> 如果要将数据发送到 Log Analytics，则必须在 Log Analytics 工作区所在的同一区域中创建数据收集规则。 该规则可关联到其他受支持的区域中的计算机。

可使用资源管理器模板为 Azure 虚拟机或已启用 Azure Arc 的服务器创建规则和关联。 请参阅[用于 Azure Monitor 中的数据收集规则的资源管理器模板示例](./resource-manager-data-collection-rules.md)以了解示例模板。


## <a name="manage-rules-and-association-using-powershell"></a>使用 PowerShell 管理规则和关联

> [!NOTE]
> 如果要将数据发送到 Log Analytics，则必须在 Log Analytics 工作区所在的同一区域中创建数据收集规则。 该规则可关联到其他受支持的区域中的计算机。

**数据收集规则**

| 操作 | 命令 |
|:---|:---|
| 获取规则 | [Get-AzDataCollectionRule](/powershell/module/az.monitor/get-azdatacollectionrule?view=azps-5.4.0&preserve-view=true) |
| 创建规则 | [New-AzDataCollectionRule](/powershell/module/az.monitor/new-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 更新规则 | [Set-AzDataCollectionRule](/powershell/module/az.monitor/set-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 删除规则 | [Remove-AzDataCollectionRule](/powershell/module/az.monitor/remove-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 更新规则的“标记” | [Update-AzDataCollectionRule](/powershell/module/az.monitor/update-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |

数据收集规则关联

| 操作 | 命令 |
|:---|:---|
| 获取关联 | [Get-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/get-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 创建关联 | [New-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/new-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 删除关联 | [Remove-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/remove-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |



## <a name="manage-rules-and-association-using-azure-cli"></a>使用 Azure CLI 管理规则和关联

> [!NOTE]
> 如果要将数据发送到 Log Analytics，则必须在 Log Analytics 工作区所在的同一区域中创建数据收集规则。 该规则可关联到其他受支持的区域中的计算机。

它作为 Azure CLI monitor-control-service 扩展的一部分启用。 [查看所有命令](/cli/azure/monitor/data-collection/rule?view=azure-cli-latest&preserve-view=true)


## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 代理](azure-monitor-agent-overview.md)。
- 详细了解[数据收集规则](data-collection-rule-overview.md)。
