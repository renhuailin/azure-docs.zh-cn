---
title: 注册表身份验证选项
description: 专用 Azure 容器注册表的身份验证选项，包括使用 Azure Active Directory 标识、使用服务主体以及使用可选的管理凭据进行登录。
ms.topic: article
ms.date: 06/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04a8e1e4b44340812b0e249255ab394f3081038f
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005774"
---
# <a name="authenticate-with-an-azure-container-registry"></a>使用 Azure 容器注册表进行身份验证

可通过几种方法使用 Azure 容器注册表进行身份验证，并且每种方法适用于一种或多种注册表使用方案。

建议的方法包括：

* 通过[单独的登录](#individual-login-with-azure-ad)直接对注册表进行身份验证
* 应用程序和容器业务流程协调程序可使用 Azure Active Directory (Azure AD) [服务主体](#service-principal)执行无人参与或“无外设”的身份验证

如果将容器注册表与 Azure Kubernetes Service (AKS) 或其他 Kubernetes 群集一起使用，请参阅[从 Kubernetes 使用 Azure 容器注册表进行身份验证的方案](authenticate-kubernetes-options.md)。

## <a name="authentication-options"></a>身份验证选项

下表列出了可用的身份验证方法和典型方案。 有关详细信息，请参阅链接的内容。

| 方法                               | 如何进行身份验证                                           | 方案                                                            | Azure 基于角色的访问控制 (Azure RBAC)                             | 限制                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [单个 AD 标识](#individual-login-with-azure-ad)                | Azure CLI 中的 `az acr login` <br/><br/> Azure PowerShell 中的 `Connect-AzContainerRegistry`                             | 开发人员、测试人员的交互式推送/拉取                                    | 是                              | AD 令牌必须每隔 3 小时续订一次     |
| [AD 服务主体](#service-principal)                  | `docker login`<br/><br/>Azure CLI 中的 `az acr login`<br/><br/> Azure PowerShell 中的 `Connect-AzContainerRegistry`<br/><br/> API 或工具中的注册表登录设置<br/><br/> [Kubernetes 拉取机密](container-registry-auth-kubernetes.md)                                           | 从 CI/CD 管道进行的无人参与推送<br/><br/> 到 Azure 或外部服务的无人参与拉取  | 是                              | 存储过程密码默认有效期为 1 年       |
| [Azure 资源的托管标识](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/>Azure CLI 中的  `az acr login` <br/><br/> Azure PowerShell 中的 `Connect-AzContainerRegistry`                                       | 从 Azure CI/CD 管道进行的无人参与推送<br/><br/> 到 Azure 服务的无人参与拉取<br/><br/>   | 是                              | 仅从[支持 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)的特选 Azure 服务中使用              |
| [AKS 群集托管标识](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | 创建或更新 AKS 群集时附加注册表  | 以无人参与的方式拉取到相同或不同订阅中的 AKS 群集                                                 | 否，仅限拉取访问             | 仅适用于 AKS 群集            |
| [AKS 群集服务主体](authenticate-aks-cross-tenant.md)                    | 创建或更新 AKS 群集时启用  | 从另一个 AD 租户中的注册表中，以无人参与的方式拉取到 AKS 群集                                                  | 否，仅限拉取访问             | 仅适用于 AKS 群集            |
| [管理员用户](#admin-account)                            | `docker login`                                          | 各个开发人员或测试人员的交互式推送/拉取<br/><br/>从注册表到 Azure 应用服务或 Azure 容器实例的映像门户部署                      | 否，始终仅限拉取和推送访问  | 每个注册表一个帐户，不建议用于多个用户         |
| [存储库范围的访问令牌](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>Azure CLI 中的 `az acr login`<br/><br/> Azure PowerShell 中的 `Connect-AzContainerRegistry`<br/><br/> [Kubernetes 拉取机密](container-registry-auth-kubernetes.md)    | 各个开发人员或测试人员对存储库的交互式推送/拉取<br/><br/> 各个系统或外部设备从存储库进行无人参与拉取                  | 是                              | 当前未与 AD 标识集成  |

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 进行单次登录

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

直接使用注册表时（例如向开发工作站拉取映像和从中将映像推送到创建的注册表），可使用单独的 Azure 标识进行身份验证。 使用 [az login](/cli/azure/reference-index#az_login) 登录到 [Azure CLI](/cli/azure/install-azure-cli)，然后运行 [az acr login](/cli/azure/acr#az_acr_login) 命令：

```azurecli
az login
az acr login --name <acrName>
```

使用 `az acr login` 登录时，CLI 将使用执行 `az login` 时创建的令牌和注册表对会话进行无缝身份验证。 若要完成身份验证流，你的环境中必须已安装且正在运行 Docker CLI 和 Docker 守护程序。 `az acr login` 使用 Docker 客户端在 `docker.config` 文件中设置 Azure Active Directory 令牌。 以这种方式登录后，系统会缓存凭据，并且会话中的后续 `docker` 命令将不再需要用户名或密码。

> [!TIP]
> 当希望将 Docker 映像以外的项目（例如 [OCI 项目](container-registry-oci-artifacts.md)）推送或拉取到注册表时，还可以使用 `az acr login` 来对单个标识进行身份验证。

对于注册表访问，`az acr login` 使用的令牌有效期为 3 小时  ，因此，建议在运行 `docker` 命令之前始终登录到注册表。 如果令牌过期，可以通过再次使用 `az acr login` 命令重新进行身份验证来刷新令牌。

配合使用 `az acr login` 和 Azure 标识可提供 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)。 在某些情况下，你可能要在 Azure AD 中使用自己的单个标识登录注册表，或使用特定 [Azure 角色和权限](container-registry-roles.md)配置其他 Azure 用户。 对于跨服务方案，或者若要在不想管理个人访问的情况下满足工作组或部署工作流的需求，还可以使用 [Azure 资源的托管标识](container-registry-authentication-managed-identity.md)进行登录。

### <a name="az-acr-login-with---expose-token"></a>使用 --expose-token 运行 az acr login

在某些情况下，当 Docker 守护程序未在环境中运行时，需使用 `az acr login` 进行身份验证。 例如，你可能需要在 Azure Cloud Shell（提供 Docker CLI，但不运行 Docker 守护程序）中的脚本中运行 `az acr login`。

对于此方案，请先使用 `--expose-token` 参数运行 `az acr login`。 此选项公开访问令牌，而不是通过 Docker CLI 登录。

```azurecli
az acr login --name <acrName> --expose-token
```

输出显示访问令牌（此处为缩写）：

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
```
对于注册表身份验证，建议你将令牌凭据存储在一个安全的位置，并遵循建议的做法来管理 [docker 登录](https://docs.docker.com/engine/reference/commandline/login/)凭据。 例如，将令牌值存储在环境变量中：

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

然后，运行 `docker login`，将 `00000000-0000-0000-0000-000000000000` 作为用户名进行传递并使用访问令牌作为密码：

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

直接使用注册表时（例如向开发工作站拉取映像和从中将映像推送到创建的注册表），可使用单独的 Azure 标识进行身份验证。 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 登录 [Azure PowerShell](/powershell/azure/uninstall-az-ps)，然后运行 ​​[Connect-AzContainerRegistry](/powershell/module/az.containerregistry/connect-azcontainerregistry) cmdlet：

```azurepowershell
Connect-AzAccount
Connect-AzContainerRegistry -Name <acrName>
```

使用 `Connect-AzContainerRegistry` 登录时，PowerShell 将使用执行 `Connect-AzAccount` 时创建的令牌通过注册表对会话进行无缝身份验证。 若要完成身份验证流，你的环境中必须已安装且正在运行 Docker CLI 和 Docker 守护程序。 `Connect-AzContainerRegistry` 使用 Docker 客户端在 `docker.config` 文件中设置 Azure Active Directory 令牌。 以这种方式登录后，系统会缓存凭据，并且会话中的后续 `docker` 命令将不再需要用户名或密码。

> [!TIP]
> 当希望将 Docker 映像以外的项目（例如 [OCI 项目](container-registry-oci-artifacts.md)）推送或拉取到注册表时，还可以使用 `Connect-AzContainerRegistry` 来对单个标识进行身份验证。

对于注册表访问，`Connect-AzContainerRegistry` 使用的令牌有效期为 3 小时  ，因此，建议在运行 `docker` 命令之前始终登录到注册表。 如果令牌过期，可以通过再次使用 `Connect-AzContainerRegistry` 命令重新进行身份验证来刷新令牌。

配合使用 `Connect-AzContainerRegistry` 和 Azure 标识可提供 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)。 在某些情况下，你可能要在 Azure AD 中使用自己的单个标识登录注册表，或使用特定 [Azure 角色和权限](container-registry-roles.md)配置其他 Azure 用户。 对于跨服务方案，或者若要在不想管理个人访问的情况下满足工作组或部署工作流的需求，还可以使用 [Azure 资源的托管标识](container-registry-authentication-managed-identity.md)进行登录。

---

## <a name="service-principal"></a>服务主体

如果为注册表分配了[服务主体](../active-directory/develop/app-objects-and-service-principals.md)，则应用程序或服务可以将其用于无外设身份验证。 服务主体允许对注册表进行 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)，并且可以为注册表分配多个服务主体。 如果拥有多个服务主体，则可为不同应用程序定义不同的访问权限。

容器注册表的可用角色包括：

* **AcrPull**：拉取

* **AcrPush**：拉取和推送

* **所有者**：拉取、推送和为其他用户分配角色

有关完整的角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。

有关创建服务主体以使用 Azure 容器注册表进行身份验证的 CLI 脚本，以及更多指导，请参阅[使用服务主体进行 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)。

## <a name="admin-account"></a>管理员帐户

每个容器注册表包含一个管理员用户帐户，此帐户默认禁用。 可以通过 Azure 门户、Azure CLI、Azure PowerShell 或其他 Azure 工具启用管理员用户并管理其凭据。 管理员帐户对注册表具有完全权限。

当前对于某些方案，需要管理员帐户才能将映像从容器注册表部署到特定 Azure 服务。 例如，在使用 Azure 门户将容器映像从注册表直接部署到 [Azure 容器实例](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal)或[用于容器的 Azure Web 应用](container-registry-tutorial-deploy-app.md)时，需要管理员帐户。

> [!IMPORTANT]
> 管理员帐户专门用于单个用户访问注册表，主要用于测试目的。 建议不要在多个用户之间共享管理帐户凭据。 对于使用管理员帐户进行身份验证的所有用户，他们都将显示为对注册表具有推送和拉取访问权限的单个用户。 更改或禁用此帐户会禁用使用凭据的所有用户的注册表访问权限。 建议用户和服务主体在无外设方案中使用单个标识。
>

管理员帐户有两个密码，这两个密码都可以再生成。 使用这两个密码，可以在再生成一个密码时使用另一个密码保持与注册表的连接。 如果管理员帐户已启用，可以在系统提示时将用户名和/或密码传递到 `docker login` 命令，以对注册表进行基本身份验证。 例如：

```
docker login myregistry.azurecr.io
```

有关管理登录凭据的建议做法，请查看 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令参考。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要启用现有注册表的管理员用户，可以在 Azure CLI 中使用 [az acr update](/cli/azure/acr#az_acr_update) 命令的 `--admin-enabled` 参数：

```azurecli
az acr update -n <acrName> --admin-enabled true
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要为现有注册表启用管理员用户，可以在 Azure PowerShell 中使用 [Update-AzContainerRegistry](/powershell/module/az.containerregistry/update-azcontainerregistry) 命令的 `EnableAdminUser` 参数：

```azurepowershell
Update-AzContainerRegistry -Name <acrName> -ResourceGroupName myResourceGroup -EnableAdminUser
```

---

可以在 Azure 门户中启用管理员用户，操作方法如下：导航你的注册表，在“设置”下选择“访问密钥”，然后在“管理员用户”下选择“启用”     。

![在 Azure 门户中启用管理员用户的用户界面][auth-portal-01]

## <a name="next-steps"></a>后续步骤

* [使用 Azure CLI 推送第一个映像](container-registry-get-started-azure-cli.md)

* [使用 Azure PowerShell 推送第一个映像](container-registry-get-started-powershell.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
