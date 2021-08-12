---
title: 在 Azure Kubernetes 服务 (AKS) 中使用 Pod 安全策略
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 PodSecurityPolicy 控制 Pod 许可
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 260b7a0811db4e3c33894d1b43d7fbd38c26214f
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108015325"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>预览 - 在 Azure Kubernetes 服务 (AKS) 中使用 Pod 安全策略保护群集

> [!WARNING]
> 本文档中所述的 Pod 安全策略（预览版）功能将从 Kubernetes 1.21 版开始[弃用](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/)，并在 1.25 版中删除。 随着 Kubernetes Upstream 接近该里程碑，Kubernetes 社区将努力记录可行的替代方案。 先前在没有针对客户的可行方案时才发布弃用公告。 现在，Kubernetes 社区正在研究替代方案，不再迫切需要在 Kubernetes 之前弃用。 
>
> 弃用 Pod 安全策略（预览版）之后，必须在使用已弃用功能的任何现有群集上禁用该功能，以执行将来的群集升级并保留在 Azure 支持范围内。

若要提高 AKS 群集的安全性，可以限制可进行计划的 Pod。 请求不允许的资源的 Pod 无法在 AKS 群集中运行。 可使用 Pod 安全策略定义此访问权限。 本文介绍如何在 AKS 中使用 Pod 安全策略来限制 Pod 的部署。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 Pod 安全策略，需要具有 aks-preview CLI 扩展版本 0.4.1 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 *aks-preview* Azure CLI 扩展，然后使用 [az extension update][az-extension-update] 命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>注册 Pod 安全策略功能提供程序

此文档和功能设置为在 2020 年 10 月 15 日弃用。

若要创建或更新 AKS 群集以使用 Pod 安全策略，请先对订阅启用功能标志。 若要注册 PodSecurityPolicyPreview 功能标志，请使用 [az feature register][az-feature-register] 命令，如以下示例所示：

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 安全策略概述

在 Kubernetes 群集中，当要创建资源时，会使用许可控制器截获对 API 服务器的请求。 许可控制器随后可以根据一组规则验证资源请求，或转变资源以更改部署参数。

PodSecurityPolicy 是一种许可控制器，用于验证 Pod 规范是否满足定义的要求。 这些要求可能会限制特权容器的使用、对特定类型存储的访问或是容器运行时可以采用的用户或组。 当尝试部署的资源的 Pod 规范不满足 Pod 安全策略中概述的要求时，会拒绝请求。 这种控制可在 AKS 群集中计划的 Pod 的功能可防止出现一些可能的安全漏洞或特权提升。

在 AKS 群集中启用 Pod 安全策略时，会应用一些默认策略。 这些默认策略提供现成体验来定义可以计划的 Pod。 但是在定义自己的策略之前，群集用户可能会在部署 Pod 时遇到问题。 推荐方法是：

* 创建 AKS 群集
* 定义自己的 Pod 安全策略
* 启用 Pod 安全策略功能

若要显示默认策略限制 Pod 部署的方式，在本文中，我们首先启用 Pod 安全策略功能，然后创建自定义策略。

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Pod 安全策略与 Azure Policy 之间的行为变更

下面汇总了 Pod 安全策略与 Azure Policy 之间的行为变更。

