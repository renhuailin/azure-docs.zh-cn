---
title: 管理身份的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关为 Azure Kubernetes 服务 (AKS) 中的群集管理身份验证和授权的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: a29bd1513f021be03cf6c6bd4aa83d13062de170
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181517"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的身份验证和授权的最佳做法

在 Azure Kubernetes 服务 (AKS) 中部署和维护群集时，需要实施相应的方法来管理对资源和服务的访问。 如果没有这些控件：
* 帐户可以访问不必要的资源和服务。 
* 跟踪用于作出更改的凭据集可能比较困难。

本最佳做法文章重点介绍群集操作员如何管理 AKS 群集的访问和标识。 在本文中，学习如何：

> [!div class="checklist"]
>
> * 使用 Azure Active Directory 对 AKS 群集用户进行身份验证。
> * 使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 控制对资源的访问权限。
> * 使用 Azure RBAC 大规模精确控制对 AKS 资源和 Kubernetes API 的访问权限，以及对 `kubeconfig` 的访问权限。
> * 使用托管标识在其他服务中对 Pod 本身进行身份验证。

## <a name="use-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD)

> **最佳实践指南** 
> 
> 使用 Azure AD 集成部署 AKS 群集。 使用 Azure AD 可以集中化标识管理组件。 访问 AKS 群集时，用户帐户或组状态的任何更改会自动更新。 使用[角色、群集角色或绑定](#use-kubernetes-role-based-access-control-kubernetes-rbac)将用户或组范围限制为最小权限量。

Kubernetes 群集的开发人员和应用程序所有者需要访问不同的资源。 Kubernetes 缺少一个标识管理解决方案，无法让你控制用户可与之交互的资源。 通常，你会将群集与现有的标识解决方案相集成。 进入 Azure AD：与 AKS 群集集成的企业就绪标识管理解决方案。

通过使用 AKS 中与 Azure AD 集成的群集，创建“角色”或“群集角色”，用于定义对资源的访问权限。 然后，从 Azure AD 将角色绑定到用户或组。 在[下一节](#use-kubernetes-role-based-access-control-kubernetes-rbac)中详细了解这些 Kubernetes RBAC。 下图显示了 Azure AD 集成，以及如何控制对资源的访问：

![与 AKS 集成的 Azure Active Directory 的群集级身份验证](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 开发人员在 Azure AD 中进行验证身份。
1. Azure AD 令牌颁发终结点颁发访问令牌。
1. 开发人员使用 Azure AD 令牌执行操作，例如 `kubectl create pod`。
1. Kubernetes 使用 Azure AD 验证令牌，并提取开发人员的组成员身份。
1. 同时应用了 Kubernetes RBAC 和群集策略。
1. 开发人员请求的成功与否，取决于上述 Azure AD 组成员身份的验证结果以及 Kubernetes RBAC 和策略。

若要创建使用 Azure AD 的 AKS 群集，请参阅[将 Azure Active Directory 与 AKS 集成][aks-aad]。

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)

> **最佳实践指南**
> 
> 使用 Kubernetes RBAC 定义用户或组对群集资源的权限。 创建角色和绑定，用于分配所需的最少量权限。 与 Azure AD 集成，以自动更新任何用户状态或组成员身份的更改，并保持对当前群集资源的访问权限。

在 Kubernetes 中，可以提供对群集资源的精确访问控制。 在群集级别或针对特定命名空间定义权限。 确定可使用哪些权限管理哪些资源。 然后，将这些角色应用到具有绑定的用户或组。 有关角色、群集角色和绑定的详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的访问和标识选项][aks-concepts-identity]。  

例如，可以创建一个角色，为其授予对“finance-app”命名空间所含资源的完整访问权限，如以下示例 YAML 清单中所示：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

然后创建一个“角色绑定”并为其绑定 Azure AD 用户“developer1\@contoso.com”，如以下 YAML 清单中所示：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

*developer1\@contoso.com* 通过 AKS 群集进行身份验证后，便对 *finance-app* 命名空间中的资源拥有了完全权限。 这样，即可以逻辑方式隔离和控制对资源的访问权限。 将 Kubernetes RBAC 与 Azure AD 集成结合使用。

若要了解如何使用 Azure AD 组通过 Kubernetes RBAC 来控制对 Kubernetes 资源的访问，请参阅[在 AKS 中使用基于角色的访问控制和 Azure Active Directory 标识来控制对群集资源的访问][azure-ad-rbac]。

## <a name="use-azure-rbac"></a>使用 Azure RBAC 
> **最佳实践指南** 
> 
> 使用 Azure RBAC 来定义用户或组对一或多个订阅中 AKS 资源所需拥有的最低权限。

完全操作 AKS 群集需要两个级别的访问权限： 
1. 访问 Azure 订阅上的 AKS 资源。 

    此访问级别允许：
      * 使用 AKS API 控制对群集的扩展或升级
      * 请求 `kubeconfig`。

    要查看如何控制对 AKS 资源和 `kubeconfig` 的访问权限，请参阅[限制对群集配置文件的访问](control-kubeconfig-access.md)。

2. 访问 Kubernetes API。 
    
    此访问级别由以下其中之一控制：
    * [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac)（传统做法）或 
    * 通过将 Azure RBAC 与 AKS 集成以实现 Kubernetes 授权。

    若要了解如何使用 Azure RBAC 向 Kubernetes API 精确授予权限，请参阅[使用 Azure RBAC 进行 Kubernetes 授权](manage-azure-rbac.md)。

## <a name="use-pod-managed-identities"></a>使用 Pod 托管标识

> **最佳实践指南** 
> 
> 不要在 Pod 或容器映像中使用固定的凭据，因为它们存在泄漏或滥用的风险。 请改用“Pod 标识”通过中心 Azure AD 标识解决方案来自动请求访问权限。 

> [!NOTE]
> Pod 标识仅适用于 Linux Pod 和容器映像。 对 Windows 容器的 Pod 托管标识支持即将推出。

Pod 需要获取访问凭据，才可访问其他 Azure 服务（例如 Cosmos DB、Key Vault 或 Blob 存储）。 可以使用容器映像定义访问凭据，或将其作为 Kubernetes 密码插入。 无论采用哪种方式，都需要手动创建和分配这些凭据。 这些凭据通常会在不同的 Pod 之间重复使用，并且不会定期轮换。

你可使用 Azure 资源的 Pod 托管标识，通过 Azure AD 自动请求对服务的访问权限。 Pod 托管标识目前可在 AKS 预览中查看。 若要开始使用，请参阅[在 Azure Kubernetes 服务中使用 Azure Active Directory Pod 托管标识（预览）](./use-azure-ad-pod-identity.md)文档。 

Azure Active Directory Pod 标识支持 2 种操作模式：

1. 标准模式：在此模式下，将以下 2 个组件部署到 AKS 群集： 
    * [托管标识控制器 (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/)：一种 Kubernetes 控制器，通过 Kubernetes API 服务器监视对 Pod、[AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) 和 [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) 所做的更改。 检测到相关更改时，MIC 会根据需要添加或删除 [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/)。 具体而言，如果计划了 Pod，MIC 会将 Azure 上的托管标识分配给节点池在创建阶段使用的基础 VMSS。 删除所有使用该标识的 Pod 时，MIC 会从节点池的 VMSS 中删除标识，除非其他 Pod 使用相同的托管标识。 创建或删除 AzureIdentity 或 AzureIdentityBinding 时，MIC 会执行类似的操作。
    * [节点托管标识 (NMI)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/)：是在 AKS 群集中每个节点上作为 DaemonSet 运行的 Pod。 NMI 截获每个节点上对 [Azure 实例元数据服务](../virtual-machines/linux/instance-metadata-service.md?tabs=linux)的安全令牌请求，将这些请求重定向到自身并验证 Pod 是否有权访问它为其请求令牌的标识，并代表应用程序从 Azure Active Directory 租户中提取令牌。
2. 托管模式：在此模式下，只有 NMI。 标识需要由用户手动分配和管理。 有关详细信息，请参阅[托管模式下的 Pod 标识](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/)。 在此模式下，使用 [az aks pod-identity add](/cli/azure/aks/pod-identity?view=azure-cli-latest#az_aks_pod_identity_add) 命令将 Pod 标识添加到 Azure Kubernetes 服务 (AKS) 群集时，它会在 `--namespace` 参数指定的命名空间创建 [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) 和 [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/)，而 AKS 资源提供程序将 `--identity-resource-id` 参数指定的托管标识分配给 AKS 群集中每个节点池的虚拟机规模集 (VMSS)。

> [!NOTE]
> 如果决定改为使用 [AKS 群集加载项](./use-azure-ad-pod-identity.md)安装 Azure Active Directory Pod 标识，安装程序将使用`managed`模式。

与`standard`模式相比，`managed`模式具备以下优点：

1. 节点池 VMSS 上的标识分配可能需要 40-60 秒。 对于需要访问标识且不能接受分配延迟的定时任务或应用程序，最好使用`managed`模式，因为标识已经手动或通过 [az aks pod-identity add](/cli/azure/aks/pod-identity?view=azure-cli-latest#az_aks_pod_identity_add) 命令预先分配给了节点池的 VMSS。
2. 在`standard`模式下，MIC 要求对 AKS 群集使用的 VMSS 具有写入权限和对用户分配的托管标识的`Managed Identity Operator`权限。 在`managed mode`下运行时，由于没有 MIC，因此不需要角色分配。

Pod 托管标识不会手动定义 Pod 凭据，而是会实时请求访问令牌，并使用该令牌访问仅为它们分配的服务。 在 AKS 中，有两个组件处理操作，以便 Pod 使用托管标识：

* **节点管理标识 (NMI) 服务器** 是在 AKS 群集中每个节点上作为守护程序集运行的 pod。 NMI 服务器侦听发送到 Azure 服务的 pod 请求。
* Azure 资源提供程序查询 Kubernetes API 服务器，并检查是否有与 Pod 相对应的 Azure 标识映射。

Pod 请求 Azure Active Directory 中的安全令牌以访问 Azure 服务时，网络规则会将流量重定向到 NMI 服务器。 
1. NMI 服务器：
    * 根据远程地址请求访问 Azure 服务的 Pod 标识。
    * 查询 Azure 资源提供程序。 
1. Azure 资源提供程序会检查 AKS 群集中的 Azure 标识映射。
1. NMI 服务器将根据 Pod 的标识映射从 Azure AD 中请求访问令牌。 
1. Azure AD 提供对 NMI 服务器的访问权限，该访问权限将返回给 pod。 
    * 然后，pod 可以使用此访问令牌请求对 Azure 中服务的访问权限。

在以下示例中，开发人员创建使用托管标识请求 Azure SQL 数据库访问权限的 Pod：

![pod 标识可让 pod 自动请求对其他服务的访问权限](media/operator-best-practices-identity/pod-identities.png)

1. 当 Pod 请求访问服务时，群集运算符会创建一个服务帐户来映射标识。
1. NMI 服务器与 Azure 资源提供程序一起部署，以将任何关于访问令牌的 Pod 请求中继到 Azure AD。
1. 开发人员使用托管标识部署一个 pod，该 pod 可通过 NMI 服务器请求访问令牌。
1. 该令牌将返回给 Pod，并用于访问 Azure SQL 数据库

> [!NOTE]
> Pod 托管标识目前处于预览状态。

若要使用 Pod 托管标识，请参阅[在 Azure Kubernetes 服务中使用 Azure Active Directory Pod 托管标识（预览）]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)。

## <a name="next-steps"></a>后续步骤

本最佳做法文章重点介绍了群集和资源的身份验证与授权。 若要实施其中的某些最佳做法，请参阅以下文章：

* [将 Azure Active Directory 与 AKS 集成][aks-aad]
* [在 Azure Kubernetes 服务中使用 Azure Active Directory Pod 托管标识（预览）]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [多租户和群集隔离][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]
* [高级 Kubernetes 计划程序功能][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md