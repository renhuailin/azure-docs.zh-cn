---
title: 注册表登录故障排除
description: 登录到 Azure 容器注册表时的常见问题的症状、原因和解决方法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 48b3069911b16fce23017d562d0b99fbd8f0268c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733008"
---
# <a name="troubleshoot-registry-login"></a>注册表登录故障排除

本文将帮助你排查在登录 Azure 容器注册表时可能遇到的问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 无法使用 `docker login` 和/或 `az acr login` 登录到注册表
* 无法登录到注册表，出现错误 `unauthorized: authentication required` 或 `unauthorized: Application not registered with AAD`
* 无法登录到注册表，出现 Azure CLI 错误 `Could not connect to the registry login server`
* 无法推送或拉取映像，出现 Docker 错误 `unauthorized: authentication required`
* 无法从 Azure Kubernetes 服务、Azure DevOps 或其他 Azure 服务访问注册表
* 无法访问注册表，出现错误 `Error response from daemon: login attempt failed with status: 403 Forbidden` - 请参阅[排查与注册表相关的网络问题](container-registry-troubleshoot-access.md)
* 无法在 Azure 门户中访问或查看注册表设置，或者无法使用 Azure CLI 管理注册表

## <a name="causes"></a>原因

* 在你的环境中未正确配置 Docker - [解决方案](#check-docker-configuration)
* 注册表不存在或名称不正确 - [解决方案](#specify-correct-registry-name)
* 注册表凭据无效 - [解决方案](#confirm-credentials-to-access-registry)
* 凭据未被授权用于推送、拉取或 Azure 资源管理器操作 - [解决方案](#confirm-credentials-are-authorized-to-access-registry)
* 凭据已过期 - [解决方案](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>进一步诊断 

运行 [az acr check-health](/cli/azure/acr#az_acr_check_health) 命令可详细了解注册表环境的运行状况，以及对目标注册表的访问（可选）。 例如，诊断 Docker 配置错误或 Azure Active Directory 登录问题。 

参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)以查看命令示例。 如果报告了错误，请查看[错误参考](container-registry-health-error-reference.md)和以下部分，以了解建议的解决方案。

如果在将注册表与 Azure Kubernetes 服务配合使用时遇到问题，请运行 [az aks check-acr](/cli/azure/aks#az_aks_check_acr) 命令，以验证是否可以从 AKS 群集访问该注册表。

> [!NOTE]
> 如果存在阻止访问注册表的防火墙或网络配置，也会发生某些身份验证或授权错误。 请参阅[排查与注册表相关的网络问题](container-registry-troubleshoot-access.md)。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="check-docker-configuration"></a>检查 Docker 配置

大多数 Azure 容器注册表身份验证流需要本地 Docker 安装，因此你可以向注册表进行身份验证，以便执行推送和拉取映像等操作。 确认 Docker CLI 客户端和守护程序（Docker 引擎）正在环境中运行。 你需要 Docker 客户端版本 18.03 或更高版本。

相关链接：

* [身份验证概述](container-registry-authentication.md#authentication-options)
* [容器注册表常见问题解答](container-registry-faq.yml)

### <a name="specify-correct-registry-name"></a>指定正确的注册表名称

使用 `docker login` 时，请提供注册表的完整登录服务器名称，如 myregistry.azurecr.cn。 请确保只使用小写字母。 示例：

```console
docker login myregistry.azurecr.io
```

将 [az acr login](/cli/azure/acr#az_acr_login) 与 Azure Active Directory 标识配合使用时，请先[登录 Azure CLI](/cli/azure/authenticate-azure-cli)，然后指定注册表的 Azure 资源名称。 资源名称是在创建注册表时提供的名称，如 myregistry（没有域后缀）。 示例：

```azurecli
az acr login --name myregistry
```

相关链接：

* [az acr login 成功，但 Docker 失败并出现错误：未授权: 需要身份验证](container-registry-faq.yml#az-acr-login-succeeds-but-docker-fails-with-error--unauthorized--authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>确认用于访问注册表的凭据

检查用于你的方案的凭据或由注册表所有者提供给你的凭据的有效性。 一些可能的问题：

* 如果使用 Active Directory 服务主体，请确保在 Active Directory 租户中使用正确的凭据：
  * 用户名 - 服务主体应用程序 ID（也称为 *客户端 ID*）
  * 密码 - 服务主体密码（也称为 *客户端密码*）
* 如果使用 Azure 服务（如 Azure Kubernetes 服务或 Azure DevOps）来访问注册表，请确认服务的注册表配置。 
* 如果运行了带有 `--expose-token` 选项的 `az acr login`，以允许在不使用 Docker 守护程序的情况下登录注册表，请确保使用用户名 `00000000-0000-0000-0000-000000000000` 进行身份验证。
* 如果将注册表配置为[匿名拉取访问](container-registry-faq.yml#how-do-i-enable-anonymous-pull-access-)，则以前的 Docker 登录名存储的现有 Docker 凭据可阻止匿名访问。 先运行 `docker logout`，再在注册表上尝试匿名拉取操作。

相关链接：

* [身份验证概述](container-registry-authentication.md#authentication-options)
* [使用 Azure AD 进行单次登录](container-registry-authentication.md#individual-login-with-azure-ad)
* [使用服务主体登录](container-registry-auth-service-principal.md)
* [使用托管标识登录](container-registry-authentication-managed-identity.md)
* [使用存储库范围内的令牌登录](container-registry-repository-scoped-permissions.md)
* [使用管理员帐户登录](container-registry-authentication.md#admin-account)
* [Azure AD 身份验证和授权错误代码](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) 参考

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>确认凭据已被授权访问注册表

确认与凭据关联的注册表权限，例如用于从注册表拉取映像的Azure 角色 `AcrPull`，或用于推送映像的 `AcrPush` 角色。 

若要在门户中访问注册表或要使用 Azure CLI 进行注册表管理，至少需要用于执行 Azure 资源管理器操作的 `Reader` 角色或等效权限。

如果你的权限最近更改为允许通过门户访问注册表，你可能需要尝试在浏览器中使用 incognito 或专用会话，以避免使用任何过时的浏览器缓存或 Cookie。

你或注册表所有者必须在订阅中具有足够的权限，才能添加或删除角色分配。

相关链接：

* [Azure 角色和权限 - Azure 容器注册表](container-registry-roles.md)
* [使用存储库范围内的令牌登录](container-registry-repository-scoped-permissions.md)
* [使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md)
* [使用门户来创建可以访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)
* [创建新的应用程序机密](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD 身份验证和授权代码](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>检查凭据是否未过期

令牌和 Active Directory 凭据可能会在定义的时间段后过期，从而阻止访问注册表。 若要允许访问注册表，可能需要重置或重新生成凭据。

* 如果将单个 AD 标识、托管标识或服务主体用于注册表登录，AD 令牌将在 3 小时后过期。 请再次登录到注册表。  
* 如果将 AD 服务主体与已过期的客户端密码配合使用，则订阅所有者或帐户管理员需要重置凭据或生成新的服务主体。
* 如果使用[存储库范围内的令牌](container-registry-repository-scoped-permissions.md)，则注册表所有者可能需要重置密码或生成新令牌。

相关链接：

* [重置服务主体凭据](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [重新生成令牌密码](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [使用 Azure AD 进行单次登录](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>高级故障排除

如果在注册表中启用了[收集资源日志](monitor-service.md)，请查看 ContainerRegistryLoginEvents 日志。 此日志存储身份验证事件和状态，包括传入标识和 IP 地址。 查询此日志可获得有关[注册表身份验证失败](monitor-service.md#registry-authentication-failures)的信息。 

相关链接：

* [用于诊断评估和审核的日志](./monitor-service.md)
* [容器注册表常见问题解答](container-registry-faq.yml)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果此处无法解决你的问题，请参阅以下选项。

* 其他注册表故障排除主题包括：
  * [排查与注册表相关的网络问题](container-registry-troubleshoot-access.md)
  * [注册表性能故障排除](container-registry-troubleshoot-performance.md)
* [社区支持](https://azure.microsoft.com/support/community/)选项
* [Microsoft 问答](/answers/products/)
* [开具支持票证](https://azure.microsoft.com/support/create-ticket/) - 根据你提供的信息，可能会针对注册表中的身份验证失败运行快速诊断
