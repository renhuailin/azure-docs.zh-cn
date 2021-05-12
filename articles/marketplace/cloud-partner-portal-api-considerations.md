---
title: API 注意事项-Azure 市场
description: 使用市场 API 时的版本控制、错误处理和授权问题。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 1e7319b19c5e7d8478cdad3d7359b98e023eb443
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109736009"
---
# <a name="api-considerations"></a>API 注意事项

## <a name="api-versioning"></a>API 版本控制

> [!NOTE]
> 云合作伙伴门户 API 已与合作伙伴中心集成，并仍继续在其中工作。 本次转换带来了少量更改。 查看[云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md)中列出的更改，确保转换到合作伙伴中心后代码继续正常工作。 CPP API 应仅用于在转换到合作伙伴中心之前已经集成的现有产品；新产品应使用合作伙伴中心提交 API。

可能有多个版本的 API 同时可用。 客户端必须通过提供 `api-version` 参数作为查询字符串的一部分来指明它们希望调用哪个版本。

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

对包含未知或无效 API 版本的请求的响应是 HTTP 代码 400。 此错误会在响应正文中返回已知 API 版本的集合。

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

## <a name="errors"></a>错误

API 使用相应的 HTTP 状态代码响应错误，并且可选地将响应中的其他信息序列化为 JSON。
当你收到错误（尤其是 400 类错误）时，请不要在修复底层原因之前重试请求。 例如，在上面的示例响应中，在重新发送请求之前修复 API 版本参数。

## <a name="authorization-header"></a>授权标头

对于本参考中的所有 API，必须传递授权标头以及从 Azure Active Directory (Azure AD) 获取的持有者令牌。 此标头是接收有效响应所必需的；如果不存在，则返回 `401 Unauthorized` 错误。 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
