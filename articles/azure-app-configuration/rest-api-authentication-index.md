---
title: Azure 应用配置 REST API-身份验证
description: 使用 Azure 应用配置的身份验证的参考页面 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183458"
---
# <a name="authentication"></a>身份验证

所有 HTTP 请求都必须经过身份验证。 支持以下身份验证方案。

## <a name="hmac"></a>HMAC

[HMAC 身份验证](./rest-api-authentication-hmac.md) 使用随机生成的机密来签署请求有效负载。 有关如何授权使用此身份验证方法的请求的详细信息，请参阅 [HMAC 授权](./rest-api-authorization-hmac.md) 部分。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 身份验证](../active-directory/authentication/overview-authentication.md) 利用从 Azure Active Directory 获取的持有者令牌对请求进行身份验证。 有关如何授权使用此身份验证方法的请求的详细信息，请参阅 [Azure AD 授权](./rest-api-authorization-azure-ad.md) 部分。