---
title: 使用 Azure CLI 或 Azure PowerShell FHIR 服务获取访问令牌
description: 本文介绍如何使用 Azure CLI 或 Azure PowerShell 获取 FHIR 服务的访问令牌。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 672089ac061430121916efff67280c08c1c6943f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619401"
---
# <a name="get-access-token-for-fhir-service-using-azure-cli-or-azure-powershell"></a>使用 Azure CLI 或 Azure PowerShell 获取 FHIR 服务的访问令牌

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在本文中，你将了解如何使用 Azure CLI 在 Azure 医疗保健 Api (特此称为 FHIR service) 中获取 FHIR 服务的访问令牌。 [设置 FHIR 服务](fhir-portal-quickstart.md)时，将配置一组有权访问该服务的用户或服务主体。 如果用户对象 ID 在允许的对象 ID 列表中，则可以使用通过 Azure CLI 获取的令牌来访问该服务。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

在本文中，你将了解如何使用 Azure PowerShell 在 Azure 医疗保健 Api (特此称为 FHIR service) 中获取 FHIR 服务的访问令牌。 [设置 FHIR 服务](fhir-portal-quickstart.md)时，将配置一组有权访问该服务的用户或服务主体。 如果你的用户对象 ID 在允许的对象 Id 列表中，则可以使用通过 Azure PowerShell 获取的令牌来访问该服务。

[!INCLUDE [azure-powershell-requirements.md](../../../includes/azure-powershell-requirements.md)]

---
## <a name="obtain-a-token"></a>获取令牌

FHIR 服务使用 `resource`  或 `Audience` ，其 URI 等于 FHIR 服务器的 uri `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 。 可以使用以下命令获取令牌并将其存储在一个变量（名为 `$token`）中：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-with-fhir-service"></a>使用 with FHIR 服务

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

---

>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR API](../use-postman.md)
