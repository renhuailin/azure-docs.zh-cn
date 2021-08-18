---
title: 将 F5 BIG-IP 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 F5 BIG-IP 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aae69e8ff7cb0c3002bb9333f6d7685b9e247a28
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254112"
---
# <a name="connect-your-f5-big-ip-appliance"></a>连接 F5 BIG-IP 设备 

通过 F5 BIG-IP 连接器，可以将所有 F5 BIG-IP 日志轻松连接到 Azure Sentinel，以查看工作簿、创建自定义警报和改进调查。 这样，用户就可以更深入地了解组织的网络并改善安全操作功能。 F5 BIG-IP 和 Azure Sentinel 之间的集成利用 REST API。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-f5-big-ip"></a>配置并连接 F5 BIG-IP 

F5 BIG-IP 可以直接将日志集成和导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“**数据连接器**”，然后依次选择“**F5 BIG-IP**”和“**打开连接器页**”。 
1. 若要连接 F5 BIG-IP，必须将 JSON 声明发布到系统的 API 终结点。 有关如何进行启用和配置的说明，请参阅“[将 F5 BIG-IP 与 Azure Sentinel 集成](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)”。
8. 在 F5 BIG-IP 页上，复制“工作区 ID”和“主密钥”，并按照“[将数据流式传输到 Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)”中的说明进行粘贴。
1. 完成 F5 BIG-IP 说明后，便可以在 Azure Sentinel 连接器页中看到已连接的数据类型。
1. 若要在 Log Analytics 中为 F5 BIG-IP 事件使用相关架构，请搜索 **F5Telemetry_LTM_CL**、**F5Telemetry_system_CL** 和 **F5Telemetry_ASM_CL**。


## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 F5 BIG-IP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。