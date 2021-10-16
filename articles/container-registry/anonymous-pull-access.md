---
title: 启用匿名拉取访问
description: 选择性地启用匿名拉取访问，使 Azure 容器注册表中的内容可公开提供
ms.topic: how-to
ms.date: 09/17/2021
ms.custom: ''
ms.openlocfilehash: 816e99cd5ba2ba83958a9ec7b9b0ad679e4a6550
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547279"
---
# <a name="make-your-container-registry-content-publicly-available"></a>使容器注册表内容可公开提供

为匿名（未经身份验证）拉取访问设置 Azure 容器注册表是一项可选功能，可访问 Internet 的任何用户可通过此功能从注册表中拉取任何内容。

匿名拉取访问是一项预览版功能，可在标准和高级[服务层级](container-registry-skus.md)中使用。 若要配置匿名拉取访问，请使用 Azure CLI（2.21.0 或更高版本）更新注册表。 若要安装或升级，请参阅[安装 Azure CLI](/cli/azure-install-cli)。

## <a name="about-anonymous-pull-access"></a>关于匿名拉取访问

默认情况下，只有[已经过身份验证](container-registry-authentication.md)的用户才拥有从 Azure 容器注册表拉取或推送内容的访问权限。 启用匿名（未经身份验证）拉取访问会导致所有注册表内容可供公开读取（拉取）。 在不需要用户身份验证的方案（例如分发公共容器映像）中，可以使用匿名拉取访问。

- 通过更新现有注册表的属性来启用匿名拉取访问。
- 启用匿名拉取访问后，可以随时禁用这种访问。
- 未经身份验证的客户端只能使用数据平面操作。
- 注册表可能会限制高频未经身份验证的请求。
- 如果你之前已向注册表进行身份验证，请确保在尝试执行匿名拉取操作之前清除凭据。

> [!WARNING]
> 匿名请求访问当前适用于注册表中的所有存储库。 如果你使用[存储库范围的令牌](container-registry-repository-scoped-permissions.md)管理存储库访问权限，所有用户都可以从启用了匿名拉取的注册表的存储库中拉取内容。 建议在启用匿名请求访问时删除令牌。

## <a name="configure-anonymous-pull-access"></a>配置匿名拉取访问 

### <a name="enable-anonymous-pull-access"></a>启用匿名拉取访问
使用 [az acr update](/cli/azure/acr#az_acr_update) 命令更新注册表并传递 `--anonymous-pull-enabled` 参数。 默认已在注册表中禁用匿名拉取。
          
```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

> [!IMPORTANT]
> 如果你之前已使用 Docker 凭据向注册表进行身份验证，请运行 `docker logout`，以确保在尝试执行匿名拉取操作之前清除现有凭据。 否则，你可能会看到类似于“已拒绝拉取访问”的错误消息。

### <a name="disable-anonymous-pull-access"></a>禁用匿名拉取访问
通过将 `--anonymous-pull-enabled` 设置为 `false` 来禁用匿名拉取访问。

```azurecli
az acr update --name myregistry --anonymous-pull-enabled false
```

## <a name="next-steps"></a>后续步骤

* 了解如何使用[存储库范围的令牌](container-registry-repository-scoped-permissions.md)。
* 了解用于向 Azure 容器注册表进行[身份验证](container-registry-authentication.md)的选项。
