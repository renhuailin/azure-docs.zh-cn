---
title: 有关群集安全性的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关如何在 Azure Kubernetes 服务 (AKS) 中管理群集安全性和升级的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 7560e9aaabf8b21729e1e9d8e008c0b6a0e8cefb
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453315"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的群集安全性和升级的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，关键是要确保工作负荷和数据的安全性。 在使用逻辑隔离运行多租户群集时，尤其需要保护对资源和工作负荷的访问。 通过应用最新的 Kubernetes 和节点 OS 安全更新来最大程度地降低攻击风险。

本文重点介绍如何保护 AKS 群集。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure Active Directory 和 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 来保护对 API 服务器的访问。
> * 保护容器对节点资源的访问。
> * 将 AKS 群集升级到最新的 Kubernetes 版本。
> * 使节点保持最新状态并自动应用安全修补程序。

还可以阅读有关[容器映像管理][best-practices-container-image-management]和 [Pod 安全性][best-practices-pod-security]的最佳做法。

还可使用 [Azure Kubernetes 服务与安全中心的集成][security-center-aks]，帮助检测威胁和查看关于保护 AKS 群集的建议。

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>保护对 API 服务器和群集节点的访问

> **最佳实践指南** 
>
> 要保护群集，最重要方法的一种方法是保护对 Kubernetes API 服务器的访问。 要控制对 API 服务器的访问，需要将 Kubernetes RBAC 与 Azure Active Directory (Azure AD) 集成。 借助这些控制，可以像保护对 Azure 订阅的访问一样保护 AKS。

Kubernetes API 服务器为在群集中执行操作的请求提供单一连接点。 若要保护和审核对 API 服务器的访问，请限制访问权限并尽量提供最低权限级别。 虽然这种方法并不是 Kubernetes 独有的，但它在将 AKS 群集进行逻辑隔离以供多租户使用时特别重要。

AD 提供可与 AKS 群集集成的企业级标识管理解决方案。 由于 Kubernetes 不提供标识管理解决方案，因此你可以按粒度限制对 API 服务器的访问。 借助 AKS 中与 Azure AD 集成的群集，可以使用现有用户和组帐户向 API 服务器验证用户身份。

![用于 AKS 群集的 Azure Active Directory 集成](media/operator-best-practices-cluster-security/aad-integration.png)

使用 Kubernetes RBAC 和 Azure AD 集成，你可保护 API 服务器并提供限定范围的资源集（例如单个命名空间）所需的最少权限。 可以向不同的 Azure AD 用户或组授予不同的 Kubernetes 角色。 借助这些细化的权限，可以限制对 API 服务器的访问，并提供已执行操作的清晰审核线索。

推荐的最佳做法是使用组来提供对文件和文件夹的访问，而不是使用个人标识。 例如，使用 Azure AD 组成员身份（而不是单个用户）将用户绑定到 Kubernetes 角色。  当某个用户的组成员身份发生变化时，该用户对 AKS 群集的访问权限也会相应发生变化。 

同时，假设你将单个用户直接绑定到某个角色，并且用户的职责功能发生了变化。 虽然 Azure AD 组成员身份会更新，但他们对 AKS 群集的权限不会更新。 在这种情况下，该用户最终获得的权限将超过其所需要的权限。

有关 Azure AD 集成、Kubernetes RBAC 和 Azure RBAC 的详细信息，请参阅[有关 AKS 中身份验证和授权的最佳做法][aks-best-practices-identity]。

## <a name="secure-container-access-to-resources"></a>保护容器对资源的访问

> **最佳实践指南** 
> 
> 限制对容器可以执行的操作的访问。 提供最少的权限，并避免使用 root 访问权或特权提升。

与应该向用户或组授予所需最少权限的方式一样，也应将容器限制为只能访问它们所需的操作和进程。 为了尽量减少攻击风险，避免配置需要提升的权限或 root 访问权限的应用程序和容器。 

例如，在 Pod 清单中设置 `allowPrivilegeEscalation: false`。 这些内置于 Kubernetes 的 *Pod 安全性上下文*，可用于定义其他权限（例如要以其身份运行的用户或组）或者要公开的 Linux 功能。 有关更多最佳做法，请参阅[保护 Pod 对资源的访问][pod-security-contexts]。

