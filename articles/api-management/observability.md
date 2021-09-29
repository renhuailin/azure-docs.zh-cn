---
title: Azure API 管理中的可观测性 | Microsoft Docs
description: Azure API 管理中的所有可观测性选项的概述。
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: 02a33de6ecb83f87627b84e8606e4dcc88108679
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664535"
---
# <a name="observability-in-azure-api-management"></a>Azure API 管理中的可观测性

可观测性是指通过系统产生的数据了解系统内部状态的能力，以及探索这些数据以回答有关发生了什么事件及其原因的问题的能力。 

Azure API 管理可以帮助组织集中管理所有 API。 由于它充当所有 API 流量的单一入口点，因此它是观测 API 的理想位置。 

## <a name="observability-tools"></a>可观测性工具

下表总结了 API 管理支持的用于观测 API 的所有工具，每种工具适用于一个或多个场景：

| 工具        | 有助于    | 数据滞后时间 | 保留 | 采样 | 数据类型 | 已启用|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API 检查器](api-management-howto-api-inspector.md)** | 测试和调试 | 即时 | 最后 100 个跟踪 | 已按请求启用 | 请求跟踪 | 始终
| **内置分析** | 报告和监视 | 分钟数 | 生存期 | 100% | 报表和日志 | 始终 |
| **[Azure Monitor 指标](api-management-howto-use-azure-monitor.md)** | 报告和监视 | 分钟数 | 93 天（升级后可延期） | 100% | 指标 | 始终 |
| **[Azure Monitor 日志](api-management-howto-use-azure-monitor.md)** | 报告、监视和调试 | 分钟数 | 31 天/5GB（升级后可延期） | 100%（可调整） | 日志 | 可选 |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | 报告、监视和调试 | 秒 | 90 天/5GB（升级后可延期） | 自定义 | 日志、指标 | 可选 |
| **[通过 Azure 事件中心记录](api-management-howto-log-event-hubs.md)** | 自定义场景 | 秒 | 用户管理 | 自定义 | 自定义 | 可选 |

### <a name="self-hosted-gateway"></a>自承载网关

云中的托管网关支持上述所有工具。 [自承载网关](self-hosted-gateway-overview.md)当前不向 Azure Monitor 发送诊断日志。 但是，可以在部署自承载网关本地配置和保存日志。 有关详细信息，请参阅[为自承载网关配置云指标和日志](how-to-configure-cloud-metrics-logs.md)和[为自承载网关配置本地指标和日志](how-to-configure-local-metrics-logs.md)。

## <a name="next-steps"></a>后续步骤

* [按教程操作，详细了解 API 管理](import-and-publish.md)
