---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的访问和标识
description: 了解 Azure Kubernetes 服务 (AKS) 中的访问和标识，包括 Azure Active Directory 集成、Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 以及角色和绑定。
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 98044f6ff6311241717cb66a6e26a72702d749e6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181442"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 的访问和标识选项

可通过不同的方式来对 Kubernetes 群集进行身份验证、控制访问权限/授权和实施保护。 使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)，可以仅向用户、组和服务帐户授予对所需资源的访问权限。 借助 Azure Kubernetes 服务 (AKS)，可以通过使用 Azure Active Directory 和 Azure RBAC 进一步增强安全性和权限结构。 这些方法有助于保护群集访问，并仅向开发者和操作员提供所需的最低权限。

本文介绍了有助于在 AKS 中进行身份验证和分配权限的核心概念。

## <a name="aks-service-permissions"></a>AKS 服务权限

创建群集时，AKS 会代表创建群集的用户创建或修改在创建和运行群集时所需的资源，例如 VM 和 NIC。 此标识与群集的标识权限不同，后者是在群集创建过程中创建的。

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>创建和操作群集的标识的权限

创建和操作群集的标识需要以下权限。

| 权限 | 原因 |
|---|---|
| Microsoft.Compute/diskEncryptionSets/read | 读取磁盘加密集 ID 时必需。 |
| Microsoft.Compute/proximityPlacementGroups/write | 更新邻近放置组时必需。 |
| Microsoft.Network/applicationGateways/read <br/> Microsoft.Network/applicationGateways/write <br/> Microsoft.Network/virtualNetworks/subnets/join/action | 配置应用程序网关和加入子网时必需。 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 使用自定义 VNET 为子网配置网络安全组时必需。|
| Microsoft.Network/publicIPAddresses/join/action <br/> Microsoft.Network/publicIPPrefixes/join/action | 在标准负载均衡器上配置出站公共 IP 时必需。 |
| Microsoft.OperationalInsights/workspaces/sharedkeys/read <br/> Microsoft.OperationalInsights/workspaces/read <br/> Microsoft.OperationsManagement/solutions/write <br/> Microsoft.OperationsManagement/solutions/read <br/> Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 为容器创建和更新 Log Analytics 工作区和 Azure 监视时必需。 |

### <a name="aks-cluster-identity-permissions"></a>AKS 群集标识权限

以下权限由 AKS 群集标识使用，该标识在创建 AKS 群集时随群集创建并与群集相关联。 使用每个权限的原因如下：

