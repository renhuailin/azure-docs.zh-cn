---
title: Azure Front Door 标准/高级（预览）运行状况探测监视
description: 本文将帮助你了解 Azure Front Door 如何监视后端运行状况。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a856ae7d38439d11ba0ddd65ebecdda7a3d2ce48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750196"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Azure Front Door 标准/高级（预览）运行状况探测监视

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door 向每个后端发送 HTTP 或 HTTPS 请求。 这些请求允许 Azure Front Door 确定后端池中每个终结点的运行状况。 然后，Front Door 会根据这些探测响应来确定用于路由客户端请求的“最佳”后端资源。 

> [!WARNING]
> 由于 Front Door 在全局范围内存在许多边缘环境，因此针对后端的运行状况探测量可能非常大 - 从每分钟 25 个请求到最高每分钟 1200 个请求不等，具体取决于配置的运行状况探测频率。 如果默认探测频率为 30 秒，则后端的探测量应在每分钟 200 个请求左右。

## <a name="supported-protocols"></a>支持的协议

Front Door 支持通过 HTTP 或 HTTPS 协议发送探测。 这些探测通过为路由客户端请求配置的同一 TCP 端口发送，且不能重写。

## <a name="supported-http-methods-for-health-probes"></a>受支持的运行状况探测 HTTP 方法

Front Door 支持使用以下 HTTP 方法发送运行状况探测：

* **GET：** GET 方法表示检索由请求 URI 标识的所有信息（以实体形式）。
* **HEAD：** 在 HEAD 方法中，除了服务器不能在响应中返回消息正文，其他都与 GET 方法相同。 对于新的 Front Door 配置文件，默认的探测方法设置为 HEAD。

> [!NOTE]
> 为了降低后端的负载和成本，Front Door 建议将 HEAD 请求用于运行状况探测。

## <a name="health-probe-responses"></a>运行状况探测响应

| 响应  | 说明 | 
| ------------- | ------------- |
| 确定运行状况  |  200 OK 状态代码指示后端运行状况良好。 所有其他状态均视为失败。 如果出于任何原因（包括网络故障），探测未接收到有效的 HTTP 响应，则该探测被视为失败。|
| 测量延迟  | 延迟是指从我们发送探测请求前的一刻到我们收到响应的最后一个字节的一刻所测得的时钟时间。 我们为每个请求都使用新的 TCP 连接，所以该度量不会偏向使用现有热连接的后端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何确定后端运行状况

Azure Front Door 在所有算法中均使用相同的三步过程（如下所示）来确定运行状况。

1. 排除已禁用的后端。

1. 排除具有运行状况探测错误的后端：

    * 此选择是通过查看最后 n 个运行状况探测响应来完成的。 如果至少有 x 个运行状况良好，则后端将被视为运行状况良好。

    * 通过更改负载平衡设置中的 SampleSize 属性，配置 _n_。

    * 通过更改负载平衡设置中的 SuccessfulSamplesRequired 属性，配置 _x_。

1. 对于后端池中那些运行状况良好的后端，Front Door 还将额外度量和维护每个后端的延迟（往返时间）。

> [!NOTE]
> 如果单个终结点是多个后端池的成员，Azure Front Door 会优化发送到后端的运行状况探测的数量，以减少后端的负载。 将根据配置的最小采样间隔发送运行状况探测请求。 所有池中终结点的运行状况将由来自相同运行状况探测的响应确定。

## <a name="complete-health-probe-failure"></a>完成运行状况探测失败

如果后端池中每个后端的运行状况探测均为失败，则 Front Door 会将所有后端视为运行状况良好，并将跨所有后端在轮循机制分配中路由流量。

一旦有任何后端返回到正常运行状态，Front Door 即恢复正常负载平衡算法。

## <a name="disabling-health-probes"></a>禁用运行状况探测

如果后端池中有一个后端，或者后端池中只有一个后端处于活动状态，可以选择禁用运行状况探测。 这样做可以减少应用程序后端的负载。

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door 标准版/高级版](create-front-door-portal.md)。
