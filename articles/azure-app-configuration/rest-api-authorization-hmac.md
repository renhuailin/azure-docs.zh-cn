---
title: Azure 应用配置 REST API - HMAC 授权
description: 通过 REST API 使用 HMAC 针对 Azure 应用配置进行授权
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932653"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC 授权 - REST API 参考

使用 HMAC 身份验证时，操作分为两种类别：读取或写入。 读写访问密钥授予调用所有操作的权限。 只读访问密钥授予仅调用读取操作的权限。 访问密钥是只读访问密钥还是读写访问密钥取决于其 `readOnly` 属性。 任何使用只读访问密钥发出写入请求的尝试都会导致请求未经授权。

## <a name="obtaining-access-keys"></a>获取访问密钥

描述访问密钥的规范和用于获取这些密钥的 API 在[此处](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)的 Azure 应用配置资源提供程序规范中有详细说明。 通过“ConfigurationStores_ListKeys”操作获取访问密钥。

## <a name="errors"></a>错误

```http
HTTP/1.1 403 Forbidden
```

**原因：** 用于验证请求的访问密钥没有提供执行请求的操作所需的权限。
**解决方案：** 获取一个访问密钥（提供执行请求的操作所需的权限），并使用该密钥验证请求。
