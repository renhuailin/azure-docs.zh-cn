---
title: 规划已启用 Azure Arc 的数据服务部署
description: 说明计划部署已启用 Azure Arc 的数据服务时的注意事项
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b1e7c210dc7e9a0482f6f2471e5079f71a4ac621
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741483"
---
# <a name="plan-to-deploy-azure-arc-enabled-data-services"></a>计划部署已启用 Azure Arc 的数据服务

本文介绍如何计划部署已启用 Azure Arc 的数据服务。


首先，Azure Arc 数据服务部署要求适当了解数据库工作负载以及这些工作负载的业务要求。 例如，考虑可用性、业务连续性以及这些工作负载的内存、CPU 和存储容量要求。 其次，用于支持这些数据库工作负载的基础结构需要根据业务要求进行准备。

## <a name="prerequisites"></a>先决条件

在部署已启用 Azure Arc 的数据服务之前，请务必了解先决条件，并准备好所有必要的信息，使用正确的访问级别正确配置基础结构环境，为存储、CPU 和内存使用适当的容量。 这样就可以在结束时成功部署。

查阅以下部分：
- [大小调整指南](sizing-guidance.md)
- [存储配置](storage-configuration.md)
- [连接模式及其要求](connectivity.md)

验证你是否：
- 已安装 [`arcdata` CLI 扩展](install-arcdata-extension.md)。
- 已安装其他[客户端工具](install-client-tools.md)。
- 具有 Kubernetes 群集的访问权限。
- 已配置 `kubeconfig` 文件。 它应指向要部署到的 Kubernetes 群集。 运行以下命令，验证要部署到的群集的当前上下文：
   ```console
   kubectl cluster-info
   ```
- 对 Azure Arc 数据控制器、已启用 Azure Arc 的 SQL 托管实例或已启用 Azure Arc 的 PostgreSQL 超大规模服务器等资源的 Azure 订阅将会进行预测并计费。

准备好基础结构后，按以下方式部署已启用 Azure Arc 的数据服务：
1. 在 Kubernetes 群集的某个已验证发行版中创建已启用 Azure Arc 的数据控制器
1. 创建已启用 Azure Arc 的 SQL 托管实例和/或已启用 Azure Arc 的 PostgreSQL 超大规模服务器组。

## <a name="overview-create-the-azure-arc-enabled-data-controller"></a>概述：创建已启用 Azure Arc 的数据控制器

可以使用多种不同的方法，在多种不同类型的 Kubernetes 群集和托管 Kubernetes 服务上创建已启用 Azure Arc 的数据服务。

目前，有效的 Kubernetes 服务和发行版列表包括：