若要更精确地控制容器操作，还可以使用内置 Linux 安全功能，例如 *AppArmor* 和 *seccomp*。 
1. 在节点级别定义 Linux 安全功能。
1. 通过 pod 清单实现功能。 

内置的 Linux 安全功能仅在 Linux 节点和 Pod 上提供。

> [!NOTE]
> 目前，Kubernetes 环境并不完全安全，因为可能存在恶意的多租户使用情况。 其他安全性功能（如 *AppArmor*、*seccomp*、*Pod 安全策略* 或用于节点的 Kubernetes RBAC）可有效阻止攻击。 
>
>若要在运行恶意多租户工作负荷时获得真正的安全性，应只信任虚拟机监控程序。 Kubernetes 的安全域成为整个群集，而不是单个节点。 
>
> 对于这些类型的恶意多租户工作负荷，应使用物理隔离的群集。

### <a name="app-armor"></a>App Armor

若要限制容器操作，可以使用 [AppArmor][k8s-apparmor] Linux 内核安全模块。 AppArmor 作为基础 AKS 节点 OS 的一部分提供，默认情况下处于启用状态。 可以创建 AppArmor 配置文件来限制读取、写入或执行等操作或者装载文件系统等系统功能。 默认 AppArmor 配置文件限制对各种 `/proc` 和 `/sys` 位置的访问，并提供一种在逻辑上将容器与基础节点隔离的方法。 AppArmor 适用于 Linux 上运行的任何应用程序，而不仅仅是 Kubernetes Pod。

![AKS 群集中用来限制容器操作的 AppArmor 配置文件](media/operator-best-practices-container-security/apparmor.png)

为了通过实际操作了解 AppArmor，以下示例将创建一个阻止写入文件的配置文件。 
1. 通过 [SSH][aks-ssh] 连接到 AKS 节点。
1. 创建一个名为 *deny-write.profile* 的文件。
1. 粘贴以下内容：

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

使用 `apparmor_parser` 命令添加 AppArmor 配置文件。 
1. 将配置文件添加到 AppArmor。
1. 指定在上一步中创建的配置文件的名称：

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    如果已正确分析配置文件并将其应用于 AppArmor，则不会显示任何输出，并且你将回到命令提示符。

1. 在本地计算机上，创建一个名为 *aks-apparmor.yaml* 的 Pod 清单。 此清单：
    * 为 `container.apparmor.security.beta.kubernetes` 定义一个注释。
    * 引用在前面的步骤中创建的 *deny-write* 配置文件。

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. 部署 Pod 后，验证 hello-apparmor Pod 是否显示为 blocked ：

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

有关 AppArmor 的详细信息，请参阅 [Kubernetes 中的 AppArmor 配置文件][k8s-apparmor]。

### <a name="secure-computing"></a>安全计算

AppArmor 适用于任何 Linux 应用程序，[seccomp（*安* 全 *计* 算）][seccomp]则在进程级别运行。 Seccomp 也是一个 Linux 内核安全模块，并由 AKS 节点所用的 Docker 运行时提供本机支持。 借助 seccomp，你可以限制容器进程调用。 通过以下方式，遵循授予容器运行所需要的最低权限的最佳做法：
* 定义时采用筛选器，以筛选允许或拒绝的操作。
* 在 pod YAML 清单内批注以与 seccomp 筛选器相关联。 

为了通过实际操作了解 seccomp，请创建一个筛选器来阻止更改文件权限。 
1. 通过 [SSH][aks-ssh] 连接到 AKS 节点。
1. 创建名为 */var/lib/kubelet/seccomp/prevent-chmod* 的 seccomp 筛选器。
1. 粘贴以下内容：

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    在版本 1.19 及更高版本中，需要配置以下各项：

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. 在本地计算机上，创建一个名为 *aks-seccomp.yaml* 的 Pod 清单并粘贴以下内容。 此清单：
    * 为 `seccomp.security.alpha.kubernetes.io` 定义一个注释。
    * 引用在上一步中创建的 *prevent-chmod* 筛选器。

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    在版本 1.19 及更高版本中，需要配置以下各项：

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. 使用 [kubectl apply][kubectl-apply] 命令部署示例 Pod：

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. 使用 [kubectl get pods][kubectl-get] 命令查看 Pod 状态。 
    * 该 Pod 报告一个错误。 
    * Seccomp 筛选器阻止 `chmod` 命令运行，如以下示例输出所示：    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

