---
title: 将 VMware Carbon Black Cloud Endpoint Standard 数据连接到 Azure Sentinel| Microsoft Docs
description: 了解如何将 VMware Carbon Black Cloud Endpoint Standard 数据连接到 Azure Sentinel。
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 362245b6aa3fa4ae943925242b8f306e43efd246
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253091"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 将 VMware Carbon Black Cloud Endpoint Standard 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 VMware Carbon Black Cloud Endpoint Standard 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

VMware Carbon Black Cloud Endpoint Standard 连接器可让用户轻松地将所有 [VMware Carbon Black Cloud Endpoint Standard](https://www.carbonblack.com/products/endpoint-standard/) 安全解决方案日志连接到 Azure Sentinel，以查看仪表板、创建自定义警报以及改进调查。 VMware Carbon Black 与 Azure Sentinel 之间的集成利用 Azure Functions 以使用 REST API 来拉取日志数据。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>配置和连接 VMware Carbon Black Cloud Endpoint Standard

Azure Functions 可以直接集成并从 VMware Carbon Black Cloud Endpoint Standard 拉取事件和日志，并将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“VMware Carbon Black”连接器。
2. 选择“打开连接器页”。
3. 按照“VMware Carbon Black”页上的说明进行操作。


## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在“CarbonBlackAuditLogs_CL”、“CarbonBlackNotifications_CL”和“****CarbonBlackEvents_CL****”表下方的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接
可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 


## <a name="next-steps"></a>后续步骤
在本文档中，你了解了如何使用 Azure Function 应用将 VMware Carbon Black Cloud Endpoint Standard 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。