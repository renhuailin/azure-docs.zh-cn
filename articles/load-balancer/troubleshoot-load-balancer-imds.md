---
title: Azure 实例元数据服务 (IMDS) 的常见错误代码
titleSuffix: Azure Load Balancer
description: Azure 实例元数据服务 (IMDS) 的常见错误代码和相应缓解方法概述
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519178"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>错误代码：使用 IMDS 检索负载均衡器信息时的常见错误代码

本文介绍使用 Azure 实例元数据服务 (IMDS) 时出现的常见部署错误，以及如何解决这些错误。

## <a name="error-codes"></a>错误代码

| 错误代码 | 错误消息 | 详细信息和缓解措施 |
| --- | ---------- | ----------------- |
| 400 | 缺少必需的参数“\<ParameterName>”。 请修复请求并重试。 | 该错误代码指示缺少参数。 </br> 有关添加缺少的参数的详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)。
| 400 | 不允许参数值，或者参数“ParameterName”不允许参数值“\<ParameterValue>”。 请修复请求并重试。 | 该错误代码指示未正确配置请求格式。 </br> 有关详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)，以修复请求正文并发出重试。 |
| 400 | 意外的请求。 请检查查询参数并重试。 | 该错误代码指示未正确配置请求格式。 </br> 有关详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)，以修复请求正文并发出重试。 |
| 404 | 找不到负载均衡器元数据。 请检查 VM 是否正在使用任何非基本 SKU 负载均衡器，并稍后重试。 | 该错误代码指示虚拟机未与负载均衡器关联，或者负载均衡器是基本 SKU 而不是标准 SKU。 </br> 有关详细信息，请参阅[快速入门：使用 Azure 门户创建公共负载均衡器](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard)，以部署标准负载均衡器。|
| 404 | 找不到 API: 路径 =“\<UrlPath>”，方法 =“\<Method>” | 该错误代码指示路径配置错误。 </br> 有关详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)，以修复请求正文并发出重试。|
| 405 | 不允许 Http 方法: 路径 =“\<UrlPath>”，方法 =“\<Method>” | 该错误代码指示不支持的 HTTP 谓词。 </br> 有关详细信息，请参阅 [Azure 实例元数据服务 (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) 了解支持的谓词。 |
| 429 | 请求过多 | 该错误代码指示速率限制。 </br> 有关速率限制的详细信息，请参阅 [Azure 实例元数据服务 (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting)。|
| 400 | 请求正文大于 MaxBodyLength: … | 该错误代码指示请求大于 MaxBodyLength。 </br> 有关正文长度的详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)。|
| 400 | 参数键长度大于 MaxParameterKeyLength: … | 该错误代码指示参数键长度大于 MaxParameterKeyLength。 </br> 有关正文长度的详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)。 |
| 400 | 参数值长度大于 MaxParameterValueLength: … | 该错误代码指示参数值长度大于 MaxParameterValueLength。 </br> 有关值长度的详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)。|
| 400 | 参数头值长度大于 MaxHeaderValueLength: … | 该错误代码指示参数头值长度大于 MaxHeaderValueLength。 </br> 有关值长度的详细信息，请参阅[如何使用 Azure 实例元数据服务 (IMDS) 检索负载均衡器元数据](howto-load-balancer-imds.md#sample-request-and-response)。|
| 404 | 负载均衡器元数据 API 目前不可用。 请稍后重试 | 该错误代码指示 API 可能正在预配。 请稍后尝试请求。 |
| 404 | /metadata/loadbalancer 当前不可用 | 该错误代码指示 API 正在启用中。 请稍后尝试请求。 |
| 503 | 内部服务不可用。 请稍后重试  | 该错误代码指示 API 暂时不可用。 请稍后尝试请求。 |
|  |  |

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 实例元数据服务](../virtual-machines/windows/instance-metadata-service.md)

