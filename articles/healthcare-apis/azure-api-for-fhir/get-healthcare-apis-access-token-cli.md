---
title: 使用 Azure CLI 获取访问令牌 - Azure API for FHIR
description: 本文介绍了如何使用 Azure CLI 获取用于 Azure API for FHIR 的访问令牌。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: b955fd521224197a3505bd24f7ff0d02ec95fb5d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748955"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>使用 Azure CLI 获取 Azure API for FHIR 的访问令牌

在本文中，你将了解如何使用 Azure CLI 获取用于 Azure API for FHIR 的访问令牌。 [预配 Azure API for FHIR 时](fhir-paas-portal-quickstart.md)，你将配置一组有权访问该服务的用户或服务主体。 如果用户对象 ID 在允许的对象 ID 列表中，则可以使用通过 Azure CLI 获取的令牌来访问该服务。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>获取令牌

Azure API for FHIR 使用其 URI 与 FHIR 服务器的 URI (`https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com`) 相同的 `resource` 或 `Audience`。 可以使用以下命令获取令牌并将其存储在一个变量（名为 `$token`）中：

```azurecli-interactive
$token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>用于 Azure API for FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用 Azure CLI 获取用于 Azure API for FHIR 的访问令牌。 若要了解如何使用 Postman 访问 FHIR API，请转到 Postman 教程。

>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR 服务](./../use-postman.md)