- AWS 弹性 Kubernetes 服务 (EKS)
- Azure Kubernetes 服务 (AKS)
- Azure Stack HCI 上的 Azure Kubernetes 服务
- Azure RedHat OpenShift (ARO)
- Google Cloud Kubernetes Engine (GKE)
- 通常使用 kubeadm 部署的开源上游 Kubernetes
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * 支持的最低 Kubernetes 版本为 v1.19。 请参阅[已知问题](./release-notes.md#known-issues)了解更多信息。
> * 支持的最低 OCP 版本为 4.7。
> * 如果你使用的是 Azure Kubernetes 服务，则群集的工作器节点 VM 大小应至少为 Standard_D8s_v3 并使用高级磁盘 。 
> * 群集不应跨多个可用性区域。 
> * 请参阅[已知问题](./release-notes.md#known-issues)了解更多信息。

无论选择哪个选项，在创建过程中都需要提供以下信息：

- 数据控制器名称 - 数据控制器的描述性名称 - 例如“production-dc”、“seattle-dc”。 名称必须满足 [Kubernetes 命名标准](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)。
- 用户名 - Kibana/Grafana 管理员用户的用户名。
- 密码 - Kibana/Grafana 管理员用户的密码。
- Kubernetes 命名空间的名称 - 要在其中创建数据控制器的 Kubernetes 命名空间的名称。
- 连接模式 - 连接模式确定从已启用 Azure Arc 的数据服务环境到 Azure 的连接度。 间接连接模式已正式发布。 直接连接模式为预览版。  连接模式的选择决定了部署方法的选项。  有关详细信息，请参阅[连接模式](./connectivity.md)。
- Azure 订阅 ID - 要在其中创建 Azure 数据控制器资源的 Azure 订阅 GUID。  还将在此订阅中创建所有已启用 Azure Arc 的 SQL 托管实例和 PostgreSQL 超大规模服务器组，并对该订阅进行计费。
- Azure 资源组名称 - 要在其中创建 Azure 数据控制器资源的资源组的名称。  还将在此资源组中创建所有已启用 Azure Arc 的 SQL 托管实例和 PostgreSQL 超大规模服务器组。
- Azure 位置 - 要将数据控制器资源元数据存储到的 Azure 位置。 有关可用区域的列表，请参阅 [Azure 全球基础结构/各区域的产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)。 有关要部署的数据控制器管理的 Azure 资源的元数据和计费信息将仅存储在 Azure 中指定为位置参数的位置。 如果要在直接连接模式下部署，则数据控制器的位置参数将与目标自定义位置资源的位置相同。
- 服务主体信息 - 如[上传先决条件](upload-metrics-and-logs-to-azure-monitor.md)一文中所述，在直接连接模式下部署时，你将需要在 Azure Arc 数据控制器创建过程中使用服务主体信息。 对于间接连接模式，仍需要在创建 Azure Arc 数据控制器后手动导出和上传服务主体。
- 基础结构 - 出于计费目的，需要指示在其上运行已启用 Arc 的数据服务的基础结构。  选项包括 `alibaba`、`aws`、`azure`、`gcp` `onpremises` 或 `other`。

## <a name="additional-concepts-for-direct-connected-mode"></a>直接连接模式的其他概念

如[连接模式](./connectivity.md)中所述，Azure Arc 数据控制器可在“直接”或“间接”连接模式下进行部署。 在“直接”连接模式下部署 Azure Arc 数据服务需要了解其他一些概念和注意事项。
首先，将在其中部署已启用 Arc 的数据服务的 Kubernetes 群集需要是[已启用 Azure Arc 的 Kubernetes 群集](../kubernetes/overview.md)。 将 Kubernetes 群集载入 Azure Arc 将提供 Azure 连接，可用于自动上传使用情况信息、日志、指标等功能。通过将 Kubernetes 群集连接到 Azure，还可以直接从 Azure 门户向群集部署和管理 Azure Arc 数据服务。

将 Kubernetes 群集连接到 Azure 涉及以下步骤：
- [将群集连接到 Azure](../kubernetes/quickstart-connect-cluster.md)

其次，在 Kubernetes 群集载入 Azure Arc 后，在已启用 Azure Arc 的 Kubernetes 群集上部署已启用 Azure Arc 的数据服务涉及以下内容：
- 创建 Arc 数据服务扩展，了解有关[群集扩展](../kubernetes/conceptual-extensions.md)的详细信息
- 创建自定义位置，详细了解[自定义位置](../kubernetes/conceptual-custom-locations.md)
- 创建 Azure Arc 数据控制器

通过使用 Azure 门户中的 Azure Arc 数据控制器创建向导，可以在单个步骤中执行这三个步骤。

安装 Azure Arc 数据控制器后，可以创建数据服务，例如已启用 Azure Arc 的 SQL 托管实例或已启用 Azure Arc 的 PostgreSQL 超大规模服务器组。


## <a name="next-steps"></a>后续步骤

有多个选项可用于创建 Azure Arc 数据控制器：

> 想尝试一下吗？
> 在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中使用 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 快速开始！
>
- [使用 Azure 门户在直接连接模式下创建数据控制器](create-data-controller-direct-prerequisites.md)
- [使用 CLI 在间接连接模式下创建数据控制器](create-data-controller-indirect-cli.md)
- [使用 Azure Data Studio 在间接连接模式下创建数据控制器](create-data-controller-indirect-azure-data-studio.md)
- [使用 Azure Data Studio 通过 Jupyter 笔记本从 Azure 门户在间接连接模式下创建数据控制器](create-data-controller-indirect-azure-portal.md)
- [使用 Kubernetes 工具（如 kubectl 或 oc）在间接连接模式下创建数据控制器](create-data-controller-using-kubernetes-native-tools.md)

