---
title: Azure Maps 长时间运行的操作 API V2
description: 了解 Azure Maps 中长时间运行的异步 V2 后台处理
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: da61d170d0a4829ec7dede05d33fc3f70644cc61
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725596"
---
# <a name="creator-long-running-operation-api-v2"></a>创建者长时间运行的操作 API V2

Azure Maps 中的某些 API 使用[异步请求-答复模式](/azure/architecture/patterns/async-request-reply)。 此模式允许 Azure Maps 提供高度可用且响应迅速的服务。 本文介绍了 Azure Map 长时间运行的异步后台处理的特定实现。

## <a name="submit-a-request"></a>提交请求

客户端应用程序通过对 HTTP API 的同步调用启动长时间运行的操作。 通常，此调用采用 HTTP POST 请求的形式。 成功创建异步工作负荷后，API 将返回 HTTP `202` 状态代码，指示已接受请求。 此响应包含一个指向终结点的 `Location` 标头，客户端可以轮询该终结点以检查长时间运行的操作的状态。

### <a name="example-of-a-success-response"></a>成功响应的示例

```HTTP
Status: 202 Accepted
Operation-Location: https://atlas.microsoft.com/service/operations/{operationId} 

```

如果调用未通过验证，则 API 将返回错误请求的 HTTP `400` 响应。 响应正文将为客户端提供有关请求无效的原因的详细信息。

### <a name="monitor-the-operation-status"></a>监视操作状态

可以轮询在接受的响应标头中提供的位置终结点，以检查长时间运行的操作的状态。 操作状态请求中的响应正文将始终包含 `status` 和 `created` 属性。 `status` 属性显示长时间运行的操作的当前状态。 可能的值包括 `"NotStarted"`、`"Running"`、`"Succeeded"` 和 `"Failed"`。 `created` 属性显示发出初始请求以启动长时间运行的操作的时间。 当状态为 `"NotStarted"` 或 `"Running"` 时，还将随响应提供 `Retry-After` 标头。 `Retry-After` 标头（以秒为度量单位）可用于确定何时应对操作状态 API 进行下一次轮询调用。

### <a name="example-of-running-a-status-response"></a>运行状态响应的示例

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handle-operation-completion"></a>对操作完成情况进行处理

完成长时间运行的操作后，响应的状态将是 `"Succeeded"` 或 `"Failed"`。 所有响应都会返回“HTTP 200 正常”代码。 通过长时间运行的操作创建新资源后，响应还会包含一个指向有关资源的元数据的 `Resource-Location` 标头。 失败时，响应的正文中会包含 `error` 属性。 错误数据符合 OData 错误规范。

### <a name="example-of-success-response"></a>成功响应的示例

```HTTP
Status: 200 OK
Resource-Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "2021-05-06T07:55:19.5256829+00:00",
    "status": "Succeeded"
}
```

### <a name="example-of-failure-response"></a>失败响应的示例

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```