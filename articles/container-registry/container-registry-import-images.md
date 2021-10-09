---
title: 导入容器映像
description: 使用 Azure API 将容器映像导入到 Azure 容器注册表中，无需运行 Docker 命令。
ms.topic: article
ms.date: 09/13/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14d2008599c0740bd36108760e3d4e50054b5f4d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582262"
---
# <a name="import-container-images-to-a-container-registry"></a>向容器注册表导入容器映像

可以轻松将容器映像导入（复制）到 Azure 容器注册表中，无需使用 Docker 命令。 例如，将映像从开发注册表导入到生产注册表，或者从公共注册表复制基础映像。

Azure 容器注册表可灵活应对许多常见方案，以便从现有注册表复制映像和其他工件：

* 从公共注册表导入映像

* 从其他 Azure 容器映像（在同一个或不同的 Azure 订阅或租户中）导入映像或 OCI 工件（包括 Helm 3 图表）

* 从非 Azure 专用容器注册表导入

与使用 Docker CLI 命令相比，将映像导入到 Azure 容器注册表具有以下优点：

* 由于客户端环境不需要本地 Docker 安装，因此无需考虑受支持的 OS 类型即可导入任何容器映像。

* 导入多体系结构映像（例如正式的 Docker 映像）时，会复制清单列表中指定的所有体系结构和平台的映像。

* 访问目标注册表不必使用该注册表的公共终结点。

## <a name="limitations"></a>限制

* 对于导入的映像，清单的最大数目为 50。
* 对于从公共注册表导入的映像，最大层大小为 2 GiB。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要导入容器映像，本文要求在 Azure Cloud Shell 中或本地（建议使用 2.0.55 或更高版本）运行 Azure CLI。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要导入容器映像，本文要求在 Azure Cloud Shell 中或在本地运行 Azure PowerShell（建议使用 5.9.0 或更高版本）。 运行 `Get-InstalledModule -Name Az` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure Az PowerShell 模块][install-the-azure-az-powershell-module]。

---

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

> [!IMPORTANT]
> 自 2021 年 1 月起，已引入对两个 Azure 容器注册表的映像导入的更改：
> * 如果要向/从网络受限的 Azure 容器注册表导入内容，需要受限的注册表[允许受信任的服务进行访问](allow-access-trusted-services.md)，以绕过该网络。 默认情况下，此设置处于启用状态，允许导入。 如果刚创建的具有专用终结点或注册表防火墙规则的注册表中未启用该设置，导入将失败。
> * 在用作导入源或目标的现有网络受限 Azure 容器注册表中，可以选择且建议选择启用此网络安全功能。

## <a name="prerequisites"></a>先决条件

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果还没有 Azure 容器注册表，请创建注册表。 有关步骤，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

如果还没有 Azure 容器注册表，请创建注册表。 如需了解操作步骤，请参阅[快速入门：使用 Azure PowerShell 创建专用容器注册表](container-registry-get-started-powershell.md)。

---

若要将映像导入到 Azure 容器注册表，标识必须具有对目标注册表的写入权限（至少是参与者角色或允许 importImage 操作的自定义角色）。 请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md#custom-roles)。

## <a name="import-from-a-public-registry"></a>从公共注册表导入

### <a name="import-from-docker-hub"></a>从 Docker 中心导入

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

例如，使用 [az acr import][az-acr-import] 命令将多体系结构 `hello-world:latest` 映像从 Docker 中心导入到名为 myregistry 的注册表。 由于 `hello-world` 是来自 Docker 中心的官方映像，因此该映像位于默认的 `library` 存储库中。 `--source` 映像参数的值中包含存储库名称和（可选）标记。 （可以选择性根据映像的清单摘要而不是标签来标识映像，这确保映像为特定版本。）

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

可以通过运行 `az acr repository show-manifests` 命令来验证多个清单是否与此映像关联：

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

