---
title: 将 Windows 安全事件数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用安全事件连接器将所有安全事件从 Windows 系统流式传输到 Azure Sentinel 工作区。
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570546"
---
# <a name="connect-windows-security-events"></a>连接 Windows 安全事件 

利用安全事件连接器，可以将所有安全事件从 Windows 系统（服务器和工作站、物理和虚拟）流式传输到 Azure Sentinel 工作区。 这使你可以在仪表板中查看 Windows 安全事件、在创建自定义警报时使用它们以及依靠它们改进调查，使你能够更深入地了解组织的网络并扩展安全操作功能。 可以从以下集中选择要进行流式传输的事件：<a name="event-sets"></a>

- 所有事件 - 所有 Windows 安全事件和 AppLocker 事件。
- 通用 - 一组标准事件，用于审核目的。 此集中包含完整用户审核线索。 例如，它包含用户登录和用户注销事件（事件 ID 4624 和 4634）。 还有审核操作，如安全组更改、关键域控制器 Kerberos 操作以及符合公认最佳做法的其他事件类型。

    通用事件集可能包含某些不太常见的事件类型。  这是因为通用集的主旨是将事件量减少到更易于管理的级别，同时仍保持完整审核线索功能。

- 最小 - 一小部分事件，可能指示潜在威胁。 此集不包含完整审核线索。 它仅涵盖可能指示成功违反的事件以及发生率非常低的其他重要事件。 例如，它包含成功和失败的用户登录（事件 ID 4624 和 4625），但不包含对审核很重要，而对违反检测毫无意义且数量相对较多的注销信息 (4634)。 此集的大多数数据量由登录事件和进程创建事件（事件 ID 4688）组成。

- 无 - 没有安全事件或 AppLocker 事件。 （此设置用于禁用连接器。）

    以下列表提供对每个集的安全和 App Locker 事件 ID 的完整细分：

    | 事件集 | 收集的事件 ID |
    | --- | --- |
    | **最少** | 1102、4624、4625、4657、4663、4688、4700、4702、4719、4720、4722、4723、4724、4727、4728、4732、4735、4737、4739、4740、4754、4755、4756、4767、4799、4825、4946、4948、4956、5024、5033、8001、8002、8003、8004、8005、8006、8007、8222 |
    | **通用** | 1、299、300、324、340、403、404、410、411、412、413、431、500、501、1100、1102、1107、1108、4608、4610、4611、4614、4622、4624、4625、4634、4647、4648、4649、4657、4661、4662、4663、4665、4666、4667、4688、4670、4672、4673、4674、4675、4689、4697、4700、4702、4704、4705、4716、4717、4718、4719、4720、4722、4723、4724、4725、4726、4727、4728、4729、4733、4732、4735、4737、4738、4739、4740、4742、4744、4745、4746、4750、4751、4752、4754、4755、4756、4757、4760、4761、4762、4764、4767、4768、4771、4774、4778、4779、4781、4793、4797、4798、4799、4800、4801、4802、4803、4825、4826、4870、4886、4887、4888、4893、4898、4902、4904、4905、4907、4931、4932、4933、4946、4948、4956、4985、5024、5033、5059、5136、5137、5140、5145、5632、6144、6145、6272、6273、6278、6416、6423、6424、8001、8002、8003、8004、8005、8006、8007、8222、26401、30004 |

> [!NOTE]
> 可以从 Azure 安全中心或 Azure Sentinel 配置单个工作区上下文中的安全事件集合，但不能同时从这两者进行配置。 如果你要将 Azure Sentinel 加入已从 Azure 安全中心获得 Azure Defender 警报并设置为收集安全事件的工作区，则有两个选项：
> - 保留 Azure 安全中心的安全事件集合不变。 你将能够在 Azure Sentinel 以及 Azure Defender 中查询和分析这些事件。 但是，你将不能监视连接器的连接状态或在 Azure Sentinel 中更改其配置。 如果这对你很重要，请考虑第二种选择。
>
> - 在 Azure 安全中心中[禁用安全事件集合](../security-center/security-center-enable-data-collection.md)，然后才在 Azure Sentinel 中添加安全事件连接器。 与第一种选择一样，你将能够在 Azure Sentinel 和 Azure Defender/ASC 中查询和分析事件，但现在你将能够监视连接器的连接状态或在且仅在 Azure Sentinel 中更改其配置。

## <a name="set-up-the-windows-security-events-connector"></a>设置 Windows 安全事件连接器

若要在 Azure Sentinel 中收集 Windows 安全事件，请执行以下操作：

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

1. 选择要流式传输的事件集（[“所有”、“通用”或“最小”](#event-sets)）。

1. 单击“更新”  。

1. 若要对 Windows 安全事件使用 Log Analytics 中的相关架构，请在查询窗口中键入 `SecurityEvent`。

## <a name="validate-connectivity"></a>验证连接

可能需要大约 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>为异常 RDP 登录检测配置安全事件连接器

> [!IMPORTANT]
> 异常 RDP 登录检测目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以将机器学习 (ML) 应用于安全事件数据，以确定远程桌面协议 (RDP) 登录活动的异常情况。 方案包括：

- 异常 IP - 在过去30天内很少或从未观察到 IP 地址

- 异常地理位置 - 在过去30天内很少或从未观察到 IP 地址、城市、国家/地区和 ASN

- 新用户 - 根据之前 30 天的数据，新用户用于登录的 IP 地址和地理位置这两项或其中一项不应出现。

**配置说明**

1. 必须通过“安全事件”数据连接器收集 RDP 登录数据（事件 ID 4624）。 请确保选择了[事件集](#event-sets)（“无”除外）以流式传输到 Azure Sentinel 中。

1. 在 Azure Sentinel 门户中，单击“分析”，然后单击“规则模板”选项卡 。选择“(预览版)匿名 RDP 登录检测”并将“状态”滑块移动到“已启用”  。

    > [!NOTE]
    > 由于机器学习算法需要 30 天的数据来构建用户行为的基准配置文件，因此必须先允许收集 30 天的安全事件数据，然后才能检测任何事件。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Windows 安全事件连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats-built-in.md)或[自定义](tutorial-detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。

