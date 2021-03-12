---
title: 使用 Azure CLI 获取访问令牌-Azure API for FHIR
description: 本文介绍如何使用 Azure CLI 获取用于 FHIR 的 Azure API 的访问令牌。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: b7bdb7f4a22d080f382fea984e710980428067ff
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017902"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>使用 Azure CLI 获取用于 FHIR 的 Azure API 的访问令牌

本文介绍如何使用 Azure CLI 获取用于 FHIR 的 Azure API 的访问令牌。 设置用于 [FHIR 的 AZURE API](fhir-paas-portal-quickstart.md)时，需要配置一组有权访问该服务的用户或服务主体。 如果你的用户对象 ID 在允许的对象 Id 列表中，则可以使用通过 Azure CLI 获取的令牌来访问该服务。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>获取令牌

适用于 FHIR 的 Azure API 使用 `resource`  或 `Audience` ，其 URI 等于 FHIR 服务器的 uri `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 。 可以使用以下命令获取令牌，并将其存储在名为) 的变量 (中 `$token` ：

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>与用于 FHIR 的 Azure API 配合使用

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>后续步骤

在本文中，已了解如何使用 Azure CLI 获取用于 FHIR 的 Azure API 的访问令牌。 若要了解如何使用 Postman 访问 FHIR API，请转到 Postman 教程。

>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR API](access-fhir-postman-tutorial.md)
