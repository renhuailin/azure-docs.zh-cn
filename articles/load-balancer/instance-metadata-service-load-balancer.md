---
title: 通过使用 Azure 实例元数据服务来检索负载均衡器信息
titleSuffix: Azure Load Balancer
description: 开始了解如何使用 Azure 实例元数据服务来检索负载均衡器信息。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519076"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>通过使用 Azure 实例元数据服务来检索负载均衡器信息

IMDS（Azure 实例元数据服务）提供有关当前运行的虚拟机实例的信息。 该服务是 REST API，在某个众所周知的不可路由的 IP 地址 (169.254.169.254) 上可用。 

在将虚拟机或虚拟机集实例置于 Azure 标准负载均衡器后面时，请使用 IMDS 来检索与负载均衡器和这些实例相关的元数据。

这些元数据包括有关虚拟机或虚拟机规模集的以下信息：

* 标准 SKU 公共 IP。
* 网络接口的每个专用 IP 的负载均衡器的入站规则配置。
* 网络接口的每个专用 IP 的负载均衡器的入站规则配置。

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>使用 IMDS 访问负载均衡器元数据

若要详细了解如何访问负载平衡器元数据，请参阅[使用 Azure 实例元数据服务来访问负载均衡器信息](howto-load-balancer-imds.md)。

## <a name="troubleshoot-common-error-codes"></a>排查常见错误代码

有关常见错误代码及其缓解方法的详细信息，请参阅[在使用 IMDS 时排查常见错误代码](troubleshoot-load-balancer-imds.md)。 

## <a name="support"></a>支持

如果在多次尝试后无法检索到元数据响应，请在 Azure 门户中创建支持问题。

## <a name="next-steps"></a>后续步骤
详细了解 [Azure 实例元数据服务](../virtual-machines/windows/instance-metadata-service.md)

[部署标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)

