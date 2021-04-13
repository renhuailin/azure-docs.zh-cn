---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102032920"
---
对该容器的查询在用于 `ApiKey` 的 Azure 资源的定价层计费。

Azure 认知服务容器在未连接到计量/计费终结点的情况下无权运行。 必须始终让容器可以向计费终结点传送计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

### <a name="connect-to-azure"></a>连接到 Azure

容器需要计费参数值才能运行。 这些值使容器可以连接到计费终结点。 容器约每 10 到 15 分钟报告一次使用情况。 如果容器未在允许的时间范围内连接到 Azure，容器将继续运行，但不会为查询提供服务，直到计费终结点恢复。 尝试连接按 10 到 15 分钟的相同时间间隔进行 10 次。 如果无法在 10 次尝试内连接到计费终结点，容器会停止处理请求。 有关发送给 Microsoft 进行计费的信息的示例，请参阅[认知服务容器常见问题解答](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work)。

### <a name="billing-arguments"></a>计费参数

当以下三个选项都提供了有效值时，<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">`docker run` <span class="docon docon-navigate-external x-hidden-focus"></span></a> 命令会启动容器：

| 选项 | 说明 |
|--------|-------------|
| `ApiKey` | 用于跟踪计费信息的认知服务资源的 API 密钥。<br/>必须将此选项的值设置为 `Billing` 中指定的已预配资源的 API 密钥。 |
| `Billing` | 用于跟踪计费信息的认知服务资源的终结点。<br/>必须将此选项的值设置为已预配的 Azure 资源的终结点 URI。|
| `Eula` | 表示已接受容器的许可条款。<br/>此选项的值必须设置为 **accept**。 |
