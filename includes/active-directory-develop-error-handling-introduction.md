---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760698"
---
本文概述不同类型的错误，并提供处理常见登录错误的相关建议。

## <a name="msal-error-handling-basics"></a>MSAL 错误处理基础知识

Microsoft 身份验证库 (MSAL) 中的异常旨在帮助应用开发者进行故障排除，而不会向最终用户显示。 异常消息未经本地化。

处理异常和错误时，可以使用异常类型本身和错误代码来区分不同的异常。  有关错误代码的列表，请参阅 [Azure AD 身份验证和授权错误代码](../articles/active-directory/develop/reference-aadsts-error-codes.md)。

在登录体验期间，可能会遇到有关许可、条件访问（MFA、设备管理、基于位置的限制）、令牌颁发和兑换以及用户属性的错误。

以下部分提供了有关应用错误处理的更多详细信息。