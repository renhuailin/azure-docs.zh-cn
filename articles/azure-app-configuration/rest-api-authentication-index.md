---
title: Azure 应用配置 REST API - 身份验证
description: 使用 Azure 应用配置 REST API 进行身份验证的参考页
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932687"
---
# <a name="authentication"></a>身份验证

所有 HTTP 请求都必须经过身份验证。 支持以下身份验证方案。

## <a name="hmac"></a>HMAC

[HMAC 身份验证](./rest-api-authentication-hmac.md)使用随机生成的机密为请求有效负载签名。 若要详细了解如何授权使用此身份验证方法的请求，可参阅 [HMAC 授权](./rest-api-authorization-hmac.md)部分。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 身份验证](../active-directory/authentication/overview-authentication.md)利用从 Azure Active Directory 获取的持有者令牌对请求进行身份验证。 若要详细了解如何授权使用此身份验证方法的请求，可参阅 [Azure AD 授权](./rest-api-authorization-azure-ad.md)部分。