有关可用筛选器的详细信息，请参阅 [Seccomp security profiles for Docker][seccomp]（Docker 的 Seccomp 安全配置文件）。

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>定期更新到最新的 Kubernetes 版本

> **最佳实践指南** 
> 
> 若要及时了解新功能和 bug 修复，请定期升级 AKS 群集中的 Kubernetes 版本。

与更传统的基础结构平台相比，Kubernetes 发布新功能的速度更快。 Kubernetes 更新包括：
* 新增功能
* Bug 或安全修补程序 

新功能在变得 *稳定* 之前通常会经历 *alpha* 和 *beta* 状态。 稳定后，将正式发布，并建议用于生产环境。 在 Kubernetes 新功能发布周期内，你可以对 Kubernetes 进行更新，而不会经常遇到中断性变更，也无需调整部署和模板。

AKS 支持三个 Kubernetes 次要版本。 引入新的次要版本补丁后，将停止对最早次要版本和修补程序版本的支持。 系统会定期进行次要 Kubernetes 更新。 若要保持在支持范围内，请确保你具有检查必要升级的管理过程。 有关详细信息，请参阅[支持的 Kubernetes 版本 AKS][aks-supported-versions]。

若要检查可用于群集的版本，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令，如以下示例所示：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

然后，可以使用 [az aks upgrade][az-aks-upgrade] 命令升级 AKS 群集。 升级过程安全性：
* 一次在一个节点上进行隔离和排空。
* 计划剩余节点上的 pod。
* 部署运行最新 OS 和 Kubernetes 版本的新节点。

>[!IMPORTANT]
> 在开发测试环境中测试新的次要版本，并使用新的 Kubernetes 版本验证你的工作负载是否正常运行。 
>
> Kubernetes 可能会弃用 API（如工作负载所依赖的版本 1.16）。 将新版本投入生产时，请考虑[在单独的版本上使用多个节点池](use-multiple-node-pools.md)，并一次升级一个池，从而循序渐进地在整个群集中滚动更新。 如果运行多个群集，则每次升级一个群集，从而循序渐进地监视影响或更改。
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

有关 AKS 中的升级的详细信息，请参阅 [AKS 中支持的 Kubernetes 版本][aks-supported-versions]和[升级 AKS 群集][aks-upgrade]。

## <a name="process-linux-node-updates"></a>处理 Linux 节点更新

每天晚上，AKS 中的 Linux 节点都会通过其发行版更新通道获得安全补丁。 当在 AKS 群集中部署节点时，会​​自动配置此行为。 为了尽量减少对正在运行的工作负荷的中断和潜在影响，AKS 不会在安全修补程序或内核更新需要进行重启时自动重启节点。 有关如何处理节点重启的详细信息，请参阅[将安全更新和内核更新应用于 AKS 中的节点][aks-kured]。

### <a name="node-image-upgrades"></a>节点映像升级

无人参与的升级会将更新应用于 Linux 节点 OS，但用于为群集创建节点的节点映像将保持不变。 如果将新的 Linux 节点添加到你的群集，则原始映像将用于创建节点。 这个新节点将在每晚自动检查期间接收所有可用的安全更新和内核更新，但在所有检查和重启完成之前将保持未修补状态。 可以使用节点映像升级来检查和更新群集使用的节点映像。 有关节点映像升级的更多详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 节点映像升级][node-image-upgrade]。

## <a name="process-windows-server-node-updates"></a>处理 Windows 服务器节点更新

对于 Windows 服务器节点，定期执行节点映像升级操作，以安全隔离和排空 Pod 并部署更新后的节点。

<!-- EXTERNAL LINKS -->
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
[node-image-upgrade]: node-image-upgrade.md
