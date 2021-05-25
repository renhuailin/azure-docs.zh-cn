---
title: 使用 Azure Policy 来保护群集
description: 使用 Azure Policy 来保护 Azure Kubernetes 服务 (AKS) 群集。
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777718"
---
# <a name="secure-your-cluster-with-azure-policy"></a>通过 Azure Policy 保护群集

若要提升 Azure Kubernetes 服务 (AKS) 群集的安全性，可以使用 Azure Policy 在群集上应用和强制实施内置的安全策略。 [Azure Policy][azure-policy] 可帮助实施组织标准并大规模评估合规性。 安装[适用于 AKS 的 Azure Policy 加载项][kubernetes-policy-reference]后，可以将单个策略定义或名为“initiatives”的策略定义组（有时名为“policysets”）应用到群集。 请参阅[适用于 AKS 的 Azure Policy 内置定义][aks-policies]，查看 AKS 策略和计划定义的完整列表。

本文介绍如何将策略定义应用于群集，并验证是否在强制执行这些分配。

## <a name="prerequisites"></a>必备条件

- 现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。
- AKS 群集上安装的适用于 AKS 的 Azure Policy 加载项。 按照以下[步骤安装 Azure Policy 加载项][azure-policy-addon]。

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>分配内置策略定义或计划

通过使用 Azure 门户来应用策略定义或计划。

1. 导航到 Azure 门户中的 Azure Policy 服务。
1. 在“Azure Policy”页面的左侧窗格中，选择“定义”。
1. 在“类别”下，选择 `Kubernetes`。
1. 选择要应用的策略定义或计划。 对于此示例，选择 `Kubernetes cluster pod security baseline standards for Linux-based workloads` 计划。
1. 选择“分配”。
1. 将“范围”设置为启用了 Azure Policy 加载项的 AKS 群集的资源组。
1. 选择“参数”页面并将“效果”从 `audit` 更新为 `deny` 以阻止违反基线计划的新部署 。 还可以添加更多要从计算中排除的命名空间。 对于本示例，请保留默认值。
1. 依次选择“查看 + 创建”和“创建”以提交策略分配 。

## <a name="validate-a-azure-policy-is-running"></a>验证是否有 Azure Policy 正在运行

通过运行以下内容来确认策略分配已应用于群集：

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> 策略分配可能需要[最多 20 分钟来同步][azure-policy-assign-policy]到每个群集。

输出应如下所示：

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>验证对特权 pod 的拒绝

首先测试在使用 `privileged: true` 安全性上下文计划 Pod 时所发生的情况。 此安全性上下文会提升 Pod 的特权。 该计划不允许特权 pod，因此将拒绝请求，从而导致部署被拒绝。

创建名为 `nginx-privileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，并指定 YAML 清单的名称：

```console
kubectl apply -f nginx-privileged.yaml
```

与预期一致，未能计划 Pod，如以下示例输出所示：

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod 不会到达计划阶段，因此在继续之前，没有要删除的资源。

### <a name="test-creation-of-an-unprivileged-pod"></a>测试非特权 Pod 的创建

在上面的示例中，容器映像自动尝试使用根将 NGINX 绑定到端口 80。 此请求被策略计划拒绝，pod 无法启动。 现在尝试在没有特权访问权限的情况下运行同一 NGINX Pod。

创建名为 `nginx-unprivileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，并指定 YAML 清单的名称：

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod 已成功进行计划。 使用 [kubectl get pods][kubectl-get] 命令检查 Pod 的状态时，Pod 为 Running：

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

此示例显示了只影响违反了集合中策略的部署的基线计划。 允许的部署将继续正常运行。

使用 [kubectl delete][kubectl-delete] 命令删除 NGINX 非特权 Pod，并指定 YAML 清单的名称：

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>禁用策略或计划

删除基线计划：

1. 导航到 Azure 门户上的“策略”窗格。
1. 选择左侧窗格中的“分配”。
1. 单击 `Kubernetes cluster pod security baseline standards for Linux-based workloads` 计划旁的“ **...** ”按钮。
1. 选择“删除分配”。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Policy 的工作原理：

- [Azure Policy 概述][azure-policy]
- [适用于 AKS 的 Azure Policy 计划和策略][aks-policies]
- 删除 [Azure Policy 加载项][azure-policy-addon-remove]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
