---
title: 将 Azure 信息保护连接到 Azure Sentinel
description: 通过配置 Azure 信息保护数据连接器，将 Azure 信息保护中的日志记录信息流式传输到 Azure Sentinel。
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: f936208e936bfe65b28816cc373d9ba1190308e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777997"
---
# <a name="connect-data-from-azure-information-protection"></a>连接 Azure 信息保护中的数据

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 信息保护数据连接器目前以公共预览版提供。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

你可以通过配置 Azure 信息保护数据连接器，将 [Azure 信息保护](https://azure.microsoft.com/services/information-protection/)中的日志记录信息流式传输到 Azure Sentinel。 Azure 信息保护可帮助你控制和保护敏感数据，无论数据存储在云中还是本地。

如果已配置 [Azure 信息保护的中央报告](/azure/information-protection/reports-aip)，以便将此服务的日志记录信息存储在与当前为 Azure Sentinel 选择的相同的 Log Analytics 工作区，则可以跳过此数据连接器的配置。 Azure 信息保护中的日志记录信息已可在 Azure Sentinel 上使用。

但是，如果 Azure 信息保护中的日志记录信息传输至与当前为 Azure Sentinel 选择的工作区不同的 Log Analytics 工作区，请执行以下操作之一：

- 更改在 Azure Sentinel 中选择的工作区。

- 更改 Azure 信息保护的工作区，可以通过配置此数据连接器来执行此操作。
    
    如果更改了工作区，则 Azure 信息保护的新报表数据现在将存储于 Azure Sentinel 使用的工作区，而历史数据无法供 Azure Sentinel 使用。 此外，如果以前的工作区配置了自定义查询、警报或 REST API，如果用户希望继续将其用于 Azure 信息保护，则必须将这些重新配置为 Azure Sentinel 工作区。 使用 Azure 信息保护的客户端和服务不需要重新配置。

## <a name="prerequisites"></a>先决条件

- 租户为以下 Azure AD 管理员角色之一： 
    - Azure 信息保护管理员
    - 安全管理员
    - 法规管理员
    - 合规性数据管理员
    - 全局管理员
    
    > [!NOTE]
    > 如果你的租户位于[统一的标签平台](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上，则不能使用 Azure 信息保护管理员角色。
    
    这些管理员角色仅在配置 Azure 信息保护连接器时才是必需的，在 Azure Sentinel 连接到 Azure 信息保护时不需要这些角色。

- 读取和写入 Azure Sentinel 和 Azure 信息保护所使用的 Log Analytics 工作区的权限。

- Azure 信息保护已添加到 Azure 门户。 如需此步骤的帮助，请参阅[将 Azure 信息保护添加到 Azure 门户](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)。

## <a name="connect-to-azure-information-protection"></a>连接到 Azure 信息保护

如果尚未为 Azure 信息保护配置 Log Analytics 工作区，或者需要更改存储 Azure 信息保护日志记录信息的工作区，请使用以下说明。

1. 在 Azure Sentinel 中，选择“数据连接器” > “Azure 信息保护（预览版）”。

2. 选择“打开连接器页面”。

3. 在“配置”下，选择“连接 Azure 信息保护日志”。

4. 在“配置分析（预览版）”边栏选项卡上，选择当前用于 Azure Sentinel 的工作区。 如果选择其他工作区，Azure 信息保护中的报表数据将不可用于 Azure Sentinel。

5. 选择工作区后，选择“确定”。 连接器“状态”将更改为“已连接”。

6. Azure 信息保护中的报表数据存储在所选工作区的“InformationProtectionLogs_CL”表中。 
    
    若要为此报表数据使用 Azure Monitor 中的相关架构，请搜索“InformationProtectionEvents”。 有关这些事件函数的信息，请参阅 Azure 信息保护文档中的[事件函数的友好架构参考](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)部分。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure 信息保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。