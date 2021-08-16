---
title: 将 Windows 安全事件数据连接到 Azure Sentinel（选项卡式版本）| Microsoft Docs
description: 了解如何使用 Windows 安全事件连接器将所有安全事件从 Windows 系统流式传输到 Azure Sentinel 工作区。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2021
ms.author: yelevin
ms.openlocfilehash: 364426e5b89915f51ec61e7c878e885045964554
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063443"
---
# <a name="connect-to-windows-servers-to-collect-security-events"></a>连接到 Windows 服务器以收集安全事件

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Windows 安全事件连接器可以流式传输来自连接到 Azure Sentinel 工作区的任何 Windows 服务器（物理或虚拟、本地或任何云中）的安全事件。 这使你可以在仪表板中查看 Windows 安全事件、在创建自定义警报时使用它们以及依靠它们改进调查，使你能够更深入地了解组织的网络并扩展安全操作功能。 

此连接器现有两个版本：安全事件是旧版本，采用的是 Log Analytics 代理（有时称为 MMA 或 OMS 代理），而 Windows 安全事件是新版本，目前为预览版，采用的是新的 Azure Monitor 代理 (AMA)。 本文档介绍了有关这两个连接器的信息。 你可从以下选项卡中选择，查看与所选连接器有关的信息。

> [!NOTE]
> 若要从不是 Azure 虚拟机的任何系统收集安全事件，此系统必须已安装并启用 [Azure Arc](../azure-monitor/agents/azure-monitor-agent-install.md)，然后才能启用其中的任意连接器。
>
> 这包括：
> - 安装在物理计算机上的 Windows 服务器
> - 安装在本地虚拟机上的 Windows 服务器
> - 安装在非 Azure 云中虚拟机上的 Windows 服务器

# <a name="log-analytics-agent-legacy"></a>[Log Analytics 代理（旧版本）](#tab/LAA)

可以从以下集中选择要进行流式传输的事件：<a name="event-sets"></a>

- 所有事件 - 所有 Windows 安全事件和 AppLocker 事件。
- 通用 - 一组标准事件，用于审核目的。 此集中包含完整用户审核线索。 例如，它包含用户登录和用户注销事件（事件 ID 4624 和 4634）。 还有审核操作，如安全组更改、关键域控制器 Kerberos 操作以及符合公认最佳做法的其他事件类型。

    通用事件集可能包含某些不太常见的事件类型。  这是因为通用集的主旨是将事件量减少到更易于管理的级别，同时仍保持完整审核线索功能。

- 最小 - 一小部分事件，可能指示潜在威胁。 此集不包含完整审核线索。 它仅涵盖可能指示成功违反的事件以及发生率非常低的其他重要事件。 例如，它包含成功和失败的用户登录（事件 ID 4624 和 4625），但不包含对审核很重要，而对违反检测毫无意义且数量相对较多的注销信息 (4634)。 此集的大多数数据量由登录事件和进程创建事件（事件 ID 4688）组成。

- 无 - 没有安全事件或 AppLocker 事件。 （此设置用于禁用连接器。）

> [!NOTE]
> 可以从 Azure 安全中心或 Azure Sentinel 配置单个工作区上下文中的安全事件集合，但不能同时从这两者进行配置。 如果你要将 Azure Sentinel 加入已从 Azure 安全中心获得 Azure Defender 警报并设置为收集安全事件的工作区，则有两个选项：
> - 保留 Azure 安全中心的安全事件集合不变。 你将能够在 Azure Sentinel 以及 Azure Defender 中查询和分析这些事件。 但是，你将不能监视连接器的连接状态或在 Azure Sentinel 中更改其配置。 如果这对你很重要，请考虑第二种选择。
>
> - 在 Azure 安全中心中[禁用安全事件集合](../security-center/security-center-enable-data-collection.md)，然后才在 Azure Sentinel 中添加安全事件连接器。 与第一种选择一样，你将能够在 Azure Sentinel 和 Azure Defender/ASC 中查询和分析事件，但现在你将能够监视连接器的连接状态或在且仅在 Azure Sentinel 中更改其配置。

