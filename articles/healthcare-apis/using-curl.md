---
title: 使用 cURL 访问 Azure 医疗保健 API
description: 本文介绍如何使用 cURL 访问医疗保健 API
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: ddc7b291ba6ecd86e4915523345c40054794a993
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783066"
---
# <a name="access-the-healthcare-apis-preview-with-curl"></a>使用 cURL 访问 (api) 预览版 

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文将了解如何使用 cURL 访问 Azure 医疗保健 API。

## <a name="prerequisites"></a>先决条件

### <a name="powershell"></a>PowerShell

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装 [PowerShell](/powershell/module/powershellget/) 和 [Azure Az PowerShell](/powershell/azure/install-az-ps)。
* （可选）可以使用 Rest 客户端扩展Visual Studio Code运行脚本。 有关详细信息，请参阅创建 [Rest 客户端文档 的链接](using-rest-client.md)。
* 下载并安装 [cURL](https://curl.se/download.html)。

### <a name="cli"></a>CLI

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 如果要在本地运行代码，请安装[Azure CLI。](/cli/azure/install-azure-cli) 
* （可选）安装 Bash shell，例如 Git Bash，该 shell 包含在 Git [for Windows。](https://gitforwindows.org/)
* （可选）使用 rest 客户端扩展Visual Studio Code运行脚本。 有关详细信息，请参阅创建 [Rest 客户端文档 的链接](using-rest-client.md)。
* 下载并安装 [cURL](https://curl.se/download.html)。

## <a name="obtain-azure-access-token"></a>获取 Azure 访问令牌

在访问医疗保健 API 之前，必须授予用户或客户端应用适当的权限。 若要详细了解如何授予权限，请参阅医疗保健 API [授权](authentication-authorization.md)。

有几种不同的方法可以获取医疗保健 API 的 Azure 访问令牌。 

> [!NOTE]
> 确保已登录到 Azure，并且位于部署了医疗保健 API 实例的 Azure 订阅和租户中。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
### check Azure environment and PowerShell versions
Get-AzContext 
Set-AzContext -Subscription <subscriptionid>
$PSVersionTable.PSVersion
Get-InstalledModule -Name Az -AllVersions
curl --version

### get access token for the FHIR service
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
$token=(Get-AzAccessToken -ResourceUrl $fhirservice).Token

### Get access token for the DICOM service
$dicomtokenurl= "https://dicom.healthcareapis.azure.com/"
$token=$( Get-AzAccessToken -ResourceUrl $dicomtokenurl).Token
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
### check Azure environment and CLI versions
az account show --output table
az account set --subscription <subscriptionid>
cli –version
curl --version

### get access token for the FHIR service
$fhirservice=https://<fhirservice>.fhir.azurehealthcareapis.com
token=$(az account get-access-token --resource=$fhirservice --query accessToken --output tsv)

### get access token for the DICOM service
dicomtokenurl= https://dicom.healthcareapis.azure.com/
token=$(az account get-access-token --resource=$dicomtokenurl --query accessToken --output tsv)
```

---

## <a name="access-data-in-the-fhir-service"></a>访问 FHIR 服务中的数据

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

---

`curl -X GET --header "Authorization: Bearer $token" $fhirservice/Patient`

[![使用 curl 脚本访问 FHIR 服务中的数据。 ](media/curl-fhir.png) ](media/curl-fhir.png#lightbox)

## <a name="access-data-in-the-dicom-service"></a>访问 DICOM 服务中的数据

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
---

`curl -X GET --header "Authorization: Bearer $token" $dicomservice/changefeed?includemetadata=false`

[![使用 curl 脚本访问 DICOM 服务中的数据。 ](media/curl-dicom.png) ](media/curl-dicom.png#lightbox)

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 cURL 访问医疗保健 API 数据。

若要了解如何使用 REST 客户端扩展访问医疗保健 API 数据，请参阅Visual Studio Code 

>[!div class="nextstepaction"]
>[使用 REST 客户端访问医疗保健 API](using-rest-client.md)