---
title: 为 Azure Red Hat OpenShift (ARO) 群集轮换服务主体凭据
description: 了解如何轮换 Azure Red Hat OpenShift (ARO) 中的服务主体凭据。
author: swiencki
ms.author: b-swiencki
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 05/31/2021
ms.openlocfilehash: 286126bdd3cfcc5139549d1137431c8016c99472
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090864"
---
# <a name="rotate-service-principal-credentials-for-your-azure-red-hat-openshift-aro-cluster"></a>轮换 Azure Red Hat OpenShift (ARO) 群集的服务主体凭据
本文提供轮换 Azure Red Hat OpenShift 群集 ([ARO](https://github.com/Azure/ARO-RP)) 中的服务主体凭据所需的详细信息。

## <a name="before-you-begin"></a>在开始之前
本文假设已有一个应用了最新更新的 ARO 群集。

若要轮换 ARO 群集中的服务主体凭据，所需的最低 Azure CLI 版本为 2.24.0。

若要检查运行的 Azure CLI 版本，请运行以下命令：
```azurecli-interactive
# Azure CLI version
az --version
```
若要安装或升级 Azure CLI，请按照[安装 Azure CLI](/cli/azure/install-azure-cli) 进行操作。

以下说明使用 bash 语法。

## <a name="service-principal-credential-rotation"></a>服务主体凭据轮换
>[!IMPORTANT]
>  服务主体凭据轮换最长可能需要 2 个小时，具体取决于群集状态。

服务主体凭据轮换采用两种方法：
 - [自动服务主体凭据轮换](#Automated-Service-Principal-Credential-Rotation)
 - [用户提供的客户端 ID 和客户端机密服务主体凭据轮换](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)

### <a name="automated-service-principal-credential-rotation"></a>自动服务主体凭据轮换 <a id="Automated-Service-Principal-Credential-Rotation"></a>
>[!IMPORTANT]
>  要实现自动服务主体凭据轮换，ARO 群集必须是使用 Azure CLI 2.24.0 或更高版本创建的。

自动服务主体凭据轮换将检查服务主体是否存在，然后轮换或新建服务主体。

使用以下命令自动轮换服务主体凭据：

```azurecli-interactive
# Automatically rotate service principal credentials
az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup
```

### <a name="user-provided-client-id-and-client-secret-service-principal-credential-rotation"></a>用户提供的客户端 ID 和客户端机密服务主体凭据轮换 <a id="User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation"></a>


根据以下说明使用用户提供的客户端 ID 和客户端机密手动轮换服务主体凭据：

检索服务主体 clientId (`--client-id`) 并将其设置为 `SP_ID` 环境变量。
```azurecli-interactive
# Retrieve the service principal clientId
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
```
使用上述 `SP_ID` 变量为服务主体生成新的安全机密 (`--client-secret`)。 将新的安全机密存储为 `SP_SECRET` 环境变量。
```azurecli-interactive
# Generate a new secure secret for the service principal
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```
使用上述环境变量轮换服务主体凭据。
```azurecli-interactive
# Rotate service principal credentials
az aro update --client-id $SP_ID --client-secret $SP_SECRET \
    --name MyManagedCluster --resource-group MyResourceGroup
```

## <a name="troubleshoot"></a>疑难解答
### <a name="service-principal-expiration-date"></a>服务主体到期日期
为服务主体凭据设置的到期日期为一年，应在给定的时限内进行轮换。

如果到期日期已过，可能会出现以下错误：
```bash
Failed to refresh the Token for request to MyResourceGroup StatusCode=401
Original Error: Request failed. Status Code = '401'.
[with]
Response body: {"error":"invalid_client","error_description": The provided client secret keys are expired.
[or]
Response body: {"error":"invalid_client","error_description": Invalid client secret is provided.
```
若要检查服务主体凭据的到期日期，请运行以下命令：
```azurecli-interactive
# Service principal expiry in ISO 8601 UTC format
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```
如果服务主体凭据已到期，请使用上述两种凭据轮换方法之一进行更新。

### <a name="cluster-aad-application-contains-a-client-secret-with-an-empty-description"></a>群集 AAD 应用程序包含没有任何说明的客户端机密
使用[自动服务主体凭据轮换](#Automated-Service-Principal-Credential-Rotation)方法时发生以下错误：
```bash
$ az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup

Cluster AAD application contains a client secret with an empty description.
Please either manually remove the existing client secret and run `az aro update --refresh-credentials`,
or manually create a new client secret and run `az aro update --client-secret <ClientSecret>`.
```
群集不是使用 Azure CLI 2.24.0 或更高版本创建的。 请改用[用户提供的客户端 ID 和客户端机密服务主体凭据轮换](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)方法。

### <a name="azure-cli-aro-update-help"></a>Azure CLI ARO update help
若要查看更多详细信息，请运行 Azure CLI ARO update help 命令：
```azurecli-interactive
# Azure CLI ARO update help
az aro update -h
```
