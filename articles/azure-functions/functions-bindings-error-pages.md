---
title: Azure Functions 错误处理和重试指南
description: 通过指向特定绑定错误的链接，了解如何处理 Azure Functions 中的错误并在其中重试事件。
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93284456"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions 错误处理和重试

处理 Azure Functions 中的错误对于避免丢失数据、错过事件以及监视应用程序的运行状况非常重要。

本文介绍了用于错误处理的常规策略，并提供了指向绑定特定错误的链接。

## <a name="handling-errors"></a>处理错误

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>绑定错误代码

与 Azure 服务集成时，错误可能源自底层服务的 API。 以下文章的 **异常和返回代码** 部分中提供了与特定于绑定的错误有关的信息：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 存储](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [事件中心](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT 中心](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [通知中心](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [队列存储](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [服务总线](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [表存储](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
