---
title: 规划已启用 Azure Arc 的数据服务部署
description: 本文介绍计划已启用 Azure Arc 的数据服务部署的注意事项。
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: ffb57d83493aee607fa89720d978a00b25e44e24
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426842"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>规划已启用 Azure Arc 的数据服务部署

本文介绍如何计划部署已启用 Azure Arc 的数据服务。

开始计划部署已启用 Azure Arc 的数据服务时，正确了解数据库工作负载以及这些工作负载的业务要求是很重要的。 例如，需要考虑工作负载的可用性、业务连续性以及内存、CPU 和存储的容量要求。 你还需要根据业务需求仔细准备基础结构以支持数据库工作负载。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已满足特定的先决条件，并准备好必要的背景和信息。 若要确保成功部署，必须正确配置基础结构环境，使其具有正确的访问级别和适当的存储、CPU 和内存容量。 

请参阅以下文章：

- [大小调整指南](sizing-guidance.md)
- [存储配置](storage-configuration.md)
- [连接模式及其要求](connectivity.md)

验证：

- 已安装 [arcdata CLI 扩展](install-arcdata-extension.md)。
- 已安装其他[客户端工具](install-client-tools.md)。
- 你具有 Kubernetes 群集的访问权限。
- 已配置“kubeconfig”文件。 它应指向要部署到的 Kubernetes 群集。 为验证群集的当前上下文，请运行以下命令：
   ```console
   kubectl cluster-info
   ```  
- 你有一个 Azure 订阅，Azure Arc 数据控制器、已启用 Azure Arc 的 SQL 托管实例或已启用 Azure Arc 的 PostgreSQL 超大规模服务器等资源将针对该订阅进行预测并计费。

准备好基础结构后，按以下方式部署已启用 Azure Arc 的数据服务：
1. 在 Kubernetes 群集的一个已验证发行版中创建已启用 Azure Arc 的数据控制器。
1. 创建已启用 Azure Arc 的 SQL 托管实例和/或已启用 Azure Arc 的 PostgreSQL 超大规模服务器组。

> [!CAUTION]
> 一些数据服务层级和模式已提供[正式发布版 (GA)](release-notes.md)，而一些则提供预览版。 建议不要在同一数据控制器上混合使用正式发布版和预览版服务。 如果在同一数据控制器上混合使用正式发布版和预览版服务，则无法就地升级。 在这种情况下，如果要升级，必须删除并重新创建数据控制器和数据服务。

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>概述：创建已启用 Azure Arc 的数据控制器

可在各类 Kubernetes 群集上创建已启用 Azure Arc 的数据服务，还可使用各种方法创建托管 Kubernetes 服务。

目前，有效的 Kubernetes 服务和发行版列表包括：

