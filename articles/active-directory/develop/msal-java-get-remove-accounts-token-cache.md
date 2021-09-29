---
title: 从令牌缓存中获取和删除帐户 (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 Java 的 Microsoft 身份验证库查看和删除令牌缓存中的帐户。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 8ea51bfd356e7479f421a6db804ec94bf656284a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612522"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>使用 MSAL for Java 在令牌缓存中获取和删除帐户

MSAL for Java 默认提供内存中令牌缓存。 内存中令牌缓存的持续时间与应用程序实例的持续时间相同。

## <a name="see-which-accounts-are-in-the-cache"></a>查看哪些帐户在缓存中

如以下示例所示，可以通过调用 `PublicClientApplication.getAccounts()` 查看哪些帐户在缓存中：

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>从缓存中删除帐户

如以下示例所示，若要从缓存中删除帐户，请找到需要删除的帐户，然后调用 `PublicClientApplication.removeAccount()`：

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>了解详细信息

如果使用的是适用于 Java 的 MSAL，请了解[适用于 Java 的 MSAL 中的自定义令牌缓存序列化](msal-java-token-cache-serialization.md)。
