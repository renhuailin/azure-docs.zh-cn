---
title: 在 Azure Cloud Shell 中获取用户令牌
description: 如何在 Azure Cloud Shell 中获取经过身份验证的用户的令牌
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/29/2021
ms.openlocfilehash: 117fa3672c78de29cd88797add83fa6e3bf2bf79
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362735"
---
# <a name="acquire-a-token-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中获取令牌

Azure Cloud Shell 提供一个终结点，该终结点会自动对登录到 Azure 门户的用户进行身份验证。 使用此终结点获取访问令牌后可以与 Azure 服务交互。

## <a name="authenticating-in-the-cloud-shell"></a>在 Azure Cloud Shell 中进行身份验证
Azure Cloud Shell 具有自己的终结点，该终结点与浏览器交互以自动完成登录。 当此终结点收到请求时，它会将请求发送回浏览器，浏览器会将请求转发到父级门户框架。 门户窗口向 Azure Active Directory 发出请求，并返回生成的令牌。

如果要使用不同的凭据进行身份验证，可以使用 `az login` 或 `Connect-AzAccount`

## <a name="acquire-and-use-access-token-in-cloud-shell"></a>在 Cloud Shell 中获取和使用访问令牌

### <a name="acquire-token"></a>获取令牌

执行以下命令，将用户访问令牌设置为环境变量 `access_token`。
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The access token is $access_token
```

### <a name="use-token"></a>使用令牌

执行以下命令，使用在上一步获取的令牌取得帐户中所有虚拟机的列表。

```
curl https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Compute/virtualMachines?api-version=2021-07-01 -H "Authorization: Bearer $access_token" -H "x-ms-version: 2019-02-02"
```

## <a name="handling-token-expiration"></a>处理令牌过期

本地身份验证终结点会缓存令牌。 你可以随意调用它，只要缓存中未存储令牌，或者令牌已过期，才会发生 Azure Active Directory 身份验证调用。

## <a name="limitations"></a>限制
- 资源允许列表对 Cloud Shell 令牌可以提供给哪些资源做出了限制。 如果运行命令后收到类似于 `"error":{"code":"AudienceNotSupported","message":"Audience https://newservice.azure.com/ is not a supported MSI token audience...."}` 的消息，说明你遇到了此限制。 你可以在 [GitHub](https://github.com/Azure/CloudShell/issues) 上提交问题，请求将此服务添加到允许列表。
- 如果使用 `az login` 命令显式登录，则公司可能实施的任何条件访问规则都将基于 Cloud Shell 容器（而不是运行浏览器计算机）进行评估。 Cloud Shell 容器不视为这些策略的托管设备，因此权限可能会受到策略限制。
- Azure 托管标识在 Azure Cloud Shell 中不可用。 [详细了解 Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
