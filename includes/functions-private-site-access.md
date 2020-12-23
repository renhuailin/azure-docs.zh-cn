---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020972"
---
Azure 专用终结点是一种网络接口，你可通过该接口私密安全地连接到由 Azure 专用链接提供支持的服务。  专用终结点使用虚拟网络中的专用 IP 地址，从而将该服务有效地引入虚拟网络。

你可以为 [高级](../articles/azure-functions/functions-premium-plan.md) 和 [应用服务](../articles/azure-functions/functions-scale.md#app-service-plan) 计划中托管的函数使用专用终结点。

为函数创建入站专用终结点连接时，还需要一个 DNS 记录来解析专用地址。  默认情况下，在使用 Azure 门户创建专用终结点时，将为你创建专用 DNS 记录。

若要了解详细信息，请参阅 [使用 Web 应用的专用终结点](../articles/app-service/networking/private-endpoint.md)。