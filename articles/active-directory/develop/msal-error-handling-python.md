---
title: 处理 MSAL for Python 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL for Python 应用程序中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761087"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>处理 MSAL for Python 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>MSAL for Python 中的错误处理

在适用于 Python 的 MSAL 中，大多数错误都是作为 API 调用的返回值来传递的。 错误表示为一个字典，其中包含来自 Microsoft 标识平台的 JSON 响应。

* 成功的响应包含 `"access_token"` 项。 响应的格式由 OAuth2 协议定义。 有关详细信息，请参阅 [5.1 成功的响应](https://tools.ietf.org/html/rfc6749#section-5.1)
* 错误的响应包含 `"error"`，通常为 `"error_description"`。 响应的格式由 OAuth2 协议定义。 有关详细信息，请参阅 [5.2 错误的响应](https://tools.ietf.org/html/rfc6749#section-5.2)

返回错误时，`"error_description"` 项包含用户可读的消息；它通常包含 Microsoft 标识平台错误代码。 有关各个错误代码的详细信息，请参阅[身份验证和授权错误代码](./reference-aadsts-error-codes.md)。

在适用于 Python 的 MSAL 中，很少引发异常，因为大多数错误都是通过返回错误值来处理的。 仅当你尝试使用该库的方式存在问题时（例如在 API 参数格式不正确时），才会引发 `ValueError` 异常。

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑[在 MSAL for Python 中启用日志记录](msal-logging-python.md)，以帮助你诊断和调试问题。
