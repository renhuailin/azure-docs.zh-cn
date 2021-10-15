---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 查找有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
ms.topic: conceptual
ms.date: 05/23/2021
ms.custom: references_regions
ms.openlocfilehash: fdccee2795a4e1b2c967c53dc17d15a6520f4402
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546513"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 区域目前提供 AKS？

有关可用区域的完整列表，请参阅 [AKS 区域和可用性][aks-regions]。

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>能否跨区域分布 AKS 群集？

否。 AKS 群集是区域性资源，不能跨区域。 有关如何创建包括多个区域的体系结构的指南，请参阅[用于实现业务连续性和灾难恢复的最佳做法][bcdr-bestpractices]。

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>AKS 群集是否可以跨可用性区域？

可以。 在[支持可用性区域的区域][availability-zones]中，可以在一个或跨多个[可用性区域][az-regions]部署 AKS 群集。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>是否可以控制谁有权限访问 Kubernetes API 服务器？

可以。 可以通过以下两种方式限制对 API 服务器的访问：

- 如果希望保留 API 服务器的公共终结点但仅限对一组受信任的 IP 范围的访问，请使用 [API 服务器授权的 IP 范围][api-server-authorized-ip-ranges]。
- 如要仅允许从你的虚拟网络内访问 API 服务器，请使用[专用群集][private-clusters]。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>单个群集中 VM 的大小是否可以不同？

能，可以通过创建[多个节点池][multi-node-pools]来在 AKS 群集中使用不同虚拟机大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会按照夜间计划自动将安全修补程序应用于群集中的 Linux 节点。 但是，你需要负责确保这些 Linux 节点根据需要重新启动。 可以使用多个选项来重新启动节点：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集自动升级 [cordon 和 drain 节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新修补程序版本或 Kubernetes 次要版本将新节点联机。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 通过使用[节点映像升级](node-image-upgrade.md)。

### <a name="windows-server-nodes"></a>Windows Server 节点

对于 Windows Server 节点，Windows 更新不会自动运行和应用最新的更新。 在 Windows 更新的发布周期和你自己的验证过程中，你需要定期升级 AKS 群集以及群集中的 Windows Server 节点池。 此升级过程会创建运行最新 Windows Server 映像和修补程序的节点，然后删除旧节点。 有关此过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

### <a name="are-there-additional-security-threats-relevant-to-aks-that-customers-should-be-aware-of"></a>是否存在客户应注意的与 AKS 相关的其他安全威胁？

Microsoft 针对通过 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)等服务保护工作负载可以采取的额外操作提供了相关指导。 下面列出了客户应注意的与 AKS 和 Kubernetes 相关的其他安全威胁：

