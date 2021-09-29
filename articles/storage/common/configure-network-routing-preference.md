---
title: 配置网络路由首选项
titleSuffix: Azure Storage
description: 为 Azure 存储帐户配置网络路由首选项，以指定网络流量如何通过 Internet 从客户端路由到你的帐户。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c871a1ec4feec89cc3250f1fbfefefa69ed927bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589314"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>为 Azure 存储配置网络路由首选项

本文介绍如何为存储帐户配置网络路由首选项和特定于路由的终结点。

网络路由首选项可以指定网络流量如何通过 Internet 从客户端路由到你的帐户。 特定于路由的终结点是 Azure 存储为存储帐户创建的新终结点。 这些终结点将流量路由到所需路径，而无需更改默认路由首选项。 若要了解详细信息，请参阅[Azure 存储的网络路由首选项](network-routing-preference.md)。

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>为默认公共终结点配置路由首选项

存储帐户的公共终结点的路由首选项在默认情况下设置为 Microsoft 全局网络。 可以在 Microsoft 全球网络和 Internet 路由之间进行选择，作为存储帐户公共终结点的默认路由首选项。 若要详细了解这两种类型路由之间的差异，请参阅 [Azure 存储的网络路由首选项](network-routing-preference.md)。

### <a name="portal"></a>[门户](#tab/azure-portal)

将路由首选项更改为 Internet 路由：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户中导航到存储帐户。

3. 在“设置”下选择“网络”。 

    > [!div class="mx-imgBorder"]
    > ![网络菜单选项](./media/configure-network-routing-preference/networking-option.png)

4. 在“防火墙和虚拟网络”选项卡的“网络路由”下，将“路由首选项”设置更改为“Internet 路由”。

5. 单击“保存”  。

    > [!div class="mx-imgBorder"]
    > ![internet 路由选项](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

   ```powershell
   Connect-AzAccount
   ```

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

3. 若要将路由首选项更改为 Internet 路由，请使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 命令，并将 `--routing-choice` 参数设置为 `InternetRouting`。

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   将 `<resource-group-name>` 占位符值替换为包含存储帐户的资源组的名称。

   将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 登录到 Azure 订阅。

   - 若要启动 Azure Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。

   - 若要登录到本地安装的 CLI，请运行 [az login](/cli/azure/reference-index#az_login) 命令：

     ```azurecli
     az login
     ```

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

3. 若要将路由首选项更改为 Internet 路由，请使用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 命令，并将参数 `--routing-choice` 设置为 `InternetRouting`。

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

---

## <a name="configure-a-route-specific-endpoint"></a>配置特定于路由的终结点

还可以配置特定于路由的终结点。 例如，可以将默认终结点的路由首选项设置为 Internet 路由，然后发布特定于路由的终结点，以便将 internet 的客户端与存储帐户之间的流量通过 Microsoft 全球网络来路由。

此首选项只影响特定于路由的终结点。 此首选项不影响默认路由首选项。

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在门户中导航到存储帐户。

2. 在“设置”下选择“网络”。 

3. 在“防火墙和虚拟网络”选项卡中的“发布特定于路由的终结点”下，选择特定于路由的终结点的路由首选项，然后单击“保存”。

    下图显示了所选的“Microsoft 网络路由”选项。

    > [!div class="mx-imgBorder"]
    > ![Microsoft 网络路由选项](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 若要配置特定于路由的终结点，请使用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 命令。

   - 若要创建使用 Microsoft 网络路由首选项的特定于路由的终结点，请将 `-PublishMicrosoftEndpoint` 参数设置为 `true`。

   - 若要创建使用 Internet 网络路由首选项的特定于路由的终结点，请将 `-PublishInternetEndpointTo` 参数设置为 `true`。

   以下实例创建了使用 Microsoft 网络路由首选项的特定于路由的终结点。

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   将 `<resource-group-name>` 占位符值替换为包含存储帐户的资源组的名称。

   将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 若要配置特定于路由的终结点，请使用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 命令。

   - 若要创建使用 Microsoft 网络路由首选项的特定于路由的终结点，请将 `--publish-microsoft-endpoints` 参数设置为 `true`。

   - 若要创建使用 Internet 网络路由首选项的特定于路由的终结点，请将 `--publish-internet-endpoints` 参数设置为 `true`。

   以下实例创建了使用 Microsoft 网络路由首选项的特定于路由的终结点。

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>查找特定于路由的终结点的终结点名称

如果配置了特定于路由的终结点，则可以在存储帐户的属性中找到终结点。

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 在“设置”下面，选择“属性”。

    > [!div class="mx-imgBorder"]
    > ![属性菜单选项](./media/configure-network-routing-preference/properties.png)

2. 支持路由首选项的每项服务都能显示“Microsoft 网络路由”终结点。 此图显示了 blob 和文件服务的终结点。

    > [!div class="mx-imgBorder"]
    > ![用于特定于路由的终结点的 Microsoft 网络路由选项](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 若要将终结点打印到控制台，请使用`PrimaryEndpoints`存储帐户对象的属性。

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   将 `<resource-group-name>` 占位符值替换为包含存储帐户的资源组的名称。

   将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 若要将终结点打印到控制台，请使用存储帐户对象的 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 属性。

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   将 `<resource-group-name>` 占位符值替换为包含存储帐户的资源组的名称。

   将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

---

## <a name="see-also"></a>另请参阅

- [网络路由首选项](network-routing-preference.md)
- [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md)
- [有关 Blob 存储的安全性建议](../blobs/security-recommendations.md)