|方案| Pod 安全策略 | Azure Policy |
|---|---|---|
|安装|启用 Pod 安全策略功能 |启用 Azure Policy 加载项
|部署策略| 部署 Pod 安全策略资源| 在订阅或资源组范围分配 Azure 策略。 Azure Policy 加载项是 Kubernetes 资源应用程序所必需的。
| 默认策略 | 在 AKS 中启用 Pod 安全策略时，将应用默认的“特权”和“无限制”策略。 | 启用 Azure Policy 加载项不会应用任何默认策略。 必须在 Azure Policy 中显式启用策略。
| 谁可以创建和分配策略 | 群集管理员可创建 Pod 安全策略资源 | 用户必须在 AKS 群集资源组上至少具有“所有者”角色或“资源策略参与者”权限。 - 通过 API，用户可以在 AKS 群集资源范围分配策略。 用户应在 AKS 群集资源上至少具有“所有者”或“资源策略参与者”权限。 - 在 Azure 门户中，可以在管理组/订阅/资源组级别分配策略。
| 授权策略| 用户和服务帐户需要具有显式权限才能使用 Pod 安全策略。 | 无需执行其他分配，即可授权策略。 在 Azure 中分配策略后，所有群集用户都可以使用这些策略。
| 策略适用性 | 管理员用户不能执行 Pod 安全策略。 | 所有用户（管理员与非管理员）都将看到相同的策略。 未根据用户设置特殊场景。 可在命名空间级别排除策略应用。
| 策略范围 | Pod 安全策略未指定命名空间 | Azure Policy 使用的约束模板未指定命名空间。
| 拒绝/审核/变更操作 | Pod 安全策略仅支持拒绝操作。 可以变更创建请求上的默认值。 可以在执行更新请求期间进行验证。| Azure Policy 支持审核和拒绝操作。 变更尚不受支持，但已在计划中。
| Pod 安全策略符合性 | 不显示启用 Pod 安全策略之前已存在的 Pod 的符合性。 启用 Pod 安全策略之后创建的不符合策略的 Pod 将遭到拒绝。 | 应用 Azure 策略之前已存在的不符合策略的 Pod 将显示在策略冲突中。 如果策略设置了“拒绝”效果，则会拒绝启用 Azure 策略后创建的不符合策略的 Pod。
| 如何查看群集上的策略 | `kubectl get psp` | `kubectl get constrainttemplate` - 将返回所有策略。
| Pod 安全策略标准版 - 特权 | 启用此功能时，默认情况下会创建特权 Pod安全策略资源。 | 特权模式意味着无限制，因此它等效于没有 Azure 策略分配。
| [Pod 安全策略标准版 - 基线/默认值](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | 用户安装 Pod 安全策略基线资源。 | Azure Policy 提供了一个映射到基线 Pod 安全策略的[内置基线计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d)。
| [Pod 安全策略标准版 - 受限](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | 用户安装受限 Pod 安全策略资源。 | Azure Policy 提供了一个映射到受限 Pod 安全策略的[内置限制计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>对 AKS 群集启用 Pod 安全策略

可以使用 [az aks update][az-aks-update] 命令启用或禁用 Pod 安全策略。 以下示例对名为 myResourceGroup 的资源组中的群集名称 myAKSCluster  启用 Pod 安全策略。

> [!NOTE]
> 对于实际使用，在定义自己的自定义策略之前，不要启用 Pod 安全策略。 在本文中，首先启用 Pod 安全策略，以查看默认策略如何限制 Pod 部署。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>默认 AKS 策略

启用 Pod 安全策略时，AKS 会创建一个名为 privileged 的默认策略。 请勿编辑或删除默认策略。 而是应创建自己的策略来定义要控制的设置。 我们首先看一下这些默认策略是什么以及它们如何影响 Pod 部署。

若要查看可用的策略，请使用 [kubectl get psp][kubectl-get] 命令，如以下示例中所示

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

privileged Pod 安全策略会应用于 AKS 群集中任何经过身份验证的用户。 此分配由 ClusterRoles 和 ClusterRoleBindings 控制。 使用 [kubectl get rolebindings][kubectl-get] 命令，在 kube-system 命名空间中搜索 default:privileged: 绑定：

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

如以下简洁输出所示，psp:privileged ClusterRole 会分配给任何 system:authenticated 用户。 此功能可在不定义自己的策略的情况下，提供基本级别的特权。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

在开始创建自己的 Pod 安全策略之前，请务必了解这些默认策略如何与用户请求进行交互以计划 Pod。 在接下来的几节中，我们将计划一些 Pod 以查看这些默认策略的运行情况。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 群集中创建测试用户

默认情况下，使用 [az aks get-credentials][az-aks-get-credentials] 命令时，AKS 群集的管理员凭据会添加到 `kubectl` 配置中。管理员用户会绕过 Pod 安全策略的实施。 如果为 AKS 群集使用 Azure Active Directory 集成，则可以使用非管理员用户的凭据登录，以查看实际策略实施。 在本文中，我们在 AKS 群集中创建一个可使用的测试用户帐户。

使用 [kubectl create namespace][kubectl-create] 命令为测试资源创建名为 psp-aks 的示例命名空间。 然后，使用 [kubectl create serviceaccount][kubectl-create] 命令创建名为 nonadmin-user 的服务帐户：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下来，使用 [kubectl create rolebinding][kubectl-create] 命令为 nonadmin-user 创建 RoleBinding，以在命名空间中执行基本操作：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>为管理员和非管理员用户创建别名命令

若要突出显示使用 `kubectl` 时的常规管理员用户与在前面步骤中创建的非管理员用户之间的差异，请创建两个命令行别名：

* kubectl-admin 别名用于常规管理员用户，范围限定为 psp-aks 命名空间。
* kubectl-nonadminuser 别名用于在前面步骤中创建的 nonadmin-user 用户，范围限定为 psp-aks 命名空间。

创建这两个别名，如以下命令所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>测试特权 Pod 的创建

首先测试在使用 `privileged: true` 安全性上下文计划 Pod 时所发生的情况。 此安全性上下文会提升 Pod 的特权。 在显示默认 AKS Pod 安全策略的上一节时，privilege 策略应拒绝此请求。

创建名为 `nginx-privileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod 未能进行计划，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不会到达计划阶段，因此在继续之前，没有要删除的资源。

## <a name="test-creation-of-an-unprivileged-pod"></a>测试非特权 Pod 的创建

在上面的示例中，Pod 规范请求特权提升。 此请求被默认 privilege Pod 安全策略所拒绝，因此 Pod 未能进行计划。 现在我们尝试在没有特权提升请求的情况下运行相同 NGINX Pod。

创建名为 `nginx-unprivileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod 未能进行计划，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不会到达计划阶段，因此在继续之前，没有要删除的资源。

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>使用特定用户上下文测试 Pod 的创建

在上面的示例中，容器映像自动尝试使用根将 NGINX 绑定到端口 80。 此请求被默认 privilege Pod 安全策略所拒绝，因此 Pod 未能启动。 现在我们尝试使用特定用户上下文（如 `runAsUser: 2000`）运行相同 NGINX Pod。

创建名为 `nginx-unprivileged-nonroot.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Pod 未能进行计划，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod 不会到达计划阶段，因此在继续之前，没有要删除的资源。

## <a name="create-a-custom-pod-security-policy"></a>创建自定义 Pod 安全策略

现在你已了解默认 Pod 安全策略的行为，接下来我们提供一种方式使 nonadmin-user 可成功计划 Pod。

我们创建一个策略，用于拒绝请求特权访问的 Pod。 未显式限制其他选项（如 runAsUser 或允许的卷）。 这种类型的策略会拒绝特权访问请求，但在其他情况下会允许群集运行请求的 Pod。

创建名为 `psp-deny-privileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

使用 [kubectl apply][kubectl-apply] 命令创建策略，并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要查看可用的策略，请使用 [kubectl get psp][kubectl-get] 命令，如以下示例中所示。 将 psp-deny-privileged 策略与在前面示例中实施以创建 Pod 的默认 privilege 策略进行比较。 你的策略仅拒绝使用 PRIV 提升。 psp-deny-privileged 策略对用户或组没有任何限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允许用户帐户使用自定义 Pod 安全策略

在上一步中，创建了一个 Pod 安全策略以拒绝请求特权访问的 Pod。 若要允许使用该策略，请创建 Role 或 ClusterRole。 然后，使用 RoleBinding 或 ClusterRoleBinding 关联这些角色之一。

对于此示例，创建一个 ClusterRole，它允许使用在上一步中创建的 psp-deny-privileged 策略。 创建名为 `psp-deny-privileged-clusterrole.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

使用 [kubectl apply][kubectl-apply] 命令创建 ClusterRole，并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

现在，创建 ClusterRoleBinding 以使用在上一步中创建的 ClusterRole。 创建名为 `psp-deny-privileged-clusterrolebinding.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

使用 [kubectl apply][kubectl-apply] 命令创建 ClusterRoleBinding，并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一步中，对 AKS 群集启用了 Pod 安全策略功能。 建议做法是仅在定义了自己的策略之后，才启用 Pod 安全策略功能。 这是启用 Pod 安全策略功能的阶段。 已定义了一个或多个自定义策略，并且用户帐户已与这些策略关联。 现在可以安全地启用 Pod 安全策略功能，并最大限度地减少默认策略导致的问题。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>再次测试非特权 Pod 的创建

应用了自定义 Pod 安全策略，并绑定用户帐户以使用策略后，我们再次尝试创建非特权 Pod。 通过 [kubectl apply][kubectl-apply] 命令，使用相同 `nginx-privileged.yaml` 清单创建 Pod：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod 已成功进行计划。 使用 [kubectl get pods][kubectl-get] 命令检查 Pod 的状态时，Pod 为 Running：

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此示例演示如何创建自定义 Pod 安全策略，以便为不同用户或组定义对 AKS 群集的访问权限。 默认 AKS 策略会对 Pod 可以运行的内容提供严格控制，因此创建自己的自定义策略，以便正确定义所需限制。

使用 [kubectl delete][kubectl-delete] 命令删除 NGINX 非特权 Pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清理资源

若要禁用 Pod 安全策略，请再次使用 [az aks update][az-aks-update] 命令。 以下示例对名为 myResourceGroup 的资源组中的群集名称 myAKSCluster  禁用 Pod 安全策略：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下来，删除 ClusterRole 和 ClusterRoleBinding：

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

使用 [kubectl delete][kubectl-delete] 命令删除安全策略，并指定 YAML 清单的名称：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最后，删除 psp-aks 命名空间：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>后续步骤

本文演示了如何创建 Pod 安全策略以阻止使用特权访问。 策略可以强制实施许多功能，如卷或 RunAs 用户的类型。 有关可用选项的详细信息，请参阅 [Kubernetes Pod 安全策略参考文档][kubernetes-policy-reference]。

有关限制 Pod 网络流量的详细信息，请参阅[在 AKS 中使用网络策略保护 Pod 之间的流量][network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
