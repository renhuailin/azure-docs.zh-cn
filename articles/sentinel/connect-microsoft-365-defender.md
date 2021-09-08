---
title: 将 Microsoft 365 Defender 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Microsoft 365 Defender 中的事件、警报和原始事件数据引入 Azure Sentinel 中。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: c31ed32e13e8b71b5ad6253d5e157b5adc2096bf
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260027"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>将 Microsoft 365 Defender 的数据连接到 Azure Sentinel

> [!IMPORTANT]
>
> Microsoft 365 Defender 以前被称为 Microsoft 威胁防护 (MTP)  。
>
> Microsoft Defender for Endpoint 以前被称为 Microsoft Defender 高级威胁防护 (MDATP)。  
>
> 你可能会看到旧名称，它仍将使用一段时间。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>背景

通过 Azure Sentinel [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) 连接器与事件的集成，可将所有 M365D 事件和警报都流式传输到 Azure Sentinel，并使这些事件在这两个门户之间保持同步。 M365D 事件包含其所有警报、实体和其他相关信息，它们通过 M365D 的 Microsoft defender For Endpoint、Microsoft Defender for Identity、Microsoft Defender for Office 365 和 Microsoft Cloud App Security 组件服务中的警报得到扩充，并将这些警报分组到一起   。

利用连接器，还可将 Microsoft Defender for Endpoint“高级搜寻”事件流式传输到 Azure Sentinel，以便将 Defender for Endpoint 高级搜寻查询复制到 Azure Sentinel 中；利用 Defender for Endpoint 原始事件数据扩充 Sentinel 警报，从而提供更多见解；并在 Log Analytics 中以更长的保留期存储日志。

要详细了解事件集成和高级搜寻事件收集，请参阅 [Microsoft 365 Defender 与 Azure Sentinel 集成](microsoft-365-defender-sentinel-integration.md)。

> [!IMPORTANT]
>
> Microsoft 365 Defender 连接器目前提供预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="prerequisites"></a>先决条件

- 必须具有 Microsoft 365 Defender 的有效许可证，如 [Microsoft 365 Defender 先决条件](/microsoft-365/security/mtp/prerequisites)中所述。 

- 必须是 Azure Active Directory 中的“全局管理员”或“安全管理员” 。

## <a name="connect-to-microsoft-365-defender"></a>连接到 Microsoft 365 Defender

1. 在 Azure Sentinel 中，选择“数据连接器”，从库中选择“Microsoft 365 Defender (预览版)”，然后选择“打开连接器页”  。

1. 在“连接事件和警报”部分的“配置”下，选择“连接事件和警报”按钮  。

1. 若要避免事件重复，建议选中带有“为这些产品关闭所有 Microsoft 事件创建规则”标签的复选框。

    > [!NOTE]
    > 启用 Microsoft 365 Defender 连接器后，所有 M365D 组件的连接器（本文开头部分所述的连接器）都将自动连接到后台。 要使其中一个组件的连接器断开连接，必须先断开 Microsoft 365 Defender 连接器的连接。

1. 若要查询 Microsoft 365 Defender 事件数据，请在查询窗口中使用以下语句：
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. 若要收集 Microsoft Defender for Endpoint 高级搜寻事件，可从其相应的高级搜寻表中收集以下类型的事件。

    1. 选中包含你要收集的事件类型的表的复选框：

       | 表名称 | 事件类型 |
       |-|-|
       | DeviceInfo | 计算机信息（包括 OS 信息） |
       | DeviceNetworkInfo | 计算机的网络属性 |
       | DeviceProcessEvents | 进程创建及相关事件 |
       | DeviceNetworkEvents | 网络连接及相关事件 |
       | DeviceFileEvents | 文件创建、修改和其他文件系统事件 |
       | DeviceRegistryEvents | 注册表项的创建和修改 |
       | DeviceLogonEvents | 登录和其他身份验证事件 |
       | DeviceImageLoadEvents | DLL 加载事件 |
       | DeviceEve | 其他事件类型 |
       | DeviceFileCertificateInfo | 带签名的文件的证书信息 |
       |

    1. 单击“应用更改”。

    1. 若要在 Log Analytics 中查询高级搜寻表，请在“查询”窗口中输入上述列表中的表名。

## <a name="verify-data-ingestion"></a>验证数据引入

“连接器”页中的数据图指示你正在引入数据。 你会注意到，每个突发事件、警报和事件都显示一行，事件行聚合了所有已启用的表中的事件量。 启用连接器后，可使用以下 KQL 查询生成更具体的图。

使用以下 KQL 查询可生成传入的 Microsoft 365 Defender 事件的图：

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

使用以下 KQL 查询可生成单个表的事件量图（请将 DeviceEvents 表更改为所选的表）：

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

在“后续步骤”选项卡中，可找到已包含的一些有用的工作簿、示例查询和分析规则模板。 你可在现成虚拟机上运行或者修改并保存它们。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Microsoft 365 Defender 连接器将 Microsoft 365 Defender 事件和 Microsoft Defender for Endpoint 高级搜寻事件数据集成到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
