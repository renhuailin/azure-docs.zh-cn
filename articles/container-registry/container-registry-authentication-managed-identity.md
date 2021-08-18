---
title: 使用托管标识进行身份验证
description: 通过使用用户分配或系统分配的托管 Azure 标识，提供对专用容器注册表中映像的访问。
ms.topic: article
ms.date: 06/30/2021
ms.openlocfilehash: 84f7d76eb763c8116390501dfbe2a6568849f10f
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286531"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>使用 Azure 托管标识向 Azure 容器注册表验证身份 

使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)从另一个 Azure 资源向 Azure 容器注册表验证身份，而无需提供或管理注册表凭据。 例如，在 Linux VM 上设置用户分配或系统分配的托管标识，以便从容器注册表访问容器映像，就像使用公共注册表一样容易。 或者，将 Azure Kubernetes 服务群集设置为使用其[托管标识](../aks/cluster-container-registry-integration.md)从用于 pod 部署的 Azure 容器注册表拉取容器映像。

本文将详细介绍托管标识以及如何：

> [!div class="checklist"]
> * 在 Azure VM 上启用用户分配或系统分配的标识
> * 授予标识对 Azure 容器注册表的访问权限
> * 使用托管标识访问注册表并拉取容器映像 

为了创建 Azure 资源，本文要求运行 Azure CLI 版本 2.0.55 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

若要设置容器注册表并向其推送容器映像，还必须在本地安装 Docker。 Docker 提供的包可在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

如果不熟悉 Azure 资源功能的托管标识，请参阅此[概述](../active-directory/managed-identities-azure-resources/overview.md)。

为所选的 Azure 资源设置托管标识后，便可以根据需要授予该标识对另一资源的访问权限，这一点与所有安全主体一样。 例如，为托管标识分配角色，该角色对 Azure 中的专用注册表具有拉取、推送和拉取或其他权限。 （有关完整的注册表角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。）可以授予标识对一个或多个资源的访问权限。

然后使用该标识向[支持 Azure AD 身份验证的任何服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)进行身份验证，而无需在代码中放入任何凭据。 选择如何使用托管标识进行身份验证，具体取决于你的方案。 若要使用该标识从虚拟机访问 Azure 容器注册表，请向 Azure 资源管理器验证身份。 

> [!NOTE]
> 目前，创建容器组时，不能使用 Azure 容器实例中的托管标识从 Azure 容器注册表中拉取映像。 该标识仅在正在运行的容器中可用。 若要使用 Azure 容器注册表中的映像部署 Azure 容器实例中的容器组，建议使用其他身份验证方法（如[服务主体](container-registry-auth-service-principal.md)）。

## <a name="create-a-container-registry"></a>创建容器注册表

如果还没有 Azure 容器注册表，请创建一个注册表并向其推送示例容器映像。 有关步骤，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。

本文假设你在注册表中存储了 `aci-helloworld:v1` 容器映像。 以下示例使用注册表名称 *myContainerRegistry*。 请在后续步骤中替换为你自己的注册表和映像名称。

## <a name="create-a-docker-enabled-vm"></a>创建一个启用了 Docker 的 VM

创建一个启用了 Docker 的 Ubuntu 虚拟机。 还需要在该虚拟机上安装 [Azure CLI](/cli/azure/install-azure-cli)。 如果已有 Azure 虚拟机，请跳过此虚拟机创建步骤。

使用 [az vm create][az-vm-create] 部署默认的 Ubuntu Azure 虚拟机。 以下示例在名为 *myResourceGroup* 的现有资源组中创建名为 *myDockerVM* 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 需要几分钟时间。 等该命令完成后，记下 Azure CLI 显示的 `publicIpAddress`。 使用此地址与 VM 建立 SSH 连接。

### <a name="install-docker-on-the-vm"></a>在 VM 上安装 Docker

