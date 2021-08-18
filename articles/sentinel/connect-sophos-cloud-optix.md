---
title: 将 Sophos Cloud Optix 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Sophos Cloud Optix 连接器将 <PRODUCT NAME> 日志拉取到 Azure Sentinel 中。 在工作簿中查看 <PRODUCT NAME> 数据，创建警报，并改进调查。
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
ms.openlocfilehash: f16c76c6a29bed37fb21ebaf420b0c6c92f6ada7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253980"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>将 Sophos Cloud Optix 连接到 Azure Sentinel

> [!IMPORTANT]
> Sophos Cloud Optix 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Sophos Cloud Optix 连接器可让你轻松地将所有 Sophos Cloud Optix 安全解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报，并改进调查。  此功能可让你更深入地了解你组织的云安全性和符合性状况，并改进云安全操作功能。 Sophos Cloud Optix 与 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区所在的地理位置。

## <a name="configure-and-connect-sophos-cloud-optix"></a>配置并连接 Sophos Cloud Optix

Sophos Cloud Optix 可以直接将日志集成和导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“Sophos Cloud Optix (预览版)”。

1. 选择“打开连接器页”。

1. 从连接器页复制并保存“工作区 ID”和“主密钥”。

1. 按照 Sophos 的说明[与 Microsoft Azure Sentinel 集成](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html)（从第三步开始）。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 SophosCloudOptix_CL 表的“自定义日志”部分的“日志”中。 

若要查询 Sophos Cloud Optix 数据，请在查询窗口中输入 `SophosCloudOptix_CL`。 如果需要一些有用的查询示例，请参阅连接器页上的“后续步骤”选项卡和 [Sophos 文档](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Sophos Cloud Optix 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。