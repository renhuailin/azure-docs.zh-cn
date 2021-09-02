---
title: 从 Azure Red Hat OpenShift 3.11 迁移到 Azure Red Hat OpenShift 4
description: 从 Azure Red Hat OpenShift 3.11 迁移到 Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migration, aro, openshift, red hat
ms.openlocfilehash: 9f166b393350971a48b23420daa9917b6ba7bbe1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038035"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>从 Azure Red Hat OpenShift 3.11 迁移到 Azure Red Hat OpenShift 4

OpenShift 4 上的 Azure Red Hat OpenShift 引入了 Red Hat Core OS 上的 Kubernetes 1.16、专用群集、自带虚拟网络支持和完整的群集管理员角色。 此外，现在还提供许多新功能，例如对于运算符框架、运算符中心或 OpenShift 服务网格的支持。

要从 Azure Red Hat OpenShift 3.11 成功转换到 Azure Red Hat OpenShift 4，请务必查看[存储、网络、日志记录、安全性和监视方面的区别](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html)。

在本文中，我们将演示如何从 Azure Red Hat OpenShift 3.11 群集迁移到 Azure Red Hat 4 群集。

> [!NOTE]
> Red Hat OpenShift 迁移工具（如控制平面迁移帮助工具和群集应用程序迁移工具 (CAM)）无法与 Azure Red Hat OpenShift 3.11 群集一起使用。

## <a name="before-you-begin"></a>准备阶段

本文假定你有一个现有的 Azure Red Hat OpenShift 3.11 群集。

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>创建目标 Azure Red Hat OpenShift 4 群集

首先，[创建要用作目标群集的 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。 这里，我们将使用基本配置。 如果你对其他设置感兴趣，请参阅[创建 Azure Red Hat OpenShift 4 群集教程](tutorial-create-cluster.md)。

为主节点和工作器节点创建一个包含两个空子网的虚拟网络。

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

然后使用以下命令创建群集。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>配置目标 OpenShift 4 群集

### <a name="authentication"></a>身份验证

对于要与 Azure Red Hat OpenShift 交互的用户，他们必须首先向群集进行身份验证。 身份验证层会识别与 Azure Red Hat OpenShift API 请求相关联的用户。 然后，授权层使用与发出请求的用户相关的信息来确定是否允许该请求。

创建 Azure Red Hat OpenShift 4 群集时，将创建一个临时管理用户。 [连接到群集](tutorial-connect-cluster.md)，添加用户和组，并为两者[配置适当的权限](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html)。

### <a name="networking"></a>网络

Azure Red Hat OpenShift 4 使用几个不同的运算符在群集中设置网络，它们分别是：[群集网络运算符](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator)、[DNS 运算符](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)和[流入量运算符](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html)。 有关在 Azure Red Hat OpenShift 4 群集中设置网络的详细信息，请参阅[网络关系图](concepts-networking.md)和[了解网络](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)。

### <a name="storage"></a>存储
Azure Red Hat OpenShift 4 支持以下 PersistentVolume 插件：

- AWS Elastic Block Store (EBS)
- Azure 磁盘
- Azure 文件
- GCE 永久性磁
- HostPath
- iSCSI
- 本地卷
- NFS
- Red Hat OpenShift 容器存储

有关如何配置这些存储类型的信息，请参阅[配置永久性存储](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.7/html/storage/configuring-persistent-storage)。

### <a name="registry"></a>注册表

Azure Red Hat OpenShift 4 可以从源代码生成映像，对其进行部署，并管理其生命周期。 为了实现这一点，Azure Red Hat OpenShift 4 提供[集成的内部容器映像注册表](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html)，该注册表可以部署在 Azure Red Hat OpenShift 环境中以在本地管理映像。

如果要使用 [Azure 容器注册表](../container-registry/index.yml)、[Red Hat Quay 注册表](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)或[已启用身份验证的 Red Hat 注册表](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)等外部注册表，请按照步骤向群集提供凭据，使群集可以访问存储库。

### <a name="monitoring"></a>监视

Azure Red Hat OpenShift 包括一个预配置、预安装且自行更新的监视堆栈，该堆栈基于 Prometheus 开源项目及其范围较广泛的生态系统。 它可以监视群集组件，并包含一组可立即通知群集管理员任何发生的问题的警报，以及一组 Grafana 仪表板。 群集监视堆栈仅支持监视 Azure Red Hat OpenShift 群集。 有关详细信息，请参阅 [Azure Red Hat OpenShift 的群集监视](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html)。

如果一直[针对 Azure Red Hat OpenShift 3.11 使用适用于容器的 Azure Monitor](../azure-monitor/containers/container-insights-azure-redhat-setup.md)，则还可以为 [Azure Red Hat OpenShift 4 群集](../azure-monitor/containers/container-insights-azure-redhat4-setup.md)启用适用于容器的 Azure Monitor，并继续使用同一 Log Analytics 工作区。

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>将 DNS 或负载均衡器配置移动到新群集

如果要使用 Azure 流量管理器，请添加指向目标群集的终结点，并设置这些终结点的优先级。

## <a name="deploy-application-to-your-target-cluster"></a>将应用程序部署到目标群集

为工作负载正确配置目标群集后，[连接到群集](tutorial-connect-cluster.md)，并为项目创建必要的应用程序、组件或服务。 通过 Azure Red Hat OpenShift，可以根据 Git、容器映像、Red Hat 开发人员目录、Dockerfile 和 YAML/JSON 定义或者从目录中选择数据库服务来创建它们。

## <a name="delete-your-source-cluster"></a>删除源群集
确认正确设置 Azure Red Hat OpenShift 4 群集后，请删除 Azure Red Hat OpenShift 3.11 群集。

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>后续步骤
[在此处](https://docs.openshift.com/container-platform/4.6/welcome/index.html)查看 Red Hat OpenShift 文档。
