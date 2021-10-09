---
title: 使用受信任的 Azure 服务访问受网络限制的注册表
description: 启用受信任的 Azure 服务实例，以便安全地访问受网络限制的容器注册表以拉取或推送映像
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: da22d525bbe86403f26daf7c3505c5dadffe4b81
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654137"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>允许受信任的服务安全地访问受网络限制的容器注册表（预览版）

Azure 容器注册表允许选择受信任的 Azure 服务访问配置了网络访问规则的注册表。 允许受信任的服务时，受信任的服务实例可以安全地绕过注册表的网络规则并执行拉取或推送映像等操作。 本文介绍如何通过网络受限的 Azure 容器注册表启用和使用受信任的服务。

使用 Azure Cloud Shell 或 Azure CLI 的本地安装来运行本文中的命令示例。 若要在本地使用 Azure CLI，需要安装 2.18 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

允许受信任的 Azure 服务进行注册表访问是一项 **预览版** 功能。

## <a name="limitations"></a>限制

* 对于需要托管标识的注册表访问场景，只能使用系统分配的标识。 目前不支持用户分配的托管标识。
* 允许受信任的服务这一功能不适用于配置了[服务终结点](container-registry-vnet.md)的容器注册表。 此功能只影响受[专用终结点](container-registry-private-link.md)限制或已应用[公共 IP 访问规则](container-registry-access-selected-networks.md)的注册表。 

## <a name="about-trusted-services"></a>关于受信任的服务

Azure 容器注册表具有分层的安全模型，支持多个限制对注册表进行访问的网络配置，其中包括：

* [使用 Azure 专用链接的专用终结点](container-registry-private-link.md)。 配置后，注册表的专用终结点只供虚拟网络中的资源使用专用 IP 地址进行访问。  
* [注册表防火墙规则](container-registry-access-selected-networks.md)，仅允许从特定的公共 IP 地址或地址范围访问注册表的公共终结点。 使用专用终结点时，还可以将防火墙配置为阻止对公共终结点进行的所有访问。

在虚拟网络中进行部署或使用防火墙规则进行配置后，注册表会拒绝从这些源的外部访问用户或服务。 

若干多租户 Azure 服务从不能包括在这些注册表网络设置中的网络运行，使得它们无法执行从注册表拉取或向其推送映像的操作。 通过将某些服务实例指定为“受信任”，注册表所有者就可以允许选择 Azure 资源，以便安全地绕过注册表的网络设置来执行注册表操作。 

### <a name="trusted-services"></a>受信服务

如果启用了注册表的“允许受信任的服务”设置（默认设置），则下列服务的实例可以访问受网络限制的容器注册表。 我们会不断添加更多的服务。

在指示的位置，受信任服务的访问需要在服务实例中额外配置托管标识、分配 [RBAC 角色](container-registry-roles.md)和使用注册表进行身份验证。 有关示例步骤，请参阅本文后面的[受信任的服务工作流](#trusted-services-workflow)。

|受信服务  |支持的使用方案  | 使用 RBAC 角色配置托管标识
|---------|---------|------|
| Azure 安全中心 | 通过[适用于容器注册表的 Azure Defender](scan-images-defender.md) 进行漏洞扫描 | 否 |
|ACR 任务     | [从 ACR 任务访问父注册表或其他注册表](container-registry-tasks-cross-registry-authentication.md)       | 是 |
|机器学习 | 使用自定义 Docker 容器映像在机器学习工作区中[部署](../machine-learning/how-to-deploy-custom-container.md)或[训练](../machine-learning/how-to-train-with-custom-image.md)模型 | 是 |
|Azure 容器注册表 | 向/从受网络限制的 Azure 容器注册表[导入映像](container-registry-import-images.md) | 否 |

> [!NOTE]
> 目前，启用允许受信任的服务设置不适用于某些其他托管的 Azure 服务，包括应用服务和 Azure 容器实例。

## <a name="allow-trusted-services---cli"></a>允许受信任的服务 - CLI

默认情况下，在新的 Azure 容器注册表中启用“允许受信任的服务”设置。 通过运行 [az acr update](/cli/azure/acr#az_acr_update) 命令禁用或启用此设置。

若要禁用此设置，请执行以下命令：

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

若要在现有注册表或已禁用此设置的注册表中启用此设置，请执行以下命令：

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>允许受信任的服务 - 门户

默认情况下，在新的 Azure 容器注册表中启用“允许受信任的服务”设置。 

若要在门户中禁用或重新启用此设置，请执行以下操作：

1. 在门户中，导航到容器注册表。
1. 在“设置”下选择“网络” 。 
1. 在“允许公用网络访问”中，选择“所选网络”或“禁用”。
1. 执行下列操作之一：
    * 若要禁止受信任的服务进行访问，请在“防火墙例外”下取消选中“允许受信任的 Microsoft 服务访问此容器注册表”。 
    * 若要允许受信任的服务，请在“防火墙例外”下选中“允许受信任的 Microsoft 服务访问此容器注册表”。
1. 选择“保存”。

## <a name="trusted-services-workflow"></a>受信任服务工作流

下面是一个典型的工作流，它使受信任的服务的实例可以访问受网络限制的容器注册表。 如果使用服务实例的托管标识绕过注册表的网络规则，则需要此工作流。

1. 在 Azure 容器注册表的某个[受信任的服务](#trusted-services)的实例中启用系统分配的 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
1. 将 [Azure 角色](container-registry-roles.md)的标识分配到注册表。 例如，分配 ACRPull 角色以拉取容器映像。
1. 在受网络限制的注册表中，将此设置配置为允许受信任的服务进行访问。
1. 使用标识的凭据通过受网络限制的注册表进行身份验证。 
1. 从注册表中拉取映像，或执行角色允许的其他操作。

### <a name="example-acr-tasks"></a>示例：ACR 任务

以下示例演示如何使用 ACR 任务作为受信任的服务。 有关任务详细信息，请参阅[使用 Azure 托管标识在 ACR 任务中进行跨注册表的身份验证](container-registry-tasks-cross-registry-authentication.md)。

1. 创建或更新 Azure 容器注册表。
[创建](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) ACR 任务。 
    * 在创建任务时启用系统分配的托管标识。
    * 禁用任务的默认身份验证模式 (`--auth-mode None`)。
1. 为任务标识分配[用于访问注册表的 Azure 角色](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources)。 例如，分配有权拉取和推送映像的 AcrPush 角色。
2. [将注册表的托管标识凭据添加](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task)到任务。
3. 若要确认任务绕过网络限制，请在注册表中[禁用公共访问](container-registry-access-selected-networks.md#disable-public-network-access)。
4. 运行任务。 如果注册表和任务已正确配置，则该任务会成功运行，因为注册表允许进行访问。

若要对禁止受信任的服务进行访问这一功能进行测试，请执行以下操作：

1. 禁用允许受信任的服务进行访问的设置。
1. 再次运行任务。 在这种情况下，任务运行会失败，因为注册表不再允许任务进行访问。

## <a name="next-steps"></a>后续步骤

* 要使用虚拟网络中的专用终结点限制对注册表的访问，请参阅[为 Azure 容器注册表配置 Azure 专用链接](container-registry-private-link.md)。
* 若要设置注册表防火墙规则，请参阅[配置公共 IP 网络规则](container-registry-access-selected-networks.md)。