| 权限 | 原因 |
|---|---|
| Microsoft.ContainerService/managedClusters/*  <br/> | 创建用户和运行群集时必需的
| Microsoft.Network/loadBalancers/delete <br/> Microsoft.Network/loadBalancers/read <br/> Microsoft.Network/loadBalancers/write | 为 LoadBalancer 服务配置负载均衡器时必需。 |
| Microsoft.Network/publicIPAddresses/delete <br/> Microsoft.Network/publicIPAddresses/read <br/> Microsoft.Network/publicIPAddresses/write | 为 LoadBalancer 服务查找和配置公共 IP 时必需。 |
| Microsoft.Network/publicIPAddresses/join/action | 为 LoadBalancer 服务配置公共 IP 时必需。 |
| Microsoft.Network/networkSecurityGroups/read <br/> Microsoft.Network/networkSecurityGroups/write | 为 LoadBalancer 服务创建或删除安全规则时必需。 |
| Microsoft.Compute/disks/delete <br/> Microsoft.Compute/disks/read <br/> Microsoft.Compute/disks/write <br/> Microsoft.Compute/locations/DiskOperations/read | 配置 AzureDisk 时必需。 |
| Microsoft.Storage/storageAccounts/delete <br/> Microsoft.Storage/storageAccounts/listKeys/action <br/> Microsoft.Storage/storageAccounts/read <br/> Microsoft.Storage/storageAccounts/write <br/> Microsoft.Storage/operations/read | 为 AzureFile 或 AzureDisk 配置存储帐户时必需。 |
| Microsoft.Network/routeTables/read <br/> Microsoft.Network/routeTables/routes/delete <br/> Microsoft.Network/routeTables/routes/read <br/> Microsoft.Network/routeTables/routes/write <br/> Microsoft.Network/routeTables/write | 为节点配置路由表和路由时必需。 |
| Microsoft.Compute/virtualMachines/read | 查找 VMAS 中的虚拟机的信息（例如区域、容错域、大小和数据磁盘）时必需。 |
| Microsoft.Compute/virtualMachines/write | 将 AzureDisk 附加到 VMAS 中的虚拟机时必需。 |
| Microsoft.Compute/virtualMachineScaleSets/read <br/> Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read <br/> Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read | 查找虚拟机规模集中的虚拟机的信息（例如区域、容错域、大小和数据磁盘）时必需。 |
| Microsoft.Network/networkInterfaces/write | 将 VMAS 中的虚拟机添加到负载均衡器后端地址池时必需。 |
| Microsoft.Compute/virtualMachineScaleSets/write | 将虚拟机规模集添加到负载均衡器后端地址池以及在虚拟机规模集中横向扩展节点时必需。 |
| Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write | 附加 AzureDisk 以及将虚拟机规模集中的虚拟机添加到负载均衡器时必需。 |
| Microsoft.Network/networkInterfaces/read | 搜索 VMAS 中的虚拟机的内部 IP 和负载均衡器后端地址池时必需。 |
| Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 搜索虚拟机规模集中的虚拟机的内部 IP 和负载均衡器后端地址池时必需。 |
| Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipconfigurations/publicipaddresses/read | 查找虚拟机规模集中的虚拟机的公共 IP 时必需。 |
| Microsoft.Network/virtualNetworks/read <br/> Microsoft.Network/virtualNetworks/subnets/read | 验证另一个资源组中是否存在内部负载均衡器的子网时必需。 |
| Microsoft.Compute/snapshots/delete <br/> Microsoft.Compute/snapshots/read <br/> Microsoft.Compute/snapshots/write | 配置 AzureDisk 的快照时必需。 |
| Microsoft.Compute/locations/vmSizes/read <br/> Microsoft.Compute/locations/operations/read | 查找虚拟机大小以查找 AzureDisk 卷限制时必需。 |

### <a name="additional-cluster-identity-permissions"></a>其他群集标识权限

创建具有特定属性的群集时，群集标识需要以下其他权限。 不会自动分配这些权限，因此，在创建群集标识之后，必须将这些权限添加到群集标识。

| 权限 | 原因 |
|---|---|
| Microsoft.Network/networkSecurityGroups/write <br/> Microsoft.Network/networkSecurityGroups/read | 使用另一资源组中的网络安全组时必需。 为 LoadBalancer 服务配置安全规则时必需。 |
| Microsoft.Network/virtualNetworks/subnets/read <br/> Microsoft.Network/virtualNetworks/subnets/join/action | 使用另一资源组（例如自定义 VNET）中的子网时必需。 |
| Microsoft.Network/routeTables/routes/read <br/> Microsoft.Network/routeTables/routes/write | 如果使用的子网与另一资源组（例如采用自定义路由表的自定义 VNET）中的路由表相关联，则需要此权限。 若要验证是否已存在一个对应于另一资源组中的子网的子网，则需要此权限。 |
| Microsoft.Network/virtualNetworks/subnets/read | 如果使用另一资源组中的内部负载均衡器，则需要此权限。 验证资源组中是否已存在内部负载均衡器的子网时必需。 |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes 基于角色的访问控制 (Kubernetes RBAC)

为了精确地筛选用户可执行的操作，Kubernetes 采用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 使用此控制机制，可以向用户或用户组分配执行各种操作的权限，例如创建或修改资源，或者查看正在运行的应用程序工作负载的日志。 可将这些权限的范围限制为单个命名空间，也可以授予面向整个 AKS 群集的权限。 使用 Kubernetes RBAC，可通过创建“角色”来定义权限，然后通过“角色绑定”将这些角色分配给用户 。

有关详细信息，请参阅[使用 Kubernetes RBAC 授权][kubernetes-rbac]。

### <a name="roles-and-clusterroles"></a>角色和 ClusterRole

使用 Kubernetes RBAC 向用户分配权限之前，请先将这些权限定义为“角色”。 Kubernetes 角色可“授予”权限。 不存在“拒绝”权限的概念。

角色用于授予命名空间内的权限。 若需要针对整个群集或给定命名空间外的群集资源来授予权限，可以改用“ClusterRole”。

ClusterRole 的工作原理与授予对资源的权限相同，但前者可应用于整个群集而非特定命名空间中的资源。

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding 和 ClusterRoleBinding

定义了角色来授予针对资源的权限后，可通过 RoleBinding 来分配这些 Kubernetes RBAC 权限。 若 AKS 群集[与 Azure Active Directory (Azure AD) 集成](#azure-active-directory-integration)，则向 Azure AD 用户授予在群集中执行操作的权限的过程就称为“绑定”。有关具体操作，请参阅[使用 Kubernetes 基于角色的访问控制和 Azure Active Directory 标识来控制对群集资源的访问](azure-ad-rbac.md)。

角色绑定用于针对给定命名空间分配角色。 此方法可以从逻辑上分离各 AKS 群集，使用户只能访问向其分配的命名空间中的应用程序资源。 若需要针对整个群集或给定命名空间外的群集资源来绑定角色，可以改用“ClusterRoleBinding”。

ClusterRoleBinding 的工作原理与向用户绑定角色相同，但前者可应用于整个群集而非特定命名空间中的资源。 使用此方法，可向管理员或支持工程师授予对 AKS 群集中所有资源的访问权限。


> [!NOTE]
> Microsoft/AKS 所执行的任何群集操作都是经用户同意，在内置 Kubernetes 角色 `aks-service` 和内置角色绑定 `aks-service-rolebinding` 下执行的。 此角色允许 AKS 对群集问题进行故障排除和诊断，但不能修改权限，也不能创建角色或角色绑定，或者执行其他高特权操作。 仅在具有实时 (JIT) 访问权限的活动支持票证下启用角色访问。 阅读并详细了解 [AKS 支持策略](support-policies.md)。


### <a name="kubernetes-service-accounts"></a>Kubernetes 服务帐户

Kubernetes 中的一个主要用户类型是“服务帐户”。 服务帐户存在于 Kubernetes API 中并由 Kubernetes API 进行管理。 服务帐户的凭据存储为 Kubernetes 机密，可供得到授权的 Pod 用于与 API 服务器进行通信。 大多数 API 请求都会提供服务帐户或普通用户帐户的身份验证令牌。

普通用户帐户允许人工管理员或开发人员进行更为传统的访问，而不仅仅是服务和进程。 Kubernetes 本身不提供存储常规用户帐户和密码的标识管理解决方案。 而是将外部标识解决方案集成到 Kubernetes 中。 对于 AKS 群集，此集成标识解决方案就是 Azure Active Directory。

若要详细了解 Kubernetes 中的标识选项，请参阅 [Kubernetes 身份验证][kubernetes-authentication]。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 集成

可通过集成 Azure Active Directory (AD) 增强 AKS 群集的安全性。 Azure AD 是基于数十年的企业标识管理经验推出的基于云的多租户目录，也是一种将核心目录服务、应用程序访问管理和标识保护相结合的标识管理服务。 借助 Azure AD，可以将本地标识集成到 AKS 群集中，提供帐户管理和安全性的单一源。

![Azure Active Directory 与 AKS 群集集成](media/concepts-identity/aad-integration.png)

借助集成了 Azure AD 的 AKS 群集，可授权用户或组访问一个命名空间或多个群集内的 Kubernetes 资源。 若要获取 `kubectl` 配置上下文，用户可以运行 [az aks get-credentials][az-aks-get-credentials] 命令。 随后在用户使用 `kubectl` 与 AKS 群集进行交互时，系统会提示他们使用自己的 Azure AD 凭据登录。 此方法提供用户帐户管理和密码凭据的单一源。 用户只能访问由群集管理员定义的资源。

使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][openid-connect]。 在 Kubernetes 群集内部，使用 [Webhook 令牌身份验证][webhook-token-docs]来验证身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。

### <a name="webhook-and-api-server"></a>Webhook 和 API 服务器

![Webhook 和 API 服务器身份验证流](media/concepts-identity/auth-flow.png)

如上图所示，API 服务器调用 AKS Webhook 服务器并执行以下步骤：

1. Kubectl 使用 Azure AD 客户端应用程序，通过 [OAuth 2.0 设备授权授予流](../active-directory/develop/v2-oauth2-device-code.md)来登录用户。
2. Azure AD 提供 access_token、id_token 和 refresh_token。
3. 用户使用 kubeconfig 中的 access_token 来向 kubectl 发出请求。
4. Kubectl 将 access_token 发送到 API 服务器。
5. API 服务器配置身份验证 WebHook 服务器来执行验证。
6. 身份验证 Webhook 服务器将检查 Azure AD 公共签名密钥，以确认 JSON Web 令牌签名有效。
7. 服务器应用程序使用用户提供的凭据从 MS Graph API 查询已登录用户的组成员身份。
8. 将向 API 服务器发送一个响应，其中包含用户信息，例如访问令牌的用户主体名称 (UPN) 声明，以及基于对象 ID 的用户的组成员身份。
9. API 基于 Kubernetes Role/RoleBinding 执行授权决策。
10. 授权后，API 服务器会将响应返回到 kubectl。
11. Kubectl 向用户提供反馈。
 
[在此处](managed-aad.md)了解如何将 AKS 与 AAD 集成。

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure 基于角色的访问控制 (Azure RBAC)

Azure RBAC 是在 [Azure 资源管理器](../azure-resource-manager/management/overview.md)基础上构建的授权系统，针对 Azure 资源提供精细的访问权限管理。

 Azure RBAC 用于 Azure 订阅中的资源，而 Kubernetes RBAC 用于 AKS 群集中的 Kubernetes 资源。 

使用 Azure RBAC，可创建“角色定义”，描述要应用的权限。 随后可通过角色分配在特定范围内向用户或组分配此角色定义，该范围可以是单个资源、资源组或整个订阅 。

有关详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？][azure-rbac]

完全操作 AKS 群集需要两个级别的访问权限： 
1. [访问 Azure 订阅中的 AKS 资源](#azure-rbac-to-authorize-access-to-the-aks-resource)。 借助此过程，可以使用 AKS API 来控制群集缩放或升级，还可以拉取 kubeconfig。
2. 访问 Kubernetes API。 此访问通过 [Kubernetes RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) 进行控制（传统方式），或通过[将 Azure RBAC 与 AKS 集成以实现 Kubernetes 授权](#azure-rbac-for-kubernetes-authorization-preview)来进行控制。

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>使用 Azure RBAC 授予对 AKS 资源的访问权限

使用 Azure RBAC，可为用户（或标识）提供对一个或多个订阅中的 AKS 资源的精细访问权限。 例如，你可拥有 [Azure Kubernetes 服务参与者角色](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)，以便执行缩放和升级群集等操作。 而其他用户可能拥有 [Azure Kubernetes 服务群集管理员角色](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)，该角色只授予拉取管理员 kubeconfig 的权限。

此外，可为用户提供常规[参与者](../role-based-access-control/built-in-roles.md#contributor)角色，该角色包含上述权限以及可能对 AKS 资源进行的所有操作（管理权限本身除外）。

在[此处](control-kubeconfig-access.md)详细了解如何使用 Azure RBAC 来保护对 kubeconfig 文件的访问，该文件可提供对 Kubernetes API 的访问权限。

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>使用 Azure RBAC 进行 Kubernetes 授权（预览）

使用 Azure RBAC 集成，AKS 将使用 Kubernetes 授权 Webhook 服务器来允许你使用 Azure 角色定义和角色分配管理 Azure AD 集成 K8s 群集资源的权限和分配。

![使用 Azure RBAC 执行 Kubernetes 授权流](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

如上图所示，使用 Azure RBAC 集成时，对 Kubernetes API 的所有请求都将遵循 [Azure Active Directory 集成 "部分](#azure-active-directory-integration)所述的相同身份验证流。 

但在此之后，只要 AAD 中存在发出请求的标识，请求实际上就由 Azure 进行授权，而不是仅依赖于 Kubernetes RBAC 进行授权。 如果 AAD 中不存在该标识（例如，Kubernetes 服务帐户），则 Azure RBAC 不会启动，它将是普通的 Kubernetes RBAC。

在这种情况下，你可以向用户授予四个内置角色之一，或者创建自定义角色，就像使用 Kubernetes 角色时一样，但在此情况下，你将使用 Azure RBAC 机制和 API。 

例如，使用此功能，你不仅可以向用户授予对订阅中的 AKS 资源的权限，而且还可以设置并授予用户在每个群集中将拥有的对 Kubernetes API 的访问进行控制的角色和权限。 例如，你可以在订阅范围内授予 `Azure Kubernetes Service RBAC Viewer` 角色，其接收人将能够列出和获取所有群集中的所有 Kubernetes 对象，但不能修改它们。

> [!IMPORTANT]
> 请注意，在使用此功能之前，你需要启用 Azure RBAC Kubernetes 授权。 有关详细信息和分步指南， [请参阅此处](manage-azure-rbac.md)。

#### <a name="built-in-roles"></a>内置角色

AKS 提供以下四个内置角色。 它们类似于 [Kubernetes 内置角色](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles)，但有一些不同之处，例如支持 CRD。 有关每个内置角色允许的操作的完整列表，请参阅[此文](../role-based-access-control/built-in-roles.md)。

| 角色                                | 描述  |
|-------------------------------------|--------------|
| Azure Kubernetes 服务 RBAC 查看者  | 允许进行只读访问并查看命名空间中的大多数对象。 不允许查看角色或角色绑定。 此角色不允许查看 `Secrets`，因为通过读取机密内容可以访问命名空间中的 `ServiceAccount` 凭据，这样就会允许以命名空间中任何 `ServiceAccount` 的身份进行 API 访问（一种特权提升形式）  |
| Azure Kubernetes 服务 RBAC 写入者 | 允许对命名空间中的大多数对象进行读/写访问。 此角色不允许查看或修改角色或角色绑定。 但是，此角色允许以命名空间中任何 ServiceAccount 的身份访问 `Secrets` 和运行 Pod，因此可用于获取命名空间中任何 ServiceAccount 的 API 访问级别。 |
| Azure Kubernetes 服务 RBAC 管理员  | 允许要在命名空间内授予的管理员访问权限。 允许对命名空间（或群集范围）中的大多数资源进行读/写访问，包括在命名空间内创建角色和角色绑定。 此角色不允许对资源配额或命名空间本身进行写入访问。 |
| Azure Kubernetes 服务 RBAC 群集管理员  | 允许超级用户访问权限（对任何资源执行任何操作）。 它提供对群集中每个资源和所有命名空间的完全控制。 |


## <a name="summary"></a>摘要

此表总结了当启用 Azure AD 集成时用户可以向 Kubernetes 进行身份验证的方式。  在所有情况下，用户的命令序列都是：
1. 运行 `az login` 对 Azure 进行身份验证。
1. 运行 `az aks get-credentials` 将群集的凭据下载到 `.kube/config` 中。
1. 运行 `kubectl` 命令（第一个命令可能会触发基于浏览器的身份验证以向群集进行身份验证，如下表所述）。

第二列中提到的“角色授予”是 Azure 门户中的“访问控制”选项卡上显示的 Azure RBAC 角色授予。 “群集管理员 Azure AD 组”显示在门户中的“配置”选项卡上（或在 Azure CLI 中通过参数名称 `--aad-admin-group-object-ids` 指定）。

| 说明        | 需要的角色授予| 群集管理员 Azure AD 组 | 何时使用 |
| -------------------|------------|----------------------------|-------------|
| 使用客户端证书的旧管理员登录名| Azure Kubernetes 管理员角色。 此角色允许在使用 `--admin` 标志的情况下使用 `az aks get-credentials`，以便将[旧的（非 Azure AD）群集管理证书](control-kubeconfig-access.md)下载到用户的 `.kube/config` 中。 这是“Azure Kubernetes 管理员角色”的唯一用途。|不适用|如果你被永久阻止，不能访问对你的群集具有访问权限的有效 Azure AD 组。| 
| 具有手动 (Cluster)RoleBinding 的 Azure AD| Azure Kubernetes 用户角色。 “用户”角色允许在不使用 `--admin` 标志的情况下使用 `az aks get-credentials`。 （这是“Azure Kubernetes 用户角色”的唯一用途。）因此，在启用了 Azure AD 的群集上，[一个空条目](control-kubeconfig-access.md)会下载到 `.kube/config` 中，在首次被 `kubectl` 使用时会触发基于浏览器的身份验证。| 用户不在这些组的任何一个中。 因为用户不在任何群集管理员组中，所以其权限将完全由群集管理员已设置的任何 RoleBinding 或 ClusterRoleBinding 控制。 (Cluster)RoleBinding [指定 Azure AD 用户或 Azure AD 组](azure-ad-rbac.md)作为其 `subjects`。 如果未设置此类绑定，则用户将无法执行任何 `kubectl` 命令。|如果你想要进行精细的访问控制，并且不使用 Azure RBAC 进行 Kubernetes 授权。 请注意，设置绑定的用户必须通过此表中列出的其他方法之一进行登录。|
| 由管理员组成员使用的 Azure AD| 同上|用户是此处列出的其中一个组的成员。 AKS 会自动生成一个 ClusterRoleBinding，后者将所有列出的组绑定到 `cluster-admin` Kubernetes 角色。 因此，这些组中的用户可以作为 `cluster-admin` 运行所有 `kubectl` 命令。|如果你希望方便地向用户授予完全的管理员权限，并且不使用 Azure RBAC 进行 Kubernetes 授权。|
| 将 Azure AD 与 Azure RBAC 配合使用进行 Kubernetes 授权|两个角色：第一个为 Azure Kubernetes 用户角色（如上所述）。 第二个为上面列出的“Azure Kubernetes 服务 RBAC...”角色之一，或你自己的自定义替代项。|启用了“使用 Azure RBAC 进行 Kubernetes 授权”时，“配置”选项卡上的管理员角色字段不相关。|你使用 Azure RBAC 进行 Kubernetes 授权。 此方法提供了精细的控制，无需设置 RoleBinding 或 ClusterRoleBinding。|

## <a name="next-steps"></a>后续步骤

- 若要开始使用 Azure AD 和 Kubernetes RBAC，请参阅[将 Azure Active Directory 与 AKS 集成][aks-aad]。
- 有关相关的最佳做法，请参阅[在 AKS 中进行身份验证和授权的最佳做法][operator-best-practices-identity]。
- 若要开始使用 Azure RBAC 进行 Kubernetes 授权，请参阅 [使用 AZURE rbac 在 Azure Kubernetes Service (AKS) 群集中授予访问权限](manage-azure-rbac.md)。
- 要开始保护 kubeconfig 文件，请参阅[限制对群集配置文件的访问](control-kubeconfig-access.md)

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS 规模][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