如果有 [Docker Hub 帐户](https://www.docker.com/pricing)，则建议从 Docker Hub 导入映像时使用凭据。 将 Docker Hub 用户名和密码，或者[个人访问令牌](https://docs.docker.com/docker-hub/access-tokens/)作为参数传递到 `az acr import`。 以下示例使用 Docker Hub 凭据从 Docker Hub 中的 `tensorflow` 存储库导入公共映像：

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

例如，使用 [Import-AzContainerRegistryImage][import-azcontainerregistryimage] 命令将多体系结构 `hello-world:latest` 映像从 Docker Hub 导入到名为“myregistry”的注册表。 由于 `hello-world` 是来自 Docker 中心的官方映像，因此该映像位于默认的 `library` 存储库中。 在 `-SourceImage` 参数的值中，包含存储库名称，以及标记（可选）。 （可以选择性根据映像的清单摘要而不是标签来标识映像，这确保映像为特定版本。）

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage library/hello-world:latest
```

可以通过运行 `Get-AzContainerRegistryManifest` cmdlet 来验证是否多个清单与此映像关联：

```azurepowershell
Get-AzContainerRegistryManifest -RepositoryName library/hello-world -RegistryName myregistry
```

如果有 [Docker Hub 帐户](https://www.docker.com/pricing)，则建议从 Docker Hub 导入映像时使用凭据。 将 Docker Hub 用户名和密码，或者[个人访问令牌](https://docs.docker.com/docker-hub/access-tokens/)作为参数传递到 `Import-AzContainerRegistryImage`。 以下示例使用 Docker Hub 凭据从 Docker Hub 中的 `tensorflow` 存储库导入公共映像：

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage tensorflow/tensorflow:latest-gpu -Username <Docker Hub user name> -Password <Docker Hub token>
```

---

### <a name="import-from-microsoft-container-registry"></a>从 Microsoft 容器注册表导入

例如，从 Microsoft Container Registry 中的 `windows` 存储库导入 `ltsc2019` Windows Server Core 映像。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mcr.microsoft.com -SourceImage windows/servercore:ltsc2019
```

---

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>从同一 AD 租户中的 Azure 容器注册表导入

可以使用集成的 Azure Active Directory 权限从同一 AD 租户中的 Azure 容器注册表导入映像。

* 你的标识必须具有 Azure Active Directory 权限，才能从源注册表（读者角色）读取数据并导入到目标注册表（参与者角色或允许 importImage 操作的[自定义角色](container-registry-roles.md#custom-roles)）。

* 注册表可以位于同一 Active Directory 租户的同一或不同 Azure 订阅中。

* 可能会禁用对源注册表的[公共访问](container-registry-access-selected-networks.md#disable-public-network-access)。 如果禁用了公共访问，请通过资源 ID 而不是注册表登录服务器名称指定源注册表。

* 如果源注册表和/或目标注册表具有专用终结点，或者注册表防火墙规则适用，请确保受限注册表[允许受信任的服务](allow-access-trusted-services.md)访问网络。

### <a name="import-from-a-registry-in-the-same-subscription"></a>从同一订阅的注册表中导入

例如，在同一 Azure 订阅中，将 `aci-helloworld:latest` 映像从源注册表 mysourceregistry 导入到 myregistry。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

以下示例将 `aci-helloworld:latest` 映像从源注册表 mysourceregistry 导入到 myregistry 中，在该源注册表中禁止访问注册表的公共终结点 。 使用 `--registry` 参数提供源注册表的资源 ID。 注意，`--source` 参数只指定源存储库和标记，而不指定注册表登录服务器名称。

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

下面的示例通过清单摘要（SHA-256 哈希代码，表示为 `sha256:...`）而非标记导入映像：

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mysourceregistry.azurecr.io -SourceImage aci-helloworld:latest
```

以下示例将 `aci-helloworld:latest` 映像从源注册表 mysourceregistry 导入到 myregistry 中，在该源注册表中禁止访问注册表的公共终结点 。 使用 `--registry` 参数提供源注册表的资源 ID。 注意，`--source` 参数只指定源存储库和标记，而不指定注册表登录服务器名称。

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryResourceId '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry' -SourceImage aci-helloworld:latest
```

下面的示例通过清单摘要（SHA-256 哈希代码，表示为 `sha256:...`）而非标记导入映像：

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mysourceregistry.azurecr.io -SourceImage aci-helloworld@sha256:123456abcdefg
```

---

### <a name="import-from-a-registry-in-a-different-subscription"></a>从不同订阅的注册表导入

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在下面的示例中，mysourceregistry 与 myregistry 处于同一 Active Directory 租户的不同订阅中。 使用 `--registry` 参数提供源注册表的资源 ID。 注意，`--source` 参数只指定源存储库和标记，而不指定注册表登录服务器名称。

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

在下面的示例中，mysourceregistry 与 myregistry 处于同一 Active Directory 租户的不同订阅中。 使用 `--registry` 参数提供源注册表的资源 ID。 注意，`--source` 参数只指定源存储库和标记，而不指定注册表登录服务器名称。

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryResourceId '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry' -SourceImage aci-helloworld:latest
```

---

### <a name="import-from-a-registry-using-service-principal-credentials"></a>使用服务主体凭据从注册表导入

若要从无法使用集成的 Active Directory 权限访问的注册表中导入，可以将服务主体凭据（如果可用）用于源注册表。 提供对源注册表具有 ACRPull 访问权限的 Active Directory [服务主体](container-registry-auth-service-principal.md)的 appID 和密码。 服务主体适用于需将映像导入到注册表的生成系统和其他无人参与系统。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Username <SP_App_ID> -Password <SP_Passwd>
```

---

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>从不同 AD 租户中的 Azure 容器注册表导入

若要从不同 Azure Active Directory 租户中的 Azure 容器注册表导入，请通过登录服务器名称指定源注册表，并提供允许以拉取方式访问注册表的凭据。

### <a name="cross-tenant-import-with-username-and-password"></a>使用用户名和密码进行跨租户导入
例如，使用[存储库范围内的令牌](container-registry-repository-scoped-permissions.md)和密码，或对源注册表具有 ACRPull 访问权限的 Active Directory [服务主体](container-registry-auth-service-principal.md)的 appID 和密码。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Username <SP_App_ID> -Password <SP_Passwd>
```

---

### <a name="cross-tenant-import-with-access-token"></a>使用访问令牌进行跨租户导入

若要使用源租户中具有注册表权限的标识访问源注册表，可以获取访问令牌：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# Login to Azure CLI with the identity, for example a user-assigned managed identity
az login --identity --username <identity_ID>

# Get access token returned by `az account get-access-token`
az account get-access-token
```

在目标租户中，将访问令牌作为密码传递给 `az acr import` 命令。 源注册表通过登录服务器名称进行指定。 请注意，此命令中不需要用户名：

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --password <access-token>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
# Login to Azure PowerShell with the identity, for example a user-assigned managed identity
Connect-AzAccount -Identity -AccountId <identity_ID>

# Get access token returned by `Get-AzAccessToken`
Get-AzAccessToken
```

在目标租户中，将访问令牌作为密码传递到 `Import-AzContainerRegistryImage` cmdlet。 源注册表通过登录服务器名称进行指定。 请注意，此命令中不需要用户名：

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Password <access-token>
```

---

## <a name="import-from-a-non-azure-private-container-registry"></a>从非 Azure 专用容器注册表导入

通过指定启用了对注册表进行拉取访问的凭据，从非 Azure 专用注册表导入映像。 例如，从专用 Docker 注册表拉取映像：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io/sourcerepo -SourceImage sourcerrepo:tag -Username <username> -Password <password>
```

---

## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何从公共注册表或其他专用注册表将容器映像导入 Azure 容器注册表。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 关于其他映像导入选项，请参阅 [az acr import][az-acr-import] 命令参考。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* 如需了解其他映像导入选项，请参阅 [Import-AzContainerRegistryImage][import-azcontainerregistryimage] cmdlet 参考。

---

* 映像导入可帮助你将内容移动到其他 Azure 区域、订阅或 Azure AD 租户中的容器注册表。 有关详细信息，请参阅[手动将容器注册表移到另一区域](manual-regional-move.md)。

* 了解如何从受到网络限制的容器注册表[禁用项目导出](data-loss-prevention.md)。


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
[install-the-azure-az-powershell-module]: /powershell/azure/install-az-ps
[import-azcontainerregistryimage]: /powershell/module/az.containerregistry/import-azcontainerregistryimage
