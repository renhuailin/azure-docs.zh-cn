---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
ms.topic: troubleshooting
ms.date: 09/24/2021
ms.openlocfilehash: 16aa9482b9de779295732fef0f2b88fa348e9026
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707183"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答

在创建或管理 Azure Kubernetes 服务 (AKS) 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

请尝试 [Kubernetes 群集故障排除的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
还可尝试由 Microsoft 工程师发布的[故障排除指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，用于对 Pod、节点、群集和其他功能进行故障排除。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到 `quota exceeded` 错误。 我该怎么办？ 

 [请求更多核心](../azure-portal/supportability/regional-quota-requests.md)。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到 `insufficientSubnetSize` 错误。 我该怎么办？

此错误表示用于群集的子网在其 CIDR 中不再具有用于成功分配资源的可用 IP。 对于 Kubenet 群集，需要为群集中的每个节点提供足够的 IP 空间。 对于 Azure CNI 群集，需要为群集中的每个节点和 Pod 提供足够的 IP 空间。
阅读并详细了解如何[设计为 Pod 分配 IP 的 Azure CNI](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

这些错误还出现在 [AKS 诊断](concepts-diagnostics.md)中，该诊断主动显示子网大小不足等问题。

以下三 (3) 种情况会导致子网大小不足的错误：

1. AKS 缩放或 AKS 节点池缩放
   1. 如果使用的是 Kubenet，当 `number of free IPs in the subnet` 小于 `number of new nodes requested` 时就会发生这种情况。
   1. 如果使用的是 Azure CNI，当 `number of free IPs in the subnet` 小于 `number of nodes requested times (*) the node pool's --max-pod value` 时就会发生这种情况。

1. AKS 升级或 AKS 节点池升级
   1. 如果使用的是 Kubenet，当 `number of free IPs in the subnet` 小于 `number of buffer nodes needed to upgrade` 时就会发生这种情况。
   1. 如果使用的是 Azure CNI，当 `number of free IPs in the subnet` 小于 `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` 时就会发生这种情况。
   
   默认情况下，AKS 群集将最大激增（升级缓冲区）值设置为一 (1)，但可以通过设置节点池的最大激增值来自定义此升级行为，节点池的最大激增值将增加完成升级所需的可用 IP 数。

1. 创建 AKS 或添加 AKS 节点池
   1. 如果使用的是 Kubenet，当 `number of free IPs in the subnet` 小于 `number of nodes requested for the node pool` 时就会发生这种情况。
   1. 如果使用的是 Azure CNI，当 `number of free IPs in the subnet` 小于 `number of nodes requested times (*) the node pool's --max-pod value` 时就会发生这种情况。

通过创建新的子网，可以应用以下缓解措施。 由于无法更新现有子网的 CIDR 范围，因此需要获取创建新子网的权限才能应用缓解措施。

1. 使用足以实现操作目标的更大的 CIDR 范围来重建新子网：
   1. 创建具有所需的无重叠新范围的新子网。
   1. 在新子网上创建新的节点池。
   1. 从驻留在要替换的旧子网中的旧节点池中清空 Pod。
   1. 删除旧子网和旧节点池。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在 CrashLoopBackOff 模式。 我该怎么办？

可能有多种原因导致 Pod 停滞在该模式。 可能通过以下方式查看：

* 使用 `kubectl describe pod <pod-name>` 查看 Pod 本身。
* 使用 `kubectl logs <pod-name>` 查看日志。

要详细了解如何排除 pod 问题，请参阅 Kubernetes 文档中的[调试 Pod](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>当我使用连接到 API 服务器的 `kubectl` 或其他第三方工具时，会收到 `TCP timeouts`
AKS 具有 HA 控制平面，可以根据内核数进行垂直缩放，以确保维持其服务级别目标 (SLO) 和服务级别协议 (SLA)。 如果遇到连接超时，请检查以下内容：

- **是所有 API 命令一致超时还是只有少数几个超时？** 如果只是少数几个超时，那么负责节点与控制平面之间通信的 `tunnelfront` Pod 或 `aks-link` Pod 可能未运行。 确保托管此 Pod 的节点没有过度利用或压力过大。 考虑将它们移动到自己的 [`system` 节点池](use-system-pools.md)。
- 是否已打开 [AKS 限制出口流量文档](limit-egress-traffic.md)中注明的所有所需端口、FQDN 和 IP？ 否则，多个命令调用可能会失败。
- 当前的 IP 是否在 [API IP 授权范围](api-server-authorized-ip-ranges.md)内？ 如果正在使用此功能，而 IP 不在范围内，会导致调用被阻止。 
- 是否有客户端或应用程序泄漏了对 API 服务器的调用？ 确保使用监视而不是频繁的 get 调用，以及第三方应用程序不会泄漏此类调用。 例如，每次在内部读取机密时，Istio mixer 中的一个 bug 会导致创建新的 API 服务器监视连接。 因为这种行为是定期发生的，所以监视连接会迅速累积，最终导致 API 服务器过载，且不管采用什么扩展模式都会如此。 https://github.com/istio/istio/issues/19481
- Helm 部署中是否有多个版本？ 这种情况会导致 tiller 在节点上使用过多的内存，同时导致大量的 `configmaps`，这会在 API 服务器上造成不必要的峰值。 请考虑在 `helm init` 配置 `--history-max`，并利用新的 Helm 3。 有关以下问题的详细信息： 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- [节点之间的内部流量是否被阻止？](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>我收到 `TCP timeouts`，如 `dial tcp <Node_IP>:10250: i/o timeout`

这些超时可能与被阻止节点之间的内部流量有关。 验证此流量是否未被阻止，例如通过群集节点子网上的[网络安全组](concepts-security.md#azure-network-security-groups)来这样做。

## <a name="im-trying-to-enable-kubernetes-role-based-access-control-kubernetes-rbac-on-an-existing-cluster-how-can-i-do-that"></a>我想尝试在现有群集上启用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 该如何操作？

目前不支持在现有群集上启用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)，必须在创建新群集时对其进行设置。 在使用 CLI、门户或 `2020-03-01` 之后的 API 版本时，默认会启用 Kubernetes RBAC。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>无法使用 Kubectl 日志获取日志或无法连接到 API 服务器。 我收到“来自服务器的错误：拨号后端时出错: 拨打 tcp...”。 我该怎么办？

确保端口 22、9000 和 1194 已打开，以便连接到 API 服务器。 使用 `kubectl get pods --namespace kube-system` 命令检查 `tunnelfront` 或 `aks-link` Pod 是否正在 kube-system 命名空间中运行。 如果没有，请强制删除 Pod，它会重启。

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>当连接到 AKS API 时，我从客户端收到 `"tls: client offered only unsupported versions"`。 我该怎么办？

AKS 支持的最低 TLS 版本是 TLS 1.2。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我在尝试进行升级或缩放时收到 `"Changing property 'imageReference' is not allowed"` 错误。 如何修复此问题？

收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改或删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 更改 AKS 群集中 MC_ * 组下的资源会中断服务级别目标 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>收到错误消息，指示群集处于失败状态，在修复该错误之前，将无法执行升级或缩放操作

以下故障排除帮助参考自 https://aka.ms/aks-cluster-failed

当群集由于多种原因进入失败状态时，会发生此错误。 请遵循以下步骤解决群集故障状态，然后重试先前失败的操作：

1. 除非群集摆脱 `failed` 状态，否则 `upgrade` 和 `scale` 操作不会成功。 常见的根本问题和解决方法包括：
    * 使用 **不足的计算 (CRP) 配额** 进行缩放。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高计算配额](../azure-portal/supportability/regional-quota-requests.md)，然后尝试扩展到超出初始配额限制。
    * 使用高级网络和 **不足的子网（网络）资源** 缩放群集。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高资源配额](../azure-resource-manager/templates/error-resource-quota.md#solution)，然后尝试扩展到超出初始配额限制。
2. 解决升级失败的根本原因后，重试原始操作。 此重试操作应该能使群集变为成功状态。 

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>在尝试升级或缩放时收到错误，指示群集正在升级或升级失败

以下故障排除帮助参考自 https://aka.ms/aks-pending-upgrade

 不能同时升级和缩放群集或节点池。 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 因此，在进行或尝试进行活动升级或缩放操作时，操作会受限。 

为帮助诊断此问题，请运行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 以检索群集的详细状态。 根据结果执行相应的操作：

* 如果群集正在升级，请等待操作完成。 如果群集升级成功，请再次重试先前失败的操作。
* 如果群集升级失败，请按前面部分所述的步骤操作。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>是否可以将我的群集移到不同的订阅，或将包含我的群集的订阅移动到新的租户？

如果已将 AKS 群集移动到其他订阅，或已将群集的订阅移动到新租户，群集会因缺少群集标识权限而无法正常工作。 由于存在此约束，因此，AKS 不支持跨订阅或租户移动群集。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>在尝试使用需要虚拟机规模集的功能时遇到错误

以下故障排除帮助参考自 aka.ms/aks-vmss-enablement

你可能会收到错误，指示 AKS 群集不在虚拟机规模集上，如以下示例中所示：

AgentPool `<agentpoolname>` 已将自动缩放设置为启用状态，但它不在虚拟机规模集上

群集自动缩放程序或多节点池等功能需要 `vm-set-type` 的规模集。

按照相应文档中的“准备工作”步骤操作，正确创建 AKS 群集：

* [使用群集自动缩放程序](cluster-autoscaler.md)
* [创建和使用多个节点池](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>对 AKS 资源和参数强制实施哪些命名限制？

*此故障排除帮助来自 aka.ms/aks-naming-rules*

Azure 平台和 AKS 都实施了命名限制。 如果资源名称或参数违反了这些限制之一，则会返回一个错误，要求你提供不同的输入。 将应用以下通用命名规则：

* 群集名称必须为 1-63 个字符。 仅允许使用字母、数字、短划线和下划线字符。 第一个和最后一个字符必须是字母或数字。
* AKS 节点/MC_ 资源组名称由资源组名称和资源名称组成。 自动生成的 `MC_resourceGroupName_resourceName_AzureRegion` 语法长度不能超过 80 个字符。 如果需要，请缩短资源组名称或 AKS 群集名称的长度。 也可以[自定义节点资源组名称](cluster-configuration.md#custom-resource-group-name)
* dnsPrefix 必须以字母数字值开头和结尾，并且必须介于 1-54 个字符之间。 有效字符包括字母数字值和连字符 (-)。 dnsPrefix 不能包含特殊字符，例如句点 (.)。
* AKS 节点池名称必须全部为小写形式，对于 Linux 节点池，长度为 1-11 个字符；对于 Windows 节点池，长度为 1-6 个字符。 名称必须以字母开头，并且仅允许使用字母和数字字符。
* admin-username（用于设置 Linux 节点的管理员用户名）必须以字母开头，只能包含字母、数字、连字符和下划线，其最大长度为 64 个字符。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我在尝试创建、更新、缩放、删除或升级群集时收到错误，该操作不被允许，因为另一个操作正在进行。

以下故障排除帮助参考自 aka.ms/aks-pending-operation

当上一个操作仍在进行时，群集操作会受到限制。 若要检索群集的详细状态，请使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 根据需要使用自己的资源组和 AKS 群集名称。

根据群集状态输出执行相应操作：

* 如果群集处于除“成功”或“失败”状态以外的任何预配状态，请等待操作（升级/更新/创建/缩放/删除/迁移）完成。 在上一个操作完成后，重试最新的群集操作。

* 如果群集升级失败，请按照[收到错误消息，指示群集处于失败状态，在修复该错误之前，将无法执行升级或缩放操作](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)中所述的步骤进行操作。

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>在尝试创建新群集时收到错误消息，指示找不到服务主体或服务主体无效。

创建 AKS 群集时，需要使用服务主体或托管标识代表本人创建资源。 AKS 可以在创建群集时自动创建新的服务主体，也可以接收现有服务主体。 如果使用自动创建的服务主体，Azure Active Directory 需要将其传播到每个区域，以便创建成功。 如果传播时间过长，群集创建验证将失败，因为它无法找到可用的服务主体来执行此操作。 

对于此问题，请使用以下解决方法：
* 使用现有服务主体，该主体已跨区域传播，并可在群集创建时传入 AKS。
* 如果使用自动化脚本，请在创建服务主体和创建 AKS 群集之间增加延迟时间。
* 如果使用 Azure 门户，请在创建期间返回到群集设置，然后在几分钟后重试验证页面。

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>使用 AKS API 时，我收到 `"AADSTS7000215: Invalid client secret is provided."`。   应采取何种操作？

此问题是由于服务主体凭据过期而引起的。 [更新 AKS 群集的凭据。](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>使用 API 服务器授权的 IP 范围时，无法从“自动化/开发计算机/工具”访问我的群集 API。 如何修复此问题？

若要解决此问题，请确保 `--api-server-authorized-ip-ranges` 包括所使用的自动化/开发/工具系统的 IP 或 IP 范围。 请参阅[使用经授权的 IP 地址范围保护对 API 服务器的访问](api-server-authorized-ip-ranges.md)中的“如何查找我的 IP”部分。

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>我无法在 Azure 门户的 Kubernetes 资源查看器中查看配置了 API 服务器授权 IP 范围的群集的资源。 如何修复此问题？

[Kubernetes 资源查看器](kubernetes-portal.md)需要 `--api-server-authorized-ip-ranges` 来包含对本地客户端计算机或 IP 地址范围（在此范围内浏览门户）的访问权限。 请参阅[使用经授权的 IP 地址范围保护对 API 服务器的访问](api-server-authorized-ip-ranges.md)中的“如何查找我的 IP”部分。

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>在限制出口流量后收到错误消息

限制来自 AKS 群集的出口流量时，需要遵循针对 AKS 的[必需和可选的建议](limit-egress-traffic.md)出站端口/网络规则和 FQDN/应用程序规则。 如果你的设置与以上任意规则冲突，某些 `kubectl` 命令将无法正常运行。 在创建 AKS 群集时，也可能会遇到错误。

确认你的设置不与必需或可选的建议出站端口/网络规则和 FQDN/应用程序规则相冲突。

## <a name="im-receiving-429---too-many-requests-errors"></a>我收到“429 - 请求过多”错误

当 Azure 上的 Kubernetes 群集（AKS 或非 AKS）频繁执行纵向扩展/缩减操作或使用群集自动缩放程序 (CA) 时，这些操作可能导致大量 HTTP 调用，这些 HTTP 调用会超过所分配的订阅配额，从而导致失败。 错误将如下所示

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

这些限制错误在[此处](../azure-resource-manager/management/request-limits-and-throttling.md)和[此处](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors)进行了详细说明

AKS 工程团队的建议是确保运行的版本至少是 1.18.x（其中包含许多改进功能）。 有关这些改进的更多详细信息，可参阅[此文](https://github.com/Azure/AKS/issues/1413)和[此文](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247)。

鉴于这些限制错误是在订阅级别测量的，在以下情况下它们仍可能发生：
- 有第三方应用程序在发出 GET 请求（如监视应用程序等）。 建议降低这些调用的频率。
- 有许多 AKS 群集/节点池在使用虚拟机规模集。 尝试将多个群集拆分为不同的订阅，特别是如果你希望它们处于非常活跃的状态（例如活动的群集自动缩放程序）或具有多个客户端（例如 rancher、terraform 等）。

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>无论我是否执行操作，群集的预配状态都会从“就绪”变为“失败”。 应采取何种操作？

如果无论你是否执行操作，群集的预配状态都从“就绪”变为“失败”，而群集上的应用程序仍在继续运行，则此问题可以由服务自动解决，应用程序应该不会受到影响。

如果群集的预配状态仍为“失败”或者群集上的应用程序停止工作，请[提交支持请求](https://azure.microsoft.com/support/options/#submit)。

## <a name="my-watch-is-stale-or-azure-ad-pod-identity-nmi-is-returning-status-500"></a>我的手表已过时或 Azure AD Pod 标识 NMI 返回状态 500

如果你像本[示例](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)那样使用 Azure 防火墙，则可能会遇到此问题，因为使用应用程序规则通过防火墙的长期 TCP 连接当前有一个 bug（将在 Q1CY21 中解决），这会导致 Go `keepalives` 在防火墙终止。 在解决此问题之前，可以通过将网络规则（而不是应用程序规则）添加到 AKS API 服务器 IP 来缓解此问题。

## <a name="when-resuming-my-cluster-after-a-stop-operation-why-is-my-node-count-not-in-the-autoscaler-min-and-max-range"></a>在停止操作后恢复群集时，为什么节点计数不在自动缩放程序最小和最大范围内？

如果使用的是群集自动缩放程序，则在启动群集备份时，当前节点计数可能不在所设置的最小和最大范围值之间。 这是预期的行为。 群集以运行其工作负载所需的节点数（不受自动缩放程序设置影响）开始。 当群集执行缩放操作时，最小值和最大值将影响当前节点计数，并且群集最终将进入并保持在所需范围内，直到停止群集为止。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 存储和 AKS 疑难解答

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在 Azure 磁盘的 mountOptions 中设置 uid 和 `GID` 失败

Azure 磁盘默认使用 ext4,xfs 文件系统，在装载时无法设置 uid=x,gid=x 之类的 mountOptions。 例如，如果尝试设置 mountpoptions uid=999、gid=999，将看到如下错误：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

可以通过执行以下选项之一来缓解此问题：

* 通过在 runAsUser 中设置 uid 和在 fsGroup 中设置 gid [为 Pod 配置安全上下文](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)。 例如，以下设置将 pod 设置为作为根运行，使其可供任何文件访问：

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

  >[!NOTE]
  > 因为 gid 和 uid 默认作为根或 0 装载。 如果 gid 或 uid 设置为非根（例如 1000），Kubernetes 将使用 `chown` 来更改该磁盘中的所有目录和文件。 此操作可能非常耗时，并可能导致磁盘装载速度变得很慢。

* 使用 initContainers 中的 `chown` 来设置 `GID` 和 `UID`。 例如：

```yaml
initContainers:
- name: volume-mount
  image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Azure 磁盘数目过大导致附加/分离速度缓慢

如果针对单个节点 VM 的 Azure 磁盘附加/分离操作次数超过 10 次，或针对单个虚拟机规模集池时的操作次数超过 3 次，则速度可能会比预期要慢，因为它们是按顺序执行的。 此问题是树中 Azure 磁盘驱动程序的已知限制。 [Azure 磁盘 CSI 驱动程序](https://github.com/kubernetes-sigs/azuredisk-csi-driver)通过在批处理操作中附加/分离磁盘解决了此问题。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁盘分离失败可能导致节点 VM 处于失败状态

在某些极端情况下，Azure 磁盘分离操作可能部分失败，导致节点 VM 处于故障状态。

如果节点处于失败状态，可以使用以下方式之一手动更新 VM 状态来缓解此问题：

* 对于基于可用性集的群集，使用以下代码：
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 对于基于 VMSS 的群集：
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 文件存储和 AKS 疑难解答

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 文件存储时的默认 mountOptions 是什么？

建议的设置：

| Kubernetes 版本 | fileMode 和 dirMode 值|
|--|:--:|
| 1.12.2 和更高版本 | 0777 |

可以对存储类对象指定装载选项。 以下示例设置 *0777*：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

其他一些有用的 *mountOptions* 设置：

* `mfsymlinks` 将使 Azure 文件存储装入点 (cifs) 支持符号链接
* `nobrl` 将阻止向服务器发送字节范围锁请求。 对于使用 cifs 样式的强制字节范围锁中断的某些应用程序，此设置是必需的。 大多数 cifs 服务器尚不支持请求建议字节范围锁。 如果不使用 nobrl，则使用 cifs 样式的强制字节范围锁中断的应用程序可能会导致以下类似错误消息：
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>使用 Azure 文件存储时出现“无法更改权限”错误

在 Azure 文件存储插件中运行 PostgreSQL 时，可能会出现如下所示的错误：

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

此错误是由使用 cifs/SMB 协议的 Azure 文件存储插件造成的。 使用 cifs/SMB 协议时，在装载后无法更改文件和目录权限。

若要解决此问题，请结合 Azure 磁盘插件使用 `subPath`。 

> [!NOTE] 
> 对于 ext3/4 磁盘类型，格式化磁盘后会出现一个 lost+found 目录。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>处理许多小型文件时，Azure 文件存储的延迟高于 Azure 磁盘

在某些情况下（例如处理许多的小型文件时），使用 Azure 文件存储时出现的延迟可能高于 Azure 磁盘。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>对存储帐户启用“允许从所选网络进行访问”设置时出错

如果在 AKS 中对用于动态预配的存储帐户启用“允许从所选网络进行访问”，则在 AKS 创建文件共享时会出错：

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

出现此错误是因为当设置“允许从所选网络进行访问”时，Kubernetes persistentvolume-controller 不在所选网络上。

可以通过[使用 Azure 文件存储静态预配](azure-files-volume.md)来缓解此问题。

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>由于存储帐户密钥已更改导致 Azure 文件存储装载失败

如果存储帐户密钥已更改，可能会遇到 Azure 文件存储装载失败。

可以使用 base64 编码的存储帐户密钥在 Azure 文件机密中手动更新 `azurestorageaccountkey` 字段，从而缓解此问题。

若要对存储帐户密钥进行 base64 编码，可以使用 `base64`。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

若要更新 Azure 机密文件，请使用 `kubectl edit secret`。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

几分钟后，代理节点将使用更新的存储密钥重新尝试装载 Azure 文件存储。


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>群集自动缩放程序无法缩放并显示错误：无法设置固定的节点组大小

如果群集自动缩放程序无法增大/缩小，则会在[群集自动缩放程序日志][view-master-logs]上看到如下错误。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

此错误是由于上游群集自动缩放程序争用条件导致的。 在这种情况下，集群自动缩放程序返回的值与群集中实际存在的值不同。 为此，请禁用[群集自动缩放程序][cluster-autoscaler]，然后再重新启用它。


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>为什么使用带有 kubernetes.io 前缀的节点标签时升级到 Kubernetes 1.16 失败

从 Kubernetes 1.16开始，kubelet [只能将已定义的带有 kubernetes.io 前缀的标签子集](https://v1-18.docs.kubernetes.io/docs/concepts/overview/working-with-objects/labels/)应用于节点。 未经许可，AKS 无法代表你删除活动标签，因为这可能导致受影响的工作负载发生故障。

因此，要缓解这种问题，可以执行以下操作：

1. 将群集控制平面升级到 1.16 或更高版本
2. 在 1.16 或更高版本上添加一个没有受支持的 kubernetes.io 标签的新 nodepoool
3. 删除较旧的节点池

AKS 正在研究对节点池上的活动标签进行改变的功能以改进这种缓解效果。



<!-- LINKS - internal -->
[view-master-logs]: monitor-aks-reference.md#resource-logs
[cluster-autoscaler]: cluster-autoscaler.md