等 VM 正常运行后，与 VM 建立 SSH 连接。 将 *publicIpAddress* 替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@publicIpAddress
```

运行以下命令以在 VM 上安装 Docker：

```bash
sudo apt update
sudo apt install docker.io -y
```

安装完成后，运行以下命令验证 Docker 在 VM 上是否正常运行：

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

输出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安装 Azure CLI

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 在本文中，请确保安装版本 2.0.55 或更高版本。

退出 SSH 会话。

## <a name="example-1-access-with-a-user-assigned-identity"></a>示例 1：使用用户分配的标识进行访问

### <a name="create-an-identity"></a>创建标识

使用 [az identity create](/cli/azure/identity#az_identity_create) 命令在订阅中创建标识。 可以使用先前用于创建容器注册表或虚拟机的相同资源组，也可以使用不同的资源组。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

若要在以下步骤中配置标识，请使用 [az identity show][az_identity_show] 命令将标识的资源 ID 和服务主体 ID 存储在变量中。

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

当你从虚拟机登录 CLI 时，需要在稍后的步骤中使用标识的 ID，因此请显示以下值：

```bash
echo $userID
```

ID 的格式如下：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>为 VM 配置标识

以下 [az vm identity assign][az-vm-identity-assign] 命令可为 Docker VM 配置用户分配的标识：

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>授予标识对容器注册表的访问权限

现在将标识配置为访问容器注册表。 首先使用 [az acr show][az-acr-show] 命令获取注册表的资源 ID：

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 命令向注册表分配 AcrPull 角色。 此角色将提供对注册表的[拉取权限](container-registry-roles.md)。 若要同时提供拉取和推送权限，请分配 ACRPush 角色。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>使用标识访问注册表

通过 SSH 连接到配置了标识的 Docker 虚拟机。 使用 VM 上安装的 Azure CLI 运行以下 Azure CLI 命令。

首先，使用在 VM 上配置的标识，通过 [az login][az-login] 向 Azure CLI 进行身份验证。 对于 `<userID>`，请替换成在上一步中检索到的标识 ID。 

```azurecli
az login --identity --username <userID>
```

然后，使用 [az acr login][az-acr-login] 向注册表进行身份验证。 使用此命令时，CLI 使用运行 `az login` 时创建的 Active Directory 令牌，以无缝的方式向容器注册表验证会话的身份。 （根据 VM 的设置，可能需要使用 `sudo` 运行此命令和 docker 命令。）

```azurecli
az acr login --name myContainerRegistry
```

你应该会看到 `Login succeeded` 消息。 之后，便可以在不提供凭据的情况下运行 `docker` 命令。 例如，运行 [docker pull][docker-pull] 以拉取 `aci-helloworld:v1` 映像，并指定注册表的登录服务器名称。 登录服务器名称由容器注册表名称（全部小写）后跟 `.azurecr.io` 组成 - 例如，`mycontainerregistry.azurecr.io`。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>示例 2：使用系统分配的标识进行访问

### <a name="configure-the-vm-with-a-system-managed-identity"></a>为 VM 配置系统托管标识

以下 [az vm identity assign][az-vm-identity-assign] 命令可为 Docker VM 配置系统分配的标识：

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

使用 [az vm show][az-vm-show] 命令将变量设置为 VM 标识的值 `principalId`（服务主体 ID），以便在后续步骤中使用。

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>授予标识对容器注册表的访问权限

现在将标识配置为访问容器注册表。 首先使用 [az acr show][az-acr-show] 命令获取注册表的资源 ID：

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 命令向标识分配 AcrPull 角色。 此角色将提供对注册表的[拉取权限](container-registry-roles.md)。 若要同时提供拉取和推送权限，请分配 ACRPush 角色。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>使用标识访问注册表

通过 SSH 连接到配置了标识的 Docker 虚拟机。 使用 VM 上安装的 Azure CLI 运行以下 Azure CLI 命令。

首先，使用 VM 上的系统分配标识通过 [az login][az-login] 向 Azure CLI 进行身份验证。

```azurecli
az login --identity
```

然后，使用 [az acr login][az-acr-login] 向注册表进行身份验证。 使用此命令时，CLI 使用运行 `az login` 时创建的 Active Directory 令牌，以无缝的方式向容器注册表验证会话的身份。 （根据 VM 的设置，可能需要使用 `sudo` 运行此命令和 docker 命令。）

```azurecli
az acr login --name myContainerRegistry
```

你应该会看到 `Login succeeded` 消息。 之后，便可以在不提供凭据的情况下运行 `docker` 命令。 例如，运行 [docker pull][docker-pull] 以拉取 `aci-helloworld:v1` 映像，并指定注册表的登录服务器名称。 登录服务器名称由容器注册表名称（全部小写）后跟 `.azurecr.io` 组成 - 例如，`mycontainerregistry.azurecr.io`。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何将托管标识与 Azure 容器注册表配合使用，以及如何：

> [!div class="checklist"]
> * 在 Azure VM 中启用用户分配或系统分配的标识
> * 授予标识对 Azure 容器注册表的访问权限
> * 使用托管标识访问注册表并拉取容器映像

* 详细了解 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/index.yml)。
* 了解如何将[系统分配的](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/use_system-assigned_managed_identities.md)托管标识或[用户分配的](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/use_user-assigned_managed_identities.md)托管标识与应用服务和 Azure 容器注册表配合使用。


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-show]: /cli/azure/vm#az_vm_show
[az-vm-identity-assign]: /cli/azure/vm/identity#az_vm_identity_assign
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
