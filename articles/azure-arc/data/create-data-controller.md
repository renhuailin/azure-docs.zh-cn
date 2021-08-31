---
title: 创建数据控制器
description: 在已部署的典型多节点 Kubernetes 群集上创建 Azure Arc 数据控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 74c41ed89a3980967532955e46d4dade9eaacdb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728046"
---
# <a name="create-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器


## <a name="overview-of-creating-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器的概述

可以使用多种不同的方法，在多种不同类型的 Kubernetes 群集和托管 Kubernetes 服务上创建已启用 Azure Arc 的数据服务。

目前，支持的 Kubernetes 服务和发行版列表如下：

- Azure Kubernetes 服务 (AKS)
- Azure Stack HCI 上的 Azure Kubernetes 服务
- Azure RedHat OpenShift (ARO)
- OpenShift Container Platform (OCP)
- AWS 弹性 Kubernetes 服务 (EKS)
- Google Cloud Kubernetes Engine (GKE)
- 通常使用 kubeadm 部署的开源上游 Kubernetes

> [!IMPORTANT]
> * 支持的最低 Kubernetes 版本为 v1.17。 请参阅[已知问题](./release-notes.md#known-issues)了解更多信息。 
> * 支持的最低 OCP 版本为 4.5。
> * 请参阅[连接要求](connectivity.md)，了解需要在环境与 Azure 之间建立哪种连接。
> * 请参阅[存储配置指南](storage-configuration.md)，了解有关如何配置持久性存储的详细信息。
> * 如果你使用的是 Azure Kubernetes 服务，则群集的工作器节点 VM 大小应至少为 Standard_D8s_v3 并使用高级磁盘 。 群集不应跨多个可用性区域。 
> * 如果你使用的是其他 Kubernetes 发行版或服务，应确保所用节点大小至少提供 8 GB RAM 和 4 个核心，并且所有 Kubernetes 节点总共有 32 GB RAM 的容量可用。 例如，可以使用 1 个节点并在其上提供 32 GB RAM 和 4 个核心，或者使用 2 个节点并在每个节点上提供 16 GB RAM 和 4 个核心。

> [!NOTE]
> 如果使用的是 Azure 上的 Red Hat OpenShift Container Platform，建议使用最新可用版本。

根据选择的选项，某些工具是必需的，但建议在开始创建 Azure Arc 数据控制器之前[安装所有客户端工具](./install-client-tools.md)。

无论选择哪个选项，在创建过程中都需要提供以下信息：

- 数据控制器名称 - 数据控制器的描述性名称，例如“生产数据控制器”和“西雅图数据控制器”。
- 数据控制器用户名 - 数据控制器管理员用户的任意用户名。
- 数据控制器密码 - 数据控制器管理员用户的密码。
- Kubernetes 命名空间的名称 - 要在其中创建数据控制器的 Kubernetes 命名空间的名称。
- 连接模式 - 连接模式确定从已启用 Azure Arc 的数据服务环境到 Azure 的连接度。 间接连接模式已正式发布。 直接连接模式为预览版。  有关详细信息，请参阅[连接模式](./connectivity.md)。 
- Azure 订阅 ID - 要在其中创建 Azure 数据控制器资源的 Azure 订阅 GUID。
- Azure 资源组名称 - 要在其中创建 Azure 数据控制器资源的资源组的名称。
- Azure 位置 - 要将数据控制器资源元数据存储到的 Azure 位置。 有关可用区域的列表，请参阅 [Azure 全球基础结构/各区域的产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)。 有关要部署的数据控制器管理的 Azure 资源的元数据和计费信息将仅存储在 Azure 中指定为位置参数的位置。 如果要在直接连接模式下部署，则数据控制器的位置参数将与目标自定义位置资源的位置相同。

## <a name="next-steps"></a>后续步骤

有多个选项可用于创建 Azure Arc 数据控制器：

> 想尝试一下吗？  
> 在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中使用 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 快速开始！
> 
- [使用 CLI 在间接连接模式下创建数据控制器](create-data-controller-indirect-cli.md)
- [使用 Azure Data Studio 在间接连接模式下创建数据控制器](create-data-controller-indirect-azure-data-studio.md)
- [使用 Azure Data Studio 通过 Jupyter 笔记本从 Azure 门户在间接连接模式下创建数据控制器](create-data-controller-indirect-azure-portal.md)
- [使用 Kubernetes 工具（如 kubectl 或 oc）在间接连接模式下创建数据控制器](create-data-controller-using-kubernetes-native-tools.md)
- [在间接连接模式下创建数据控制器](create-data-controller-direct-prerequisites.md)
- [使用用于加速体验测试部署的 Azure Arc Jumpstart 创建数据控制器](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
