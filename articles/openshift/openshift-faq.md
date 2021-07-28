---
title: 有关 Azure Red Hat OpenShift 的常见问题解答
description: 下面是有关 Microsoft Azure Red Hat OpenShift 的常见问题解答
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a3721083e48774963cd761178abdb552c93b15c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634339"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift 常见问题解答

本文解答了有关 Microsoft Azure Red Hat OpenShift 的常见问题 (FAQ)。

## <a name="installation-and-upgrade"></a>安装和升级

### <a name="which-azure-regions-are-supported"></a>支持哪些 Azure 区域？

有关 Azure Red Hat OpenShift 4.x 支持区域的列表，请参阅[可用区域](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)。

有关 Azure Red Hat OpenShift 3.11 支持区域的列表，请参阅[各区域的产品可用性](supported-resources.md#azure-regions)。

### <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虚拟机大小？

有关 Azure Red Hat OpenShift 4 支持的虚拟机大小列表，请参阅 [Azure Red Hat OpenShift 4 支持的资源](support-policies-v4.md)。

有关 Azure Red Hat OpenShift 3.11 支持的虚拟机大小列表，请参阅 [Azure Red Hat OpenShift 3.11 支持的资源](supported-resources.md)。

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 群集中的 Pod 数量上限是多少？  Azure Red Hat OpenShift 群集中的 Pod 数量上限是多少？

实际受支持的 Pod 的数量取决于应用程序的内存、CPU 和存储要求。

Azure Red Hat OpenShift 4.x 的上限是每个节点 250 个 Pod 以及 100 个计算节点。 一个群集中支持的 Pod 最大数量是 250 &times; 100 = 25,000 个。

Azure Red Hat OpenShift 3.11 的上限是每个节点 50 个 Pod 以及 20 个计算节点。 一个群集中支持的 Pod 最大数量是 50 &times; 20 = 1,000 个。

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>一个群集中能否包含跨多个 Azure 区域的计算节点？

不知道。 Azure Red Hat OpenShift 群集中的所有节点都必须来源于同一 Azure 区域。

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>是否可以跨多个可用性区域部署群集？

是的。 如果将群集部署到支持可用性区域的 Azure 区域中，这种情况将会自动发生。 有关详细信息，请参阅[可用性区域](../availability-zones/az-overview.md#availability-zones)。

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>控制平面节点是否像 Azure Kubernetes 服务 (AKS) 一样被抽象出来？

不知道。 所有资源（包括群集主节点）都是在客户订阅中运行。 这些类型的资源位于只读资源组中。

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>群集是否驻留在客户订阅中？ 

Azure 托管应用程序与客户订阅一道位于锁定的资源组中。 客户可以查看该资源组中的对象，但不能对其进行修改。

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Azure Red Hat OpenShift 中是否有任何与其他客户共享的元素？ 或者一切都是独立的？

每个 Azure Red Hat OpenShift 群集都是专用于给定的客户，并且存在于该客户的订阅中。 

### <a name="are-infrastructure-nodes-available"></a>基础结构节点是否可用？

在 Azure Red Hat OpenShift 4.x 群集上，基础结构节点当前不可用。

在 Azure Red Hat OpenShift 3.11 群集上，默认包括基础结构节点。

## <a name="how-do-i-handle-cluster-upgrades"></a>如何处理群集升级？

有关升级、维护和支持版本的信息，请参阅[支持生命周期指南](support-lifecycle.md)。

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>主机操作系统和 OpenShift 软件如何更新？

当 Azure Red Hat OpenShift 使用来自上游 OpenShift 容器平台的次要发布版本和补丁时，主机操作系统和 OpenShift 软件将进行更新。

### <a name="whats-the-process-to-reboot-the-updated-node"></a>节点更新后，将如何重新启动？

节点将在升级过程中重新启动。

## <a name="cluster-operations"></a>群集操作

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>能否使用 Prometheus 监视我的应用程序？

Prometheus 经过预安装并配置，适用于 Azure Red Hat OpenShift 4.x 群集。 详细了解[群集监视](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html)。

对于 Azure Red Hat OpenShift 3.11 群集，可以在命名空间中部署 Prometheus 并监视命名空间中的应用程序。 有关详细信息，请参阅[在 Azure Red Hat OpenShift 群集中部署 Prometheus 实例](howto-deploy-prometheus.md)。

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>能否使用 Prometheus 监视与群集运行状况和容量相关的指标？

在 Azure Red Hat OpenShift 4.x 中：可以。

在 Azure Red Hat OpenShift 3.11 中：不可以。

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>底层 VM 的日志是否会流出到客户日志分析系统中？

来自底层 VM 的日志由托管服务处理，不会向客户公开。

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>客户如何获取对 CPU/内存等节点级别指标的访问权限，以便采取措施来缩放、调试问题等？ 我好像无法在 Azure Red Hat OpenShift 群集上运行 kubectl top。

对于 Azure Red Hat OpenShift 4.x 群集，OpenShift Web 控制台包含了节点级别的所有指标。 有关详细信息，请参阅关于[查看群集信息](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html)的 Red Hat 文档。

对于 Azure Red Hat OpenShift 3.11 群集，客户可以通过使用命令 `oc adm top nodes` 或 `kubectl top nodes`，利用客户管理员群集角色来访问节点级别的 CPU/内存指标。 客户还可以使用命令 `oc adm top pods` 或 `kubectl top pods` 来访问 `pods` 的 CPU/内存指标。

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果纵向扩展部署，Azure 容错域将如何映射到 Pod 位置，以确保某个服务的所有 Pod 在单个容错域中出现故障时不会失效？

在使用 Azure 中的虚拟机规模集时，默认有五个容错域。 规模集中的每个虚拟机实例都将被放入这其中的任一容错域之中。 这样可以确保部署到群集中的计算节点上的应用程序将被置于单独的容错域中。

有关详细信息，请参阅[为虚拟机规模集选择合适数量的容错域](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md)。

### <a name="is-there-a-way-to-manage-pod-placement"></a>是否有方法能管理 Pod 放置？

客户能够以客户管理员身份获取节点和查看标签。这将提供一种方法定位规模集中的任何 VM。

在使用特定标签时，必须注意：

- 不得使用主机名。 主机名通常会在升级和更新后轮换，且一定会发生变化。
- 如果客户有针对特定标签或某个部署策略的请求，或许可能实现，但是需要一定的工程工作，且目前暂不受支持。

有关详细信息，请参阅[控制 Pod 放置](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html)。

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>映像注册表是否可在外部使用，以便使用 Jenkins 等工具？

对于 4.x 群集，需要公开一个安全注册表并配置身份验证。 有关详细信息，请参阅以下 Red Hat 文档：

- [公开注册表](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [访问注册表](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

对于 3.11 群集，可以使用 Docker 映像注册表。 Docker 注册表可从 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` 获取。 还可以使用 Azure 容器注册表。

## <a name="networking"></a>网络

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>能否将群集部署到现有的虚拟网络中？

在 4.x 群集中，可以将群集部署到现有 VNet 中。

在 3.11 群集中，不可以将群集部署到现有 VNet 中。 但可以通过对等互连功能，将 Azure Red Hat OpenShift 3.11 群集连接到现有 VNet。

### <a name="is-cross-namespace-networking-supported"></a>是否支持跨命名空间的网络连接？

客户及各项目管理员可以使用 `NetworkPolicy` 对象，自定义每个项目的跨命名空间网络连接（包括拒绝网络连接）。

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我正在尝试对另一个订阅中的虚拟网络进行对等互连，但未能成功，收到了 VNet CIDR 错误。

在具有虚拟网络的订阅中，请确保使用以下命令注册 `Microsoft.ContainerService` 提供程序：`az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>是否可以指定专用 VNet 上部署的 IP 范围，以免对等互连后，与其他公司的 VNet 发生冲突？

在 4.x 群集中，可以指定自己的 IP 范围。

在 3.11 群集中，Azure Red Hat OpenShift 支持 VNet 对等互连，并允许客户提供用于对等互连的 VNet，以及供 OpenShift 网络在其中运行的 VNet CIDR。

Azure Red Hat OpenShift 创建的 VNet 将受到保护，不接受配置更改。 对等互连的 VNet 由客户控制，并驻留在该客户的订阅中。

### <a name="is-the-software-defined-network-module-configurable"></a>软件定义的网络模块是否可配置？

软件定义的网络是 `openshift-ovs-networkpolicy`，不可配置。

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift 使用什么 Azure 负载均衡器？  标准版还是基本版，是否可配置？

Azure Red Hat OpenShift 使用标准版 Azure 负载均衡器，且不可配置。

## <a name="permissions"></a>权限

### <a name="can-an-admin-manage-users-and-quotas"></a>管理员是否可以管理用户和配额？

是的。 Azure Red Hat OpenShift 管理员不仅可以访问所有用户创建的项目，还可以管理用户和配额。

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>是否可以将群集限制为仅限特定的 Azure AD 用户？

是的。 可以通过配置 Azure AD 应用程序，限制哪些 Azure AD 用户可以登录到群集。 有关详细信息，请参阅[如何：将应用限制为供一组用户使用](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

### <a name="can-i-restrict-users-from-creating-projects"></a>是否可以限制用户创建项目？

是的。 以管理员身份登录到群集，然后执行以下命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

有关详细信息，请参阅相关 OpenShift 文档，了解如何对群集版本禁用自我预配：

- [在 4.6 群集中禁用自我预配](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [在 3.11 群集中禁用自我预配](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>哪些 UNIX 权限（IaaS 中）可用于主节点/基础结构节点/应用节点？

对于 4.x 群集，可以通过群集管理角色访问节点。 有关详细信息，请参阅 [Kubernetes RBAC 概述](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html)。

在 3.11 群集中，禁止访问节点。

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我们有哪些 OCP 权限？ 群集管理员？ 项目管理员？

对于 4.x 群集，群集管理员角色可用。 有关详细信息，请参阅 [Kubernetes RBAC 概述](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html)。

对于 3.11 群集，请参阅[群集管理概述](https://docs.openshift.com/aro/admin_guide/index.html)了解更多详细信息。

### <a name="which-identity-providers-are-available"></a>有哪些标识提供者可用？

对于 4.x 群集，可以配置自己的标识提供者。 有关详细信息，请参阅关于[配置标识提供者](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html)的 Red Hat 文档。

对于 3.11 群集，可以使用 Azure AD 集成。 

## <a name="storage"></a>存储

### <a name="is-data-on-my-cluster-encrypted"></a>我的群集上的数据是否已加密？

默认情况下，数据采用静态加密。 Azure 存储平台会在保存数据前先自动加密数据，然后在检索之前解密数据。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../storage/common/storage-service-encryption.md)。

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>etcd 中存储的数据在 Azure Red Hat OpenShift 上是否已加密？

对于 Azure Red Hat OpenShift 4 群集，默认不会对数据进行加密，但是提供了启用加密的选项。 有关详细信息，请参阅[加密 etcd](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html) 上的指南。

对于 3.11 群集，不会在 etcd 级别对数据进行加密。 当前不支持启用加密的选项。 OpenShift 支持此功能，但需经过工程作业后才能纳入路线图规划。 数据在磁盘级别进行加密。 有关详细信息，请参阅[在数据存储层加密数据](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)。

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>能否选择任何持久性存储解决方案，例如 OCS？ 

对于 4.x 群集，Azure 磁盘 (Premium_LRS) 配置为默认存储类。 有关其他存储提供程序以及配置详细信息（包括 Azure 文件），请参阅关于[永久性存储](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html)的 Red Hat 文档。

对于 3.11 群集，默认提供两个存储类：一个用于 Azure 磁盘 (Premium_LRS)，另一个用于 Azure 文件。

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO 是否会将任何客户数据存储在群集区域之外？

不知道。 在 ARO 群集中创建的所有数据都将保留在群集区域内。
