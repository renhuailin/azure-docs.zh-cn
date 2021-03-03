---
title: 使用受信任的 Azure 服务访问受网络限制的注册表
description: 启用受信任的 Azure 服务实例，以安全地访问受网络限制的容器注册表以请求或推送映像
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 3cc32630ea689891e7ba75163c33bc499a38becd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716476"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>允许受信任的服务安全访问受网络限制的容器注册表 (预览) 

Azure 容器注册表可允许选择受信任的 Azure 服务访问配置了网络访问规则的注册表。 允许受信任的服务时，受信任的服务实例可以安全地绕过注册表的网络规则并执行请求或推送映像等操作。 服务实例的托管标识用于访问，必须分配有 Azure 角色并使用注册表进行身份验证。

使用 Azure CLI 的 Azure Cloud Shell 或本地安装运行本文中的命令示例。 如果要在本地使用，则需要2.18 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

允许受信任的 Azure 服务进行注册表访问是一项 **预览** 功能。

## <a name="limitations"></a>限制

* 必须在 [受信任的服务](#trusted-services) 中使用启用系统分配的托管标识，才能访问受网络限制的容器注册表。 当前不支持用户分配的托管标识。
* 允许受信任的服务不适用于配置了 [服务终结点](container-registry-vnet.md)的容器注册表。 此功能只影响受 [专用终结点](container-registry-private-link.md) 限制或已应用 [公共 IP 访问规则](container-registry-access-selected-networks.md) 的注册表项。 

## <a name="about-trusted-services"></a>关于可信服务

Azure 容器注册表具有分层的安全模型，该模型支持多个限制对注册表的访问的网络配置，包括：

* [具有 Azure 专用链接的专用终结点](container-registry-private-link.md)。 配置后，只能使用专用 IP 地址对虚拟网络中的资源进行注册表专用终结点的访问。  
* [注册表防火墙规则](container-registry-access-selected-networks.md)，仅允许从特定的公共 IP 地址或地址范围访问注册表的公共终结点。 你还可以将防火墙配置为在使用专用终结点时阻止对公共终结点的所有访问。

在虚拟网络中部署或使用防火墙规则配置后，默认情况下，注册表会拒绝来自这些源外部的用户或服务的访问权限。 

多租户 Azure 服务从这些注册表网络设置中不能包含的网络中运行，阻止它们向注册表拉取或推送映像。 通过将某些服务实例指定为 "受信任"，注册表所有者可以允许选择 Azure 资源，以安全地绕过注册表的网络设置来请求或推送映像。 

### <a name="trusted-services"></a>受信服务

如果 (默认的) 启用了注册表的 " **允许受信任服务** " 设置，则下列服务的实例可以访问受网络限制的容器注册表。 随着时间的推移，将添加更多服务。

|受信服务  |支持的使用方案  |
|---------|---------|
|ACR 任务     | [从 ACR 任务访问不同注册表](container-registry-tasks-cross-registry-authentication.md)       |
|Azure 容器注册表 | [从另一个 Azure 容器注册表导入映像](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> 给予，启用 "允许受信任的服务" 设置不允许其他托管 Azure 服务的实例（包括应用服务、Azure 容器实例和 Azure 安全中心）访问受网络限制的容器注册表。

## <a name="allow-trusted-services---cli"></a>允许受信任的服务-CLI

默认情况下，在新的 Azure 容器注册表中启用 "允许受信任的服务" 设置。 通过运行 [az acr update](/cli/azure/acr#az-acr-update) 命令禁用或启用设置。

若要禁用：

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

若要在现有注册表或已禁用的注册表中启用设置，请执行以下操作：

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>允许受信任的服务-门户

默认情况下，在新的 Azure 容器注册表中启用 "允许受信任的服务" 设置。 

若要禁用或重新启用门户中的设置：

1. 在门户中，导航到容器注册表。
1. 在“设置”下选择“网络” 。 
1. 在 " **允许访问公用网络**" 中，选择 " **所选网络** " 或 " **禁用**"。
1. 执行下列操作之一：
    * 若要禁用受信任的服务访问，请在 " **防火墙例外**" 下，取消选中 " **允许受信任的 Microsoft 服务访问此容器注册表**"。 
    * 若要允许受信任的服务，请在 " **防火墙例外**" 下选中 " **允许受信任的 Microsoft 服务访问此容器注册表**"。
1. 选择“保存”。

## <a name="trusted-services-workflow"></a>可信服务工作流

下面是一个典型的工作流，用于启用受信任服务的实例来访问受网络限制的容器注册表。

1. 为 azure 容器注册表的某个[受信任服务](#trusted-services)的实例中的 azure 资源启用系统分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
1. 将 [Azure 角色](container-registry-roles.md) 的标识分配给注册表。 例如，将 ACRPull 角色分配给拉取容器映像。
1. 在 "受网络限制的注册表" 中，将设置配置为允许受信任的服务访问。
1. 使用标识的凭据通过受网络限制的注册表进行身份验证。 
1. 从注册表中提取映像，或执行该角色允许的其他操作。

### <a name="example-acr-tasks"></a>示例： ACR 任务

下面的示例演示如何使用 ACR 任务作为受信任的服务。 有关任务详细信息，请参阅 [使用 Azure 托管标识的 ACR 任务中的跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md) 。

1. 创建或更新 Azure 容器注册表，并将 [示例基映像推送](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) 到注册表。 此注册表是方案的 *基本注册表* 。
1. 在第二个 Azure 容器注册表中， [定义](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) 并 [创建](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) 一个 ACR 任务，以从基本注册表中提取映像。 创建任务时，启用系统分配的托管标识。
1. 将任务标识指定为 [Azure 角色以访问基本注册表](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources)。 例如，分配有权提取映像的 AcrPull 角色。
1. [向任务添加托管标识凭据](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) 。
1. 若要确认任务绕过网络限制，请在基本注册表中 [禁用公共访问](container-registry-access-selected-networks.md#disable-public-network-access) 。
1. 运行该任务。 如果基本注册表和任务已正确配置，则该任务会成功运行，因为基注册表允许访问。

测试受信任服务的禁用访问：

1. 在基本注册表中，禁用 "允许受信任的服务进行访问" 设置。
1. 再次运行该任务。 在这种情况下，任务运行将失败，因为基础注册表不再允许任务访问。

## <a name="next-steps"></a>后续步骤

* 要使用虚拟网络中的专用终结点限制对注册表的访问，请参阅[为 Azure 容器注册表配置 Azure 专用链接](container-registry-private-link.md)。
* 若要设置注册表防火墙规则，请参阅 [配置公共 IP 网络规则](container-registry-access-selected-networks.md)。