* [新的大规模市场活动目标 Kubeflow](https://techcommunity.microsoft.com/t5/azure-security-center/new-large-scale-campaign-targets-kubeflow/ba-p/2425750) - 2021 年 6 月 8 日

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

AKS 在多个 Azure 基础结构资源之上构建，包括虚拟机规模集、虚拟网络和托管磁盘。 因此，你可以在 AKS 提供的托管 Kubernetes 环境内利用 Azure 平台的众多核心功能。 例如，大多数 Azure 虚拟机类型都可直接用于 AKS，而 Azure 预留可用于自动接收这些资源的折扣。

要启用此体系结构，每个 AKS 部署都需跨越两个资源组：

1. 创建第一个资源组。 此组仅包含 Kubernetes 服务资源。 在部署期间，AKS 资源提供程序自动创建第二个资源组， 例如 MC_myResourceGroup_myAKSCluster_eastus。 如需了解如何指定第二个资源组的名称，请参阅下一节。
1. 第二个资源组（称为节点资源组）包含与该群集相关联的所有基础结构资源。 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 默认情况下，节点资源组的名称类似于 MC_myResourceGroup_myAKSCluster_eastus。 每当删除群集时，AKS 会自动删除节点资源组，因此，仅应对生命周期与群集相同的资源使用 AKS。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我是否可为 AKS 节点资源组提供自己的名称？

是的。 默认情况下，AKS 将节点资源组命名为 MC_resourcegroupname_clustername_location，但你也可以提供自己的名称。

若要自行指定一个资源组名称，请安装 [aks-preview][aks-preview-cli] Azure CLI 扩展版本 0.3.2 或更高版本。 使用 [az aks create][az-aks-create] 命令创建 AKS 群集时，请使用 `--node-resource-group` 参数并指定资源组的名称。 如果[使用 Azure 资源管理器模板][aks-rm-template]部署 AKS 群集，可以使用 nodeResourceGroup 属性定义资源组名称。

* 第二个资源组由订阅中的 Azure 资源提供程序自动创建。
* 只能在创建群集时指定自定义资源组名称。

请注意，对于节点资源组，不能执行以下操作：

* 不能为节点资源组指定现有资源组。
* 为节点资源组指定不同的订阅。
* 创建群集后更改节点资源组名称。
* 不能为节点资源组内的受管理资源指定名称。
* 不能修改或删除节点资源组内受管理资源中由 Azure 创建的标记。 （请参阅下一部分的附加信息。）

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>是否可以修改节点资源组中 AKS 资源的标记和其他属性？

如果修改或删除节点资源组中 Azure 创建的标记和其他资源属性，可能会出现意外的结果，例如缩放和升级错误。 使用 AKS，可以创建和修改由最终用户创建的自定义标记，还可以在[创建节点池](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)时添加这些标记。 例如，可以创建或修改标记，以分配业务单位或成本中心。 这也可以通过在托管资源组上创建具有作用域的 Azure 策略来实现。

但是，在 AKS 群集中的节点资源组下修改任何 Azure 在资源中创建的标记是不受支持的操作，这会中断服务级别目标 (SLO)。 有关详细信息，请参阅 [AKS 是否提供服务级别协议？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支持哪些 Kubernetes 许可控制器？ 是否可以添加或删除许可控制器？

AKS 支持以下[许可控制器][admission-controllers]：

- NamespaceLifecycle
- LimitRanger
- ServiceAccount
- DefaultStorageClass
- DefaultTolerationSeconds
- MutatingAdmissionWebhook
- ValidatingAdmissionWebhook
- ResourceQuota
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

目前无法修改 AKS 中的准入控制器列表。

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>是否可以在 AKS 上使用许可控制器 Webhook？

是的，可以在 AKS 上使用许可控制器 Webhook。 建议排除带有 control-plane 标记的内部 AKS 命名空间。 例如，可以将以下内容添加到 Webhook 配置：

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS 对 API 服务器出口设置了防火墙，因此需要能够从群集内部访问许可控制器 Webhook。

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>许可控制器 Webhook 是否会影响 kube 系统和内部 AKS 命名空间？

为了保护系统的稳定性，并防止自定义的许可控制器影响 kube 系统中的内部服务，我们在命名空间 AKS 中设置了一个 **许可执行程序**，它自动排除 kube 系统和 AKS 内部命名空间。 此服务确保自定义许可控制器不会影响在 kube 系统中运行的服务。

如果你有一个用于在 kube 系统上部署某些内容的关键用例（不建议这样做），并且需要使用自定义许可 Webhook 来涵盖该系统，则可添加以下标签或注释，这样许可执行程序就会忽略该系统。

标签：```"admissions.enforcer/disabled": "true"```，或注释：```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

AKS 目前尚未与 Azure Key Vault 本机集成。 但是，[适用于 CSI 机密存储的 Azure Key Vault 提供程序][csi-driver]支持从 Kubernetes pod 到 Key Vault 机密的直接集成。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

是的，可以在 AKS 运行 Windows Server 容器。 若要在 AKS 中运行 Windows Server 容器，需创建一个将 Windows Server 作为来宾 OS 运行的节点池。 Windows Server 容器只能使用 Windows Server 2019。 若要开始使用，请[创建包含单个节点池的 AKS 群集][aks-windows-cli]。

Windows Server 对节点池的支持具有一些限制，Kubernetes 项目中的上游 Windows Server 也具有这些限制。 有关这些限制的详细信息，请参阅[在 AKS 中使用 Windows Server 容器的一些限制][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

AKS 通过[运行时间 SLA][uptime-sla] 提供 SLA 保障（可选的附加功能）。 

默认提供的免费 SKU 没有关联的服务级别协议，但具有 99.5% 的服务级别目标 。 在升级、底层节点运行不正常、平台维护、应用程序请求使 API 服务器不堪重负等情况下，可能会出现暂时性连接问题。如果你的工作负载不容许 API 服务器重启，那么建议使用运行时间 SLA。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>是否可将 Azure 预留折扣应用于 AKS 代理节点？

AKS 代理节点按标准 Azure 虚拟机计费，因此，如果你已为在 AKS 中使用的 VM 大小购买了 [Azure 预留][reservation-discounts]，这些折扣会自动应用。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>我可以在 Azure 租户之间移动/迁移群集吗？

当前不支持在租户之间移动 AKS 群集。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>我可以在订阅之间移动/迁移群集吗？

目前不支持跨订阅移动群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以将 AKS 群集从当前的 Azure 订阅移到另一个订阅？

不支持在 Azure 订阅之间移动 AKS 群集及其关联的资源。

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>是否可以将我的 AKS 群集或 AKS 基础结构资源移到其他资源组，或将它们重命名？

不支持移动或重命名 AKS 群集及其关联的资源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>为何群集删除需要如此长的时间？

大多数群集是按用户请求删除的；某些情况下，尤其是在客户引入自己的资源组或执行跨 RG 任务的情况下，删除操作可能需要更多的时间，或者可能会失败。 如果在删除时出现问题，请仔细检查，确保没有在 RG 上进行锁定、RG 之外的任何资源均已取消与 RG 的关联，等等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果 Pod/部署处于“NodeLost”或“未知”状态，是否仍然可以升级群集？

可以，但是 AKS 不建议这样做。 升级应该在群集状态已知且正常的情况下完成。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我有一个群集的一个或多个节点处于“运行不正常”状态或关闭状态，是否可以进行升级？

否。请删除/移除任何处于故障状态的节点或因为其他原因从群集中移除的节点，然后再进行升级。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我运行了群集删除操作，但出现错误：`[Errno 11001] getaddrinfo failed`

这种情况最可能的原因是用户有一个或多个网络安全组 (NSG) 仍在使用并与群集相关联。  请将其移除，然后再次尝试删除操作。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我运行了升级，但现在我的 Pod 处于崩溃循环中，且就绪情况探测失败。

请确认你的服务主体尚未过期。  请参阅：[AKS 服务主体](./kubernetes-service-principal.md)和 [AKS 更新凭据](./update-credentials.md)。

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>我的群集在运行，但突然不能预配 LoadBalancer，不能装载 PVC，等等。

请确认你的服务主体尚未过期。  请参阅：[AKS 服务主体](./kubernetes-service-principal.md)和 [AKS 更新凭据](./update-credentials.md)。

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>能否将 AKS 群集缩放为零？
可以完全[停止正在运行的 AKS 群集](start-stop-cluster.md)，从而节省相应的计算成本。 此外，还可以选择[将所有的或特定的 `User` 节点池缩放或自动缩放](scale-cluster.md#scale-user-node-pools-to-0)为 0，以仅维护必要的群集配置。
你不能直接将[系统节点池](use-system-pools.md)缩放为零。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>是否可以使用虚拟机规模集 API 手动进行缩放？

否。使用虚拟机规模集 API 进行的缩放操作不受支持。 请使用 AKS API (`az aks scale`)。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>是否可以使用虚拟机规模集手动缩放为 0 个节点？

否。使用虚拟机规模集 API 进行的缩放操作不受支持。 可以使用 AKS API 缩放到零个非系统节点池，或改为[停止群集](start-stop-cluster.md)。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>是否可以停止或解除分配我的所有 VM？

虽然 AKS 有对抗此类配置并从其恢复的复原机制，但这不是受支持的配置。 改为[停止群集](start-stop-cluster.md)。

## <a name="can-i-use-custom-vm-extensions"></a>是否可以使用自定义 VM 扩展？

不可以，AKS 是托管服务，不支持操作 IaaS 资源。 若要安装自定义组件，请使用 Kubernetes API 和机制。 例如，使用 DaemonSets 安装所需组件。

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS 是否将任何客户数据存储在群集区域之外？

将客户数据存储到一个区域的功能目前仅适用于亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部区域（圣保罗州）。 对于其他所有区域，客户数据存储在以下地域。

## <a name="are-aks-images-required-to-run-as-root"></a>AKS 映像是否需要以根用户身份运行？

除了以下两个映像之外，其他 AKS 映像不需要以根用户身份运行：

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>什么是 Azure CNI 透明模式与桥模式？

从 v1.2.0 开始，Azure CNI 会将透明模式作为单租户 Linux CNI 部署的默认模式。 透明模式将替换桥模式。 在本部分，我们将详细讨论这两种模式的差异，以及在 Azure CNI 中使用透明模式的优点/限制。

### <a name="bridge-mode"></a>桥模式

顾名思义，桥模式 Azure CNI 会以“实时”方式创建一个名为“azure0”的 L2 桥。 所有主机端 Pod `veth` 对接口都会连接到此桥。 因此，Pod 到 Pod 内部 VM 通信和其余流量都通过此桥。 所涉及的桥是一个第 2 层虚拟设备，它自己无法接收或传输任何内容，除非你将一个或多个真实设备绑定到该桥。 因此，Linux VM 的 eth0 必须转换为“azure0”桥的下级。 这会在 Linux VM 中创建复杂的网络拓扑，一个征兆就是，CNI 必须处理其他网络功能，例如 DNS 服务器更新等。

:::image type="content" source="media/faq/bridge-mode.png" alt-text="桥模式拓扑":::

下面是桥模式下的 IP 路由设置示例。 不管节点有多少个 Pod，都只会有两个路由。 第一个路由：azure0 上除本地流量以外的所有流量都将通过 IP 为“src 10.240.0.4”（即节点主 IP）的接口进入子网的默认网关；第二个路由：从“10.20.x.x”Pod 空间到内核，由内核决定。

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>透明模式
透明模式采用直截了当的方法来设置 Linux 网络。 在此模式下，Azure CNI 不会更改 Linux VM 中 eth0 接口的任何属性。 这种对 Linux 网络属性更改最少的方法有助于减少群集在桥模式下可能会遇到的复杂极端情况问题。 在透明模式下，Azure CNI 会创建并添加那些将添加到主机网络的主机端 Pod `veth` 对接口。 内部 VM Pod 到 Pod 通信通过 CNI 会添加的 IP 路由进行。 基本上，Pod 到 Pod 通信通过第 3 层进行，Pod 通信通过 L3 路由规则进行路由。

:::image type="content" source="media/faq/transparent-mode.png" alt-text="透明模式拓扑":::

下面是透明模式的 IP 路由设置示例，每个 Pod 的接口都会连接一个静态路由，这样，目标 IP 为 Pod 的流量会直接发送到 Pod 的主机端 `veth` 对接口。

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>透明模式的优点

- 针对 `conntrack` DNS 并行争用情况提供缓解措施，避免 5 秒 DNS 延迟问题，无需设置节点本地 DNS（出于性能方面的原因，你仍可以使用节点本地 DNS）。
- 消除了 CNI 桥模式目前由于“实时”桥设置而引入的最初 5 秒 DNS 延迟。
- 桥模式下的极端情况之一是，Azure CNI 无法不断更新用户添加到 VNET 或 NIC 的自定义 DNS 服务器列表。 这会导致 CNI 仅选取 DNS 服务器列表的第一个实例。 此问题在透明模式下得到解决，因为 CNI 不更改任何 eth0 属性。 在[此处](https://github.com/Azure/azure-container-networking/issues/713)了解详细信息。
- 提供了更好的 UDP 流量处理，并且在 ARP 超时时可以缓解 UDP 数据风暴。在桥模式下，当桥在 VM 内部 Pod 到 Pod 通信中不知道目标 Pod 的 MAC 地址时，按照设计，这会导致所有端口都出现数据包风暴。 此问题在透明模式下得到解决，因为路径中没有 L2 设备。 在[此处](https://github.com/Azure/azure-container-networking/issues/704)了解详细信息。
- 与桥模式相比，在内部 VM Pod 到 Pod 通信中，透明模式在吞吐量和延迟方面的性能更佳。

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>当卷中有很多文件时，如何避免权限所有权设置缓慢问题？

通常情况下，如果 Pod 以非根用户（应为根用户）身份运行，则必须在 Pod 的安全上下文中指定一个 `fsGroup`，才能使该卷可供 Pod 读取和写入。 [此处](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)将更详细地介绍此要求。

但是设置 `fsGroup` 的一个影响是，每次装载卷时，Kubernetes 都必须通过 `chown()` 和 `chmod()` 递归卷内的所有文件和目录（下面提到的一些情况例外）。 即使卷的组所有权已经与请求的 `fsGroup` 匹配，也会发生这种情况，而对于包含大量小文件的较大卷来说，价格可能会非常高昂，这会导致 Pod 需要花费很长时间才能启动。 在 v1.20 之前，这种情况是一个已知问题，解决方法是将 Pod 设置为以根用户身份运行：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

此问题已由 Kubernetes v1.20 解决，有关详细信息，请参阅 [Kubernetes 1.20：批量许可更改的粒度控制](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/)。

## <a name="can-i-use-fips-cryptographic-libraries-with-deployments-on-aks"></a>是否可以将 FIPS 加密库用于 AKS 上的部署？

已启用 FIPS 的节点目前在基于 Linux 的节点池上提供预览版。 有关更多详细信息，请参阅[添加已启用 FIPS 的节点池（预览版）](use-multiple-node-pools.md#add-a-fips-enabled-node-pool-preview)。

## <a name="can-i-configure-nsgs-with-aks"></a>是否可以使用 AKS 配置 NSG？

AKS 不会将网络安全组 (NSG) 应用于其子网，也不会修改与该子网相关的任何 NSG。 AKS 只会修改 NIC 级别的 NSG。 如果要使用 CNI，还必须确保 NSG 中的安全规则允许节点和 pod CIDR 范围之间的通信。 如果要使用 kubenet，还必须确保 NSG 中的安全规则允许节点和 pod CIDR 之间的通信。 有关详细信息，请参阅[网络安全组](concepts-network.md#network-security-groups)。

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
