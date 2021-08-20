---
title: 用于修改 Azure AD B2C 用户流的 API 示例 | Microsoft Docs
description: 使用 API 连接器修改用户流的代码示例
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/16/2021
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d27bac61a7f39b50d692e579edb29da945234a27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114479556"
---
# <a name="api-connector-rest-api-samples"></a>API 连接器 REST API 示例

下表提供了一些代码示例的链接，这些示例通过 [API 连接器](api-connectors-overview.md)在用户流中使用 Web API。 这些示例主要设计为与内置用户流结合使用。

## <a name="azure-function-quickstarts"></a>Azure Function 快速入门
| 示例                                                                                                                          | 说明                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 此 .NET Core Azure Function 示例演示如何将登录限制为特定电子邮件域并验证用户提供的信息。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 此 Node.js Azure Function 示例演示如何将登录限制为特定电子邮件域并验证用户提供的信息。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 此 Python Azure Function 示例演示如何将登录限制为特定电子邮件域并验证用户提供的信息。    |


## <a name="automated-fraud-protection-services--captcha"></a>自动防欺诈服务和 CAPTCHA
| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs 欺诈和滥用防护](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | 此示例演示如何使用 Arkose Labs 欺诈和滥用防护服务来保护用户登录。 |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | 此示例演示如何使用 reCAPTCHA 质询来保护用户登录，以防止自动滥用。 |


## <a name="identity-verification"></a>身份验证

| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 此示例演示如何使用 IDology 的服务在注册流中验证用户标识。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 此示例演示如何使用 Experian 的服务在注册流中验证用户标识。 |


## <a name="other"></a>其他

| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [邀请码](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | 此示例演示如何使用邀请码限制特定访问群体注册。|
| [API 连接器社区示例](https://github.com/azure-ad-b2c/api-connector-samples) | 此存储库包含由 API 连接器启用的社区维护的方案示例。|
