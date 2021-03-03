---
title: 将 Sophos Cloud Optix data 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Sophos Cloud Optix 连接器将日志请求到 <PRODUCT NAME> Azure Sentinel。 查看 <PRODUCT NAME> 工作簿中的数据、创建警报并改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700794"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>将 Sophos Cloud Optix 连接到 Azure Sentinel

> [!IMPORTANT]
> Sophos Cloud Optix 连接器目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

Sophos Cloud Optix 连接器可让你轻松地将所有 Sophos Cloud Optix security 解决方案日志与你的 Azure Sentinel 连接起来，查看仪表板、创建自定义警报，以及改进调查。  此功能使你可以更深入地了解你的组织的云安全性和符合性状况，并改善你的云安全操作功能。 Sophos Cloud Optix 和 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-sophos-cloud-optix"></a>配置并连接 Sophos Cloud Optix

Sophos Cloud Optix 可以将日志直接集成到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，并选择 " **Sophos Cloud Optix (预览")**。

1. 选择 " **打开连接器" 页面**。

1. 从连接器页面复制并保存 **工作区 ID** 和 **主密钥** 。

1. 按照 Sophos 中的说明与从第三个步骤) 开始 ([集成 Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) 。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**自定义日志**" 部分下的 "**日志**" 下的 " *SophosCloudOptix_CL* " 表中。

若要查询 Sophos Cloud Optix data，请 `SophosCloudOptix_CL` 在查询窗口中输入。 请参阅 "连接器" 页上的 " **后续步骤** " 选项卡和 [Sophos 文档](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)，了解一些有用的查询示例。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Sophos Cloud Optix 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
