---
title: 从 Azure 门户访问 Kubernetes 资源
description: 了解如何与 Kubernetes 资源交互，从 Azure 门户管理 Azure Kubernetes 服务 (AKS) 群集。
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: d48a11a3897a4de8f613e015ffc87c84bea08950
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779095"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>从 Azure 门户访问 Kubernetes 资源

Azure 门户包含 Kubernetes 资源视图，可以让你轻松访问 Azure Kubernetes 服务 (AKS) 群集中的 Kubernetes 资源。 从 Azure 门户查看 Kubernetes 资源可减少 Azure 门户和 `kubectl` 命令行工具之间的上下文切换，从而简化了查看和编辑 Kubernetes 资源的体验。 资源查看器当前包括多个资源类型，例如部署、Pod 和副本集。

Azure 门户中的 Kubernetes 资源视图将替换已弃用的 [AKS 仪表板附加产品][kubernetes-dashboard]。

## <a name="prerequisites"></a>先决条件

若要查看 Azure 门户中的 Kubernetes 资源，需要一个 AKS 群集。 支持任何群集，但如果使用 Azure Active Directory (Azure AD) 集成，则群集必须使用 [AKS 托管的 Azure AD 集成][aks-managed-aad]。 如果你的群集使用旧版 Azure AD，则可以在门户中升级群集，或者使用 [Azure CLI][cli-aad-upgrade] 进行升级。 此外，还可以[使用 Azure 门户][portal-cluster]创建新 AKS 群集。

## <a name="view-kubernetes-resources"></a>查看 Kubernetes 资源

若要查看 Kubernetes 资源，请导航到 Azure 门户中的 AKS 群集。 使用左侧的导航窗格访问资源。 资源包括：

- “命名空间”显示群集的命名空间。 命名空间列表顶部的筛选器提供了一种快速筛选和显示命名空间资源的方法。
- “工作负荷”显示有关部署、Pod、副本集、StatefulSet、守护程序集、作业和部署到群集的 cron 作业的信息。 下面的屏幕截图显示了一个示例 AKS 群集中的默认系统 Pod。
- “服务和流入量”显示群集的所有服务和流入量资源。
- “存储”显示 Azure 存储类和永久卷信息。
- “配置”显示群集的 ConfigMap 和机密。

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Azure 门户中显示的 Kubernetes Pod 信息。" lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>部署应用程序

在此示例中，我们将使用示例 AKS 群集从 [AKS 快速入门][portal-quickstart]部署 Azure 投票应用程序。

1. 从任何资源视图（命名空间、工作负荷、服务和流入量、存储或配置）中选择“添加”。
1. 从 [AKS 快速入门][portal-quickstart]粘贴 Azure 投票应用程序的 YAML。
1. 选择 YAML 编辑器底部的“添加”，以部署应用程序。 

添加 YAML 文件后，资源查看器将显示已创建的 Kubernetes 服务：内部服务 (azure-vote-back)，和外部服务 (azure-vote-front) 来访问 Azure 投票应用程序。 外部服务包含链接的外部 IP 地址，因此你可以轻松地在浏览器中查看应用程序。

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure 门户中显示的 Azure 投票应用程序信息。" lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>监视部署见解

已启用[适用于容器的 Azure Monitor][enable-monitor]的 AKS 群集可快速查看部署和其他见解。 在 Kubernetes 资源视图中，用户可以查看各个部署的实时状态，包括 CPU 和内存使用情况，还可以转换到 Azure Monitor，以获取更深入的关于特定节点和容器的信息。 下面是一个示例 AKS 群集的部署见解示例：

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Azure 门户中显示的部署见解。" lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>编辑 YAML

Kubernetes 资源视图还包括一个 YAML 编辑器。 内置的 YAML 编辑器意味着可以从门户内更新或创建服务和部署，并立即应用更改。

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure 门户中显示的 Kubernetes 服务的 YAML 编辑器。":::

编辑 YAML 后，通过依次选择“查看”和“保存”，确认更改，然后再次保存来应用更改。

>[!WARNING]
> 不建议通过 UI 或 CLI 执行直接生产更改，应该利用[持续集成 (CI) 和持续部署 (CD) 最佳做法](kubernetes-action.md)。 Azure 门户 Kubernetes 管理功能和 YAML 编辑器在开发和测试设置中旨在用于学习和测试新部署。

## <a name="troubleshooting"></a>疑难解答

本部分介绍常见问题和故障排除步骤。

### <a name="unauthorized-access"></a>未授权的访问

若要访问 Kubernetes 资源，必须有权访问 AKS 群集、Kubernetes API 和 Kubernetes 对象。 确保你是群集管理员或具有访问 AKS 群集的适当权限的用户。 有关群集安全性的详细信息，请参阅 [AKS 的访问权限和标识选项][concepts-identity]。

>[!NOTE]
> Azure 门户中的 Kubernetes 资源视图仅受[已启用托管 AAD 的群集](managed-aad.md)或未启用 AAD 的群集的支持。 如果你使用的是已启用托管 AAD 的群集，则你的 AAD 用户或标识除了需要拉取 [user `kubeconfig`](control-kubeconfig-access.md) 的权限外，还需要具有各自的角色/角色绑定来访问 Kubernetes API。

### <a name="enable-resource-view"></a>启用资源视图

对于现有群集，可能需要启用 Kubernetes 资源视图。 若要启用资源视图，请按照群集的门户中的提示进行操作。

> [!TIP]
> 可以添加 [API 服务器已授权 IP 范围](api-server-authorized-ip-ranges.md) AKS 功能，以便限制 API 服务器仅访问防火墙的公共终结点。 此类群集的另一个选项是更新 `--api-server-authorized-ip-ranges`，以包括（从所浏览的门户）访问本地客户端计算机或 IP 地址范围的权限。 要允许此访问，需要计算机的公共 IPv4 地址。 可通过以下命令或在 Internet 浏览器中搜索“我的 IP 地址是多少”来查找此地址。
```bash
# Retrieve your IP address
CURRENT_IP=$(dig +short myip.opendns.com @resolver1.opendns.com)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>后续步骤

本文介绍了如何访问 AKS 群集的 Kubernetes 资源。 请参阅[部署和 YAML 清单][deployments]，进一步了解群集资源以及使用 Kubernetes 资源查看器访问的 YAML 文件。

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md
