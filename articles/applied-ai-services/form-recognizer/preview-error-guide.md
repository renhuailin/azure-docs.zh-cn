---
title: 参考：表单识别器错误 (2021-09-30-preview)
titleSuffix: Azure Applied AI Services
description: 了解表单识别器中的错误的表示形式，查找服务返回的可能错误列表。
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/30/2021
ms.author: paulhsu
ms.custom: cog-serv-seo-aug-2020
keywords: 文档处理
ms.openlocfilehash: a67c98189198cf8df0f8f7e6fa88c9a552812bfa
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730543"
---
# <a name="form-recognizer-error-guide-v30-preview"></a>表单识别器错误指南 v3.0（预览版）

表单识别器使用统一的设计来表示 REST API 中遇到的所有错误。  每当 API 操作返回 4xx 或 5xx 状态代码时，响应 JSON 正文中就会返回有关错误的附加信息，如下所示：

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

对于可能遇到了多个错误的长时间运行的操作，顶级错误代码将设置为最严重的错误，并且 error.details 属性下会列出各个错误。  在这种情况下，每个错误的 target 属性指定了错误的触发器。

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

顶级 error.code 属性可以是以下错误代码消息之一：

| 错误代码           | Message                                                | Http 状态 |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | 请求无效。                                       | 400         |
| InvalidArgument      | 无效的参数。                                      | 400         |
| 禁止            | 由于策略或其他配置的原因，已禁止访问。 | 403         |
| NotFound             | 找不到资源。                                    | 404         |
| MethodNotAllowed     | 不允许请求的 HTTP 方法。              | 405         |
| Conflict             | 由于发生冲突，无法完成请求。  | 409         |
| UnsupportedMediaType | 请求内容类型不受支持。                 | 415         |
| InternalServerError  | 发生了意外错误。                          | 500         |
| ServiceUnavailable   | 发生了暂时性错误。 重试。      | 503         |

在可能的情况下，内部错误属性中会指定更多详细信息。

| 顶级错误代码 | 内部错误代码 | Message |
| -------------- | ---------------- | ------- |
| Conflict | ModelExists | 使用所提供名称的模型已存在。 |
| 禁止 | AuthorizationFailed | 授权失败: {details} |
| 禁止 | InvalidDataProtectionKey | 数据保护密钥无效: {details} |
| 禁止 | OutboundAccessForbidden | 请求包含当前访问控制策略不允许的域名。 |
| InternalServerError | Unknown | 未知错误。 |
| InvalidArgument | InvalidContentSourceFormat | 内容源无效: {details} |
| InvalidArgument | InvalidParameter | 参数 {parameterName} 无效: {details} |
| InvalidArgument | InvalidParameterLength | 参数 {parameterName} 的长度不得超过 {maxChars} 个字符。 |
| InvalidArgument | InvalidSasToken | 共享访问签名(SAS)无效: {details} |
| InvalidArgument | ParameterMissing | 参数 {parameterName} 是必需的。 |
| InvalidRequest | ContentSourceNotAccessible | 内容不可访问: {details} |
| InvalidRequest | ContentSourceTimeout | 从客户端接收文件时发生超时。 |
| InvalidRequest | DocumentModelLimit | 帐户无法创建 {maximumModels} 个以上的模型。 |
| InvalidRequest | DocumentModelLimitComposed | 帐户无法创建包含 {details} 个以上的组件模型的模型。 |
| InvalidRequest | InvalidContent | 文件已损坏或格式不受支持。 请参阅文档了解支持的格式列表。 |
| InvalidRequest | InvalidContentDimensions | 输入图像的尺寸超出范围。 请参阅文档了解支持的图像尺寸。 |
| InvalidRequest | InvalidContentLength | 输入图像太大。 请参阅文档了解最大文件大小。 |
| InvalidRequest | InvalidFieldsDefinition | 字段无效: {details} |
| InvalidRequest | InvalidTrainingContentLength | 训练内容包含 {bytes} 个字节。 训练限制为 {maxBytes} 个字节。 |
| InvalidRequest | InvalidTrainingContentPageCount | 训练内容包含 {pages} 个页面。 训练限制为 {pages} 个页面。 |
| InvalidRequest | ModelAnalyzeError | 无法使用自定义模型进行分析: {details} |
| InvalidRequest | ModelBuildError | 无法生成模型: {details} |
| InvalidRequest | ModelComposeError | 无法组合模型: {details} |
| InvalidRequest | ModelNotReady | 模型尚未准备就绪，无法执行请求的操作。 请等待训练完成，或检查是否出现操作错误。 |
| InvalidRequest | ModelReadOnly | 请求的模型是只读的。 |
| InvalidRequest | NotSupportedApiVersion | 请求的操作需要 {minimumApiVersion} 或更高版本。 |
| InvalidRequest | OperationNotCancellable | 不再可以取消该操作。 |
| InvalidRequest | TrainingContentMissing | 缺少训练数据: {details} |
| InvalidRequest | UnsupportedContent | 内容不受支持: {details} |
| NotFound | ModelNotFound | 找不到请求的模型。 该模型可能已被删除或仍在生成中。 |
| NotFound | OperationNotFound | 找不到请求的操作。 标识符可能无效，或者该操作可能已过期。 |