- Amazon Elastic Kubernetes 服务 (Amazon EKS)
- Azure Kubernetes 服务 (AKS)
- Azure Stack HCI 上的 Azure Kubernetes 服务
- Azure Red Hat OpenShift
- Google Kubernetes 引擎 (GKE)
- 开源的上游 Kubernetes（通常使用 kubeadm 进行部署）
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * 支持的最低 Kubernetes 版本为 v1.19。 有关详细信息，请参阅[发行说明&nbsp;- 已启用 Azure Arc 的数据服务](./release-notes.md#known-issues)的“已知问题”部分。
> * 支持的最低 OCP 版本为 4.7。
> * 如果要使用 Azure Kubernetes 服务，则群集的工作器节点虚拟机 (VM) 大小应至少为 Standard_D8s_v3 并使用高级磁盘。 
> * 群集不应跨多个可用性区域。 
> * 有关详细信息，请参阅[发行说明&nbsp;- 已启用 Azure Arc 的数据服务](./release-notes.md#known-issues)的“已知问题”部分。

创建已启用 Azure Arc 的数据服务时，无论选择何种服务或分发选项，你都需提供以下信息：

- **数据控制器名称**：数据控制器的描述性名称（例如“production-dc”或“seattle-dc”） 。 名称必须满足 [Kubernetes 命名标准](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)。

- **用户名**：Kibana/Grafana 管理员用户的用户名。
- **密码**：Kibana/Grafana 管理员用户的密码。
- **Kubernetes 命名空间的名称**：要在其中创建数据控制器的 Kubernetes 命名空间的名称。
- **连接模式**：确定从已启用 Azure Arc 的数据服务环境到 Azure 的连接程度。 间接连接模式已正式发布，而直接连接模式以预览版提供 。 对连接模式的选择决定了部署方法的选项。 有关详细信息，请参阅[连接模式和要求](./connectivity.md)。
- **Azure 订阅 ID**：要在其中创建 Azure 数据控制器资源的 Azure 订阅 GUID。 所有已启用 Azure Arc 的 SQL 托管实例和 Azure Database for PostgreSQL 超大规模服务器组也都在此订阅中创建并针对此订阅计费。
- **Azure 资源组名称**：要在其中创建 Azure 数据控制器资源的资源组的名称。 所有已启用 Azure Arc 的 SQL 托管实例和 Azure Database for PostgreSQL 超大规模服务器组也都在此资源组中创建。
- **Azure 位置**：Azure 中要存储数据控制器资源元数据的 Azure 位置。 有关可用区域列表，请参阅 Azure 全局基础结构的[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 页面。 由部署的数据控制器管理的 Azure 资源的相关元数据和账单信息仅存储在 Azure 中你指定为位置参数的位置。 如果要在直接连接模式下部署，则数据控制器的位置参数与目标自定义位置资源的位置相同。
- **服务主体信息**： 
   - 如果要在创建 Azure Arc 数据控制器期间以直接连接模式部署，则需要服务主体信息。 有关详细信息，请参阅[将使用数据、指标和日志上传到 Azure](upload-metrics-and-logs-to-azure-monitor.md) 的“将角色分配到服务主体”一节。 
   - 在间接连接模式下，你仍需手动导出和上传服务主体，但前提是已创建了 Azure Arc 数据控制器。
- **基础结构**：出于计费目的，必须指明在哪个基础结构上运行已启用 Azure Arc 的数据服务。 选项包括 alibaba、aws、azure、gcp、onpremises 或其他项     。

## <a name="additional-concepts-for-direct-connectivity-mode"></a>直接连接模式的其他概念

如[连接模式和要求](./connectivity.md)中所述，你可以在直接或间接连接模式下部署 Azure Arc 数据控制器。 在直接连接模式下部署 Azure Arc 数据服务需要了解一些其他的概念和注意事项：

* 首先，将在其中部署已启用 Azure Arc 的数据服务的 Kubernetes 群集需要是[已启用 Azure Arc 的 Kubernetes 群集](../kubernetes/overview.md)。 通过将 Kubernetes 群集部署到 Azure Arc，可为 Azure 连接提供自动上传使用情况信息、日志和指标等功能。 通过将 Kubernetes 群集连接到 Azure，还可直接从 Azure 门户将 Azure Arc 数据服务部署到群集并进行管理。 若要了解如何操作，请参阅[将群集连接到 Azure](../kubernetes/quickstart-connect-cluster.md)。

* 在 Kubernetes 群集部署到 Azure Arc 后，要在已启用 Azure Arc 的 Kubernetes 群集上部署已启用 Azure Arc 的数据服务，请进行以下操作：
   1. 创建 Arc 数据服务扩展。 若要了解如何操作，请参阅[已启用 Azure Arc 的 Kubernetes 的群集扩展](../kubernetes/conceptual-extensions.md)。
   1. 创建自定义位置。 若要了解如何操作，请参阅[已启用 Azure Arc 的 Kubernetes 上的自定义位置](../kubernetes/conceptual-custom-locations.md)。
   1. 创建 Azure Arc 数据控制器。

   通过使用 Azure 门户中的 Azure Arc 数据控制器创建向导，可在一个步骤中执行这三个步骤。

安装 Azure Arc 数据控制器后，可访问数据服务，例如已启用 Azure Arc 的 SQL 托管实例或已启用 Azure Arc 的超大规模 PostgreSQL。


## <a name="next-steps"></a>后续步骤

可使用其他几个选项来创建 Azure Arc 数据控制器：

> 想尝试一下吗？
> 在 AKS、Amazon EKS、GKE 上或在 Azure VM 中使用 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 可快速入门。
>
- [使用 Azure 门户在直接连接模式下创建数据控制器](create-data-controller-direct-prerequisites.md)
- [使用 CLI 在间接连接模式下创建数据控制器](create-data-controller-indirect-cli.md)
- [使用 Azure Data Studio 在间接连接模式下创建数据控制器](create-data-controller-indirect-azure-data-studio.md)
- [通过 Azure Data Studio 中的 Jupyter 笔记本，从 Azure 门户以间接连接模式创建数据控制器](create-data-controller-indirect-azure-portal.md)
- [使用 Kubernetes 工具（如 kubectl 或 oc）在间接连接模式下创建数据控制器](create-data-controller-using-kubernetes-native-tools.md)
