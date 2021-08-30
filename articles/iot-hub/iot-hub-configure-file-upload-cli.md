---
title: 使用 Azure CLI 配置到 IoT 中心的文件上传 | Microsoft Docs
description: 如何使用跨平台 Azure CLI 配置到 Azure IoT 中心的文件上传。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.openlocfilehash: ecaae482a0a577efd480610f25d0186502dbeb15
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735155"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>使用 Azure CLI 配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

本文介绍如何使用 Azure CLI 在 IoT 中心配置文件上传。 

若要使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)，必须先将 Azure 存储帐户和 Blob 容器与 IoT 中心关联。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。 除了存储帐户和 blob 容器之外，你还可以设置 SAS URI 生存时间以及 IoT 中心对 Azure 存储使用的身份验证类型。 你还可以为 IoT 中心可以提供给后端服务的可选文件上传通知配置设置。

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* Azure IoT 中心。 如果没有 IoT 中心，可以使用 [`az iot hub create` 命令](/cli/azure/iot/hub#az_iot_hub_create)创建一个，或[使用门户创建 IoT 中心](iot-hub-create-through-portal.md)。

* 一个 Azure 存储帐户。 如果没有 Azure 存储帐户，可以使用 Azure CLI 创建一个。 有关详细信息，请参阅[创建存储帐户](../storage/common/storage-account-create.md)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="sign-in-and-set-your-azure-account"></a>登录并设置 Azure 帐户

登录到 Azure 帐户，并选择订阅。 如果你使用的是 Azure Cloud Shell，则应该已登录；但是，如果你有多个订阅，则仍可能需要选择 Azure 订阅。

1. 在命令提示符中，运行 [login 命令](/cli/azure/get-started-with-azure-cli)：

    ```azurecli
    az login
    ```

    按照说明使用代码进行身份验证，并通过 Web 浏览器登录 Azure 帐户。

2. 如果有多个 Azure 订阅，登录 Azure 可获得与凭据关联的所有 Azure 帐户的访问权限。 使用[以下命令列出可供使用的 Azure 帐户](/cli/azure/account)：

    ```azurecli
    az account list
    ```

    使用以下命令，选择想要用于运行命令以创建 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>检索存储帐户详细信息

以下步骤假设已使用 **Resource Manager** 部署模型而不 **经典** 部署模型创建了存储帐户。

若要从设备配置文件上传，需要 Azure 存储帐户的连接字符串。 存储帐户必须与 IoT 中心位于同一订阅中。 还需要存储帐户中 Blob 容器的名称。 使用以下命令检索存储帐户密钥：

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```
连接字符串将类似于以下输出：

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={your storage account name};AccountKey={your storage account key}"
}
```

记下 `connectionString` 值。 在后续步骤中需要用到它。

可将现有的 Blob 容器用于文件上传，或新建一个容器：

* 若要列出存储帐户中的现有 Blob 容器，请使用以下命令：

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* 若要在存储帐户中创建 Blob 容器，请使用以下命令：

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="configure-your-iot-hub"></a>配置 IoT 中心

现在可以使用存储帐户详细信息配置 IoT 中心以[将文件上传到 IoT 中心](iot-hub-devguide-file-upload.md)。

配置需要以下值：

* **存储容器**：当前 Azure 订阅中要与 IoT 中心关联的 Azure 存储帐户中的 Blob 容器。 检索在上一部分中必要的存储帐户信息。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。

* 接收已上传文件的通知：启用或禁用文件上传通知。 

* **SAS TTL**：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时。

* **文件通知设置默认 TTL**：文件上传通知到期前的生存时间。 默认设置为一天。

* **文件通知最大传送数**：IoT 中心将尝试传送文件上传通知的次数。 默认设置为 10。

* **文件通知锁定持续时间**：文件通知队列的锁定持续时间。 默认设为 60 秒。

* **身份验证类型**：IoT 中心用于 Azure 存储的身份验证类型。 此设置决定 IoT 中心如何对 Azure 存储进行身份验证和授权。 默认为基于密钥的身份验证；但也可以使用系统分配的托管标识和用户分配的托管标识。 托管标识在 Azure AD 中以安全的方式为 Azure 服务提供自动托管标识。 若要了解如何在 IoT 中心和 Azure 存储帐户上配置托管标识，请参阅 [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)。 配置后，可以设置其中一个托管标识，用于对 Azure 存储进行身份验证。

    > [!NOTE]
    > 身份验证类型设置配置 IoT 中心如何对 Azure 存储帐户进行身份验证。 设备始终使用从 IoT 中心获取的 SAS URI 对 Azure 存储进行身份验证。 


以下命令显示如何在 IoT 中心配置文件上传设置。 为清楚起见，这些命令会单独显示，但通常情况下，你会发出单个命令，其中包含方案所需的所有参数。 包含命令行中的引号。 请勿包含大括号。 有关各个参数的详细信息，请参阅关于 [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update) 命令的 Azure CLI 文档。

以下命令配置存储帐户和 blob 容器。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-connectionstring "{your storage account connection string}" \
    --fileupload-storage-container-name "{your container name}" 
```

以下命令将 SAS URI 生存时间设为默认值（一小时）。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-sas-ttl 1 
```

以下命令启用文件通知并将文件通知属性设为其默认值。 （文件上传通知的生存时间设为一小时，锁定持续时间设为 60 秒。）

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-notifications true  \
    --fileupload-notification-max-delivery-count 10 \
    --fileupload-notification-ttl 1 \
    --fileupload-notification-lock-duration 60
```

以下命令配置基于密钥的身份验证：

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type keyBased
```

以下命令使用 IoT 中心的系统分配的托管标识配置身份验证。 要运行此命令，需要先为 IoT 中心启用系统分配的托管标识，并为其授予针对 Azure 存储帐户的正确 RBAC 角色。 若要了解如何操作，请参阅 [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity [system] 
```

以下命令检索在 IoT 中心配置的用户分配的托管标识，并使用其中一个标识来配置身份验证。 要使用用户分配的托管标识进行身份验证，必须先在 IoT 中心对其进行配置并为其授予针对 Azure 存储帐户的相应 RBAC 角色。 若要了解详细信息和步骤，请参阅 [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)。

若要在 IoT 中心查询用户分配的托管标识，请使用 [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) 命令。

```azurecli
az iot hub identity show --name {your iot hub name} --query userAssignedIdentities
```

该命令返回在 IoT 中心配置的一系列用户分配的托管标识。 以下输出显示包含一个用户分配的托管标识的集合。

```json
{
  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}": 
  {
    "clientId": "<client ID GUID>",
    "principalId": "<principal ID GUID>"
  }
}
```

以下命令将身份验证配置为使用上述用户分配的标识。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}"
```

可以使用以下命令查看 IoT 中心的设置：

```azurecli
az iot hub show --name {your iot hub name}
```

若要仅查看文件上传设置，请使用以下命令：

```azurecli
az iot hub show --name {your iot hub name}
    --query '[properties.storageEndpoints, properties.enableFileUploadNotifications, properties.messagingEndpoints.fileNotifications]'
```

大多数情况下，在 Azure CLI 命令中使用命名参数是最简单的；但也可以使用 `--set` 参数配置文件上传设置。 以下命令有助于了解如何操作。 

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

## <a name="next-steps"></a>后续步骤

* [从设备上传文件概述](iot-hub-devguide-file-upload.md)
* [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)
* [文件上传操作指南](./iot-hub-csharp-csharp-file-upload.md)
* Azure CLI [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update)、[az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) 和 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 命令