# <a name="azure-monitor-agent-new"></a>[Azure Monitor 代理（新版本）](#tab/AMA)

> [!IMPORTANT]
>
> - 采用 Azure Monitor 代理 (AMA) 的 Windows 安全事件数据连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[Azure Monitor 代理](../azure-monitor/agents/azure-monitor-agent-overview.md)使用数据收集规则 (DCR) 来定义要从每个代理收集的数据。 数据收集规则可以大规模地管理集合设置，同时还为计算机的子集启用了有作用域的唯一配置。 这些规则独立存在于工作区与虚拟机之外，这意味着这些规则在定义后可在各种计算机和环境中重复使用。 请参阅[为 Azure Monitor 代理配置数据收集](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)。

除了可选择使用旧连接器引入预选择事件集（“所有事件”、“最少”或“通用”）之外，数据收集规则还允许生成自定义筛选器，以便选择想要引入的确切事件。 Azure Monitor 代理使用这些规则在数据源中筛选数据，并且仅引入所需要的事件，对于其他所有内容则不做处理。 这可在数据引入成本方面节省大量的资金！

本文档演示了如何创建数据收集规则。

> [!NOTE]
> **与其他代理共存**
> 
> Azure Monitor 代理可以与现有代理共存，因此你可以在计算或迁移期间继续使用旧连接器。 由于对现有解决方案的支持有限，当新连接器为预览版时，请格外注意这一点。 但在收集重复数据时应该小心，因为这可能会使查询结果扭曲，并导致数据提取和保留的额外费用。

---

## <a name="set-up-the-windows-security-events-connector"></a>设置 Windows 安全事件连接器

若要在 Azure Sentinel 中收集 Windows 安全事件，请执行以下操作：

# <a name="log-analytics-agent-legacy"></a>[Log Analytics 代理（旧版本）](#tab/LAA)

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 在连接器列表中，单击“安全事件”，然后单击右下角的“打开连接器页面”按钮。 然后按照“说明”选项卡下的屏幕说明进行操作（如此部分其余内容所述）。

1. 验证你是否拥有相应的权限，如连接器页上的“先决条件”部分中下的内容所述。

1. 在要将其安全事件流式传输到 Azure Sentinel 的计算机上下载并安装 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)（也称为 Microsoft Monitoring Agent 或 MMA）。

    对于 Azure 虚拟机：
    
    1. 单击“在 Azure Windows 虚拟机上安装代理”，然后单击下方显示的链接。
    1. 对于要连接的每个虚拟机，在右侧显示的列表中单击其名称，然后单击“连接”。

    对于非 Azure Windows 计算机（物理、虚拟本地或其他云中的虚拟）：

    1. 单击“在非 Azure Windows 计算机上安装代理”，然后单击下方显示的链接。
    1. 单击在“Windows 计算机”下方右侧显示的相应下载链接。
    1. 使用下载的可执行文件，在所选 Windows 系统上安装代理，并使用上面提到的下载链接下显示的“工作区 ID 和密钥”对它进行配置。

    > [!NOTE]
    >
    > 若要允许没有必要 Internet 连接的 Windows 系统仍可将事件流式传输到 Azure Sentinel，请使用右下方的链接在单独计算机上下载并安装 OMS 网关以充当代理。  仍需要在要收集其事件的每个 Windows 系统上安装 Log Analytics 代理。
    >
    > 有关此方案的详细信息，请参阅 [Log Analytics 网关文档](../azure-monitor/agents/gateway.md)。

    有关其他安装选项和更多详细信息，请参阅 [Log Analytics 代理文档](../azure-monitor/agents/agent-windows.md)。

