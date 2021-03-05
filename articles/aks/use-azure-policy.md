---
title: 使用 Azure 策略保护群集
description: 使用 Azure 策略来保护 Azure Kubernetes 服务 (AKS) 群集。
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193392"
---
# <a name="secure-your-cluster-with-azure-policy"></a>通过 Azure 策略保护群集

若要提高 Azure Kubernetes 服务的安全性 (AKS) 群集，可以使用 Azure 策略在群集上应用和强制实施内置安全策略。 [Azure 策略][azure-policy] 可帮助实施组织标准并大规模评估符合性。 安装 [适用于 AKS 的 Azure 策略外接程序][kubernetes-policy-reference]后，你可以将名为 "计划" 的单个策略定义或策略定义组应用 (有时称为 policysets) 到群集。 有关 AKS 策略和计划定义的完整列表，请参阅适用 [于 AKS 的 Azure 策略内置定义][aks-policies] 。

本文介绍如何将策略定义应用于群集，并验证是否强制执行这些分配。

## <a name="prerequisites"></a>先决条件

- 现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。
- 在 AKS 群集上安装的适用于 AKS 的 Azure 策略外接程序。 按照以下 [步骤安装 Azure 策略外接程序][azure-policy-addon]。

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>分配内置策略定义或计划

若要应用策略定义或计划，请使用 Azure 门户。

1. 导航到 Azure 门户中的 Azure 策略服务。
1. 在“Azure Policy”页面的左侧窗格中，选择“定义”。
1. 在 " **类别** " 下选择 `Kubernetes` 。
1. 选择要应用的策略定义或计划。 对于本示例，请选择该 `Kubernetes cluster pod security baseline standards for Linux-based workloads` 计划。
1. 选择“分配”。
1. 将 **范围** 设置为启用了 Azure 策略外接程序的 AKS 群集的资源组。
1. 选择 " **参数** " 页面并将 **效果** 从更新 `audit` 为 " `deny` 阻止新的部署违反基线计划"。 你还可以添加要从计算中排除的其他命名空间。 对于本示例，请保留默认值。
1. 依次选择 " **查看** " 和 **"创建"** ，提交策略分配。

## <a name="validate-a-azure-policy-is-running"></a>验证 Azure 策略是否正在运行

通过运行以下内容确认策略分配已应用于群集：

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> 策略分配 [最多可能需要20分钟才能同步][azure-policy-assign-policy] 到每个群集。

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

### <a name="validate-rejection-of-a-privileged-pod"></a>验证特权 pod 的拒绝

首先测试在使用 `privileged: true` 安全性上下文计划 Pod 时所发生的情况。 此安全性上下文会提升 Pod 的特权。 该计划不允许有特权箱，因此，请求将被拒绝，导致部署被拒绝。

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

使用 [kubectl apply][kubectl-apply] 命令创建 pod，并指定 YAML 清单的名称：

```console
kubectl apply -f nginx-privileged.yaml
```

如预期那样，无法计划 pod，如以下示例输出所示：

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod 不会到达计划阶段，因此在继续之前，没有要删除的资源。

### <a name="test-creation-of-an-unprivileged-pod"></a>测试非特权 Pod 的创建

在上面的示例中，容器映像自动尝试使用根将 NGINX 绑定到端口 80。 策略计划拒绝了此请求，因此无法启动 pod。 现在，让我们尝试在无需访问权限的情况下运行相同的 NGINX pod。

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

此示例显示了仅影响违反集合中策略的部署的基准计划。 允许的部署将继续工作。

使用 [kubectl delete][kubectl-delete] 命令删除 NGINX 非特权 Pod，并指定 YAML 清单的名称：

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>禁用策略或计划

删除基准计划：

1. 导航到 Azure 门户上的 "策略" 窗格。
1. 从左窗格中选择 " **分配** "。
1. 单击该计划旁边的 " **...** " 按钮。 `Kubernetes cluster pod security baseline standards for Linux-based workloads`
1. 选择 " **删除分配**"。

## <a name="next-steps"></a>后续步骤

有关 Azure 策略的工作方式的详细信息，请执行以下操作：

- [Azure Policy 概述][azure-policy]
- [适用于 AKS 的 Azure 策略计划和策略][aks-policies]
- 删除 [Azure 策略附加项][azure-policy-addon-remove]。

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
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
