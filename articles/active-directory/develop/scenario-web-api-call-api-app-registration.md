---
title: 注册调用 Web API 的 Web API - Microsoft 标识平台 | Azure
description: 了解如何构建调用下游 Web API 的 Web API（应用注册）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 790580160ec236d1923dc28b9990d2675c253b44
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442678"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>调用 Web API 的 Web API：应用注册

调用下游 Web API 的 Web API 与受保护的 Web API 具有相同的注册。 因此，需要按照[受保护的 Web API：应用注册](scenario-protected-web-api-app-registration.md)中的说明进行操作。

由于 Web 应用现在调用 Web API，因此它将成为一个机密客户端应用程序。 这就是为什么需要额外的注册信息的原因：应用需要与 Microsoft 标识平台共享机密（客户端凭据）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 权限

Web 应用代表收到持有者令牌的用户调用 API。 Web 应用需要请求委托的权限。 有关详细信息，请参阅[添加访问 Web API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。

## <a name="next-steps"></a>后续步骤

转到本方案中的下一篇文章 [应用代码配置](scenario-web-api-call-api-app-configuration.md)。
