---
title: 使用 Azure CLI 创建 Azure 内容分发网络 (CDN) 配置文件和终结点
description: Azure CLI 示例脚本用于创建 Azure CDN 配置文件、终结点、源组、源和自定义域。
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723441"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure CDN 配置文件和终结点

作为 Azure 门户的替代项，您可以使用这些示例 Azure CLI 脚本来管理以下 CDN 操作：

- 创建 CDN 配置文件。
- 创建 CDN 终结点。
- 创建 CDN 源组，并将其设为默认组。
- 创建 CDN 源。
- 创建自定义域并启用 HTTPS。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>示例脚本

如果还没有用于 CDN 配置文件的资源组，请使用命令 `az group create` 创建该组：

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

以下 Azure CLI 脚本创建 CDN 配置文件和 CDN 终结点：

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

以下 Azure CLI 脚本将创建一个 CDN 源组，设置终结点的默认源组，并创建一个新的源：

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

以下 Azure CLI 脚本创建 CDN 自定义域并启用 HTTPS。 在将自定义域与 Azure CDN 终结点结合使用之前，必须先在 Azure DNS 或 DNS 提供商的配合下创建一个规范名称 (CNAME) 记录，使之指向 CDN 终结点。 有关详细信息，请参阅[创建 CNAME DNS 记录](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)。

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>清理资源

完成运行示例脚本后，使用以下命令删除资源组以及与其关联的所有资源。

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>本文中使用的 Azure CLI 命令

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