1. 选择要流式传输的事件集（“所有”、“通用”或“最少”）。 请参阅“最少”和“通用”事件集中[包含的事件 ID 列表](#event-id-reference)。

1. 单击“更新”  。

1. 若要对 Windows 安全事件使用 Log Analytics 中的相关架构，请在查询窗口中键入 `SecurityEvent`。

# <a name="azure-monitor-agent-new"></a>[Azure Monitor 代理（新版本）](#tab/AMA)

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 在连接器列表中，单击“Windows 安全事件（预览版）”，然后单击右下角的“打开连接器页面”按钮。 然后按照“说明”选项卡下的屏幕说明进行操作（如此部分其余内容所述）。

1. 验证你是否拥有相应的权限，如连接器页上的“先决条件”部分中下的内容所述。

    1. 你必须对工作区以及要从中引入 Windows 安全事件的所有数据源拥有读取和写入权限。

    1. 若要从不是 Azure VM 的 Windows 计算机收集事件，此计算机（物理的或虚拟的）必须已安装并启用 Azure Arc。 [了解详细信息](../azure-monitor/agents/azure-monitor-agent-install.md)。

1. 在“配置”下，选择“+ 添加数据收集规则”。 “创建数据收集规则”向导将在右侧打开。

1. 在“基本信息”下，输入规则名称，并指定将在其中创建数据收集规则 (DCR) 的订阅和资源组。 这并不要求受监视计算机及其关联必须位于同一资源组或订阅中，只要它们位于同一租户中即可。

1. 在“资源”选项卡中，选择“+ 添加资源”以添加将要应用数据收集规则的计算机。 “选择作用域”对话框将打开，并显示可用订阅的列表。 展开订阅以查看其资源组，然后展开资源组以查看可用的计算机。 列表中将显示 Azure 虚拟机和已启用 Azure Arc 的服务器。 你可以选中订阅或资源组的复选框以选择它们包含的所有计算机，也可以选择单个计算机。 选定所有需要的计算机，然后选择“应用”。 此过程结束时，Azure Monitor 代理将安装在任何尚未安装该代理的选定计算机上。

1. 在“收集”选项卡上，选择要收集的[事件集](#event-id-reference)，或选择“自定义”以指定其他日志或使用 [XPath 查询](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)筛选事件。 在框中输入表达式，表达式的计算结果为要收集事件的特定 XML 条件，然后选择“添加”。 一个框中最多可输入 20 个表达式，一项规则中最多可有 100 个输入框。

    若要详细了解[数据收集规则](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr)，请参阅 Azure Monitor 文档。

    > [!NOTE]
    > - 请确保仅查询 Windows 安全性和 AppLocker 日志。 来自其他 Windows 日志的事件或来自其他环境的安全日志的事件可能未遵循 Windows 安全事件架构，并且无法正确分析，在这种情况下，这些事件不会被引入工作区。
    >
    > - Azure Monitor 代理仅支持 [XPath 版本 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) 的 XPath 查询。

1. 添加所有需要的筛选表达式后，选择“下一步：查看 + 创建”。

1. 看到“验证通过”的消息后，选择“创建”。 

你将在连接器页面的“配置”下看到所有数据收集规则（包括通过 API 创建的规则）。 你还可以在此处编辑或删除现有规则。

> [!TIP]
> 将 PowerShell cmdlet 命令 Get-WinEvent 与 -FilterXPath 参数配合使用可测试 XPath 查询的有效性。 以下脚本显示了一个示例：
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
> - 如果返回事件，则查询有效。
> - 如果收到消息“找不到任何与指定的选择条件匹配的事件。”，则查询可能有效，但在本地计算机上没有匹配的事件。
> - 如果收到消息“指定的查询无效”，则查询语法无效。

### <a name="create-data-collection-rules-using-the-api"></a>使用 API 创建数据收集规则

你还可使用 API 创建数据收集规则（[请参阅架构](/rest/api/monitor/data-collection-rules)），在需要创建大量规则（例如你是 MSSP）的情况下，这将提供极大的便利。 以下示例可用作创建规则的模板：

**请求 URL 和标头**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```
**请求 URL 和标头**

```json
{
    "location": "eastus",
    "properties": {
        "dataSources": {
            "windowsEventLogs": [
                {
                    "streams": [
                        "Microsoft-SecurityEvent"
                    ],
                    "xPathQueries": [
                        "Security!*[System[(EventID=) or (EventID=4688) or (EventID=4663) or (EventID=4624) or (EventID=4657) or (EventID=4100) or (EventID=4104) or (EventID=5140) or (EventID=5145) or (EventID=5156)]]"
                    ],
                    "name": "eventLogsDataSource"
                }
            ]
        },
        "destinations": {
            "logAnalytics": [
                {
                    "workspaceResourceId": "/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.OperationalInsights/workspaces/centralTeamWorkspace",
                    "name": "centralWorkspace"
                }
            ]
        },
        "dataFlows": [
            {
                "streams": [
                    "Microsoft-SecurityEvent"
                ],
                "destinations": [
                    "centralWorkspace"
                ]
            }
        ]
    }
}
```



---

## <a name="validate-connectivity"></a>验证连接

可能需要大约 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>为异常 RDP 登录检测配置安全事件/ Windows 安全事件连接器

> [!IMPORTANT]
> 异常 RDP 登录检测目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以将机器学习 (ML) 应用于安全事件数据，以确定远程桌面协议 (RDP) 登录活动的异常情况。 方案包括：

- 异常 IP - 在过去30天内很少或从未观察到 IP 地址

- 异常地理位置 - 在过去30天内很少或从未观察到 IP 地址、城市、国家/地区和 ASN

- 新用户 - 根据之前 30 天的数据，新用户用于登录的 IP 地址和地理位置这两项或其中一项不应出现。

**配置说明**

1. 必须通过安全事件或 Windows 安全事件数据连接器来收集 RDP 登录数据（事件 ID 4624）。 请确保已选择除“无”之外的[事件集](#event-id-reference)，或已创建包含此事件 ID 的数据收集规则，以便将事件流式传输到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“分析”，然后单击“规则模板”选项卡 。选择“(预览版)匿名 RDP 登录检测”并将“状态”滑块移动到“已启用”  。

    > [!NOTE]
    > 由于机器学习算法需要 30 天的数据来构建用户行为的基线配置文件，因此必须先允许收集 30 天的 Windows 安全事件数据，然后才能检测任何事件。

## <a name="event-id-reference"></a>事件 ID 引用

以下列表提供对每个集的安全和 App Locker 事件 ID 的完整细分：

| 事件集 | 收集的事件 ID |
| --- | --- |
| **最少** | 1102、4624、4625、4657、4663、4688、4700、4702、4719、4720、4722、4723、4724、4727、4728、4732、4735、4737、4739、4740、4754、4755、4756、4767、4799、4825、4946、4948、4956、5024、5033、8001、8002、8003、8004、8005、8006、8007、8222 |
| **通用** | 1、299、300、324、340、403、404、410、411、412、413、431、500、501、1100、1102、1107、1108、4608、4610、4611、4614、4622、4624、4625、4634、4647、4648、4649、4657、4661、4662、4663、4665、4666、4667、4688、4670、4672、4673、4674、4675、4689、4697、4700、4702、4704、4705、4716、4717、4718、4719、4720、4722、4723、4724、4725、4726、4727、4728、4729、4733、4732、4735、4737、4738、4739、4740、4742、4744、4745、4746、4750、4751、4752、4754、4755、4756、4757、4760、4761、4762、4764、4767、4768、4771、4774、4778、4779、4781、4793、4797、4798、4799、4800、4801、4802、4803、4825、4826、4870、4886、4887、4888、4893、4898、4902、4904、4905、4907、4931、4932、4933、4946、4948、4956、4985、5024、5033、5059、5136、5137、5140、5145、5632、6144、6145、6272、6273、6278、6416、6423、6424、8001、8002、8003、8004、8005、8006、8007、8222、26401、30004 |
|

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Windows 安全事件连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats-built-in.md)或[自定义](tutorial-detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。

