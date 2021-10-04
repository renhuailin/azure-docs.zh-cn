---
title: 使用虚拟网络 (VNet) 保护工作区资源
titleSuffix: Azure Machine Learning
description: 使用隔离的 Azure 虚拟网络 (VNet) 保护 Azure 机器学习工作区资源和计算环境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 09/24/2021
ms.topic: how-to
ms.custom: devx-track-python, references_regions, contperf-fy21q1,contperf-fy21q4,FY21Q4-aml-seo-hack, security
ms.openlocfilehash: 1844d9a84714231aac7cb399239c31a6af62661c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093509"
---
<!-- # Virtual network isolation and privacy overview -->
# <a name="secure-azure-machine-learning-workspace-resources-using-virtual-networks-vnets"></a>使用虚拟网络 (VNet) 保护 Azure 机器学习工作区资源

使用虚拟网络 (VNet) 保护 Azure 机器学习工作区资源和计算环境。 本文使用一个示例方案来说明如何配置完整的虚拟网络。

> [!TIP]
> 本文是介绍如何保护 Azure 机器学习工作流系列文章的一部分。 请参阅本系列中的其他文章：
>
> * [保护工作区资源](how-to-secure-workspace-vnet.md)
> * [保护训练环境](how-to-secure-training-vnet.md)
> * [保护推理环境](how-to-secure-inferencing-vnet.md)
> * [启用工作室功能](how-to-enable-studio-virtual-network.md)
> * [使用自定义 DNS](how-to-custom-dns.md)
> * [使用防火墙](how-to-access-azureml-behind-firewall.md)

## <a name="prerequisites"></a>先决条件

本文假定你已熟悉以下主题：
+ [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
+ [IP 网络](../virtual-network/public-ip-addresses.md)
+ [具有专用终结点的 Azure 机器学习工作区](how-to-configure-private-link.md)
+ [网络安全组 (NSG)](../virtual-network/network-security-groups-overview.md)
+ [网络防火墙](../firewall/overview.md)
## <a name="example-scenario"></a>示例方案

本部分将介绍如何设置一个常见的网络方案来确保 Azure 机器学习与专用 IP 地址的通信安全。

下表比较了在使用 VNet 和不使用 VNet 的情况下服务如何访问 Azure 机器学习网络的不同部分：

| 场景 | 工作区 |  关联的资源 | 训练计算环境 | 推理计算环境 |
|-|-|-|-|-|-|
|**无虚拟网络**| 公共 IP | 公共 IP | 公共 IP | 公共 IP |
|**确保虚拟网络中的资源安全**| 专用 IP（专用终结点） | 公共 IP（服务终结点） <br> \- 或 - <br> 专用 IP（专用终结点） | 公共 IP | 专用 IP  | 

* 工作区 - 为你的工作区创建专用终结点。 专用终结点通过多个专用 IP 地址将工作区连接到 VNet。
* 关联的资源 - 使用服务终结点或专用终结点连接到工作区资源，如 Azure 存储、Azure Key Vault。 对于 Azure 容器服务，请使用专用终结点。
    * **服务终结点**：为 Azure 服务提供虚拟网络的标识。 在虚拟网络中启用服务终结点后，可以添加虚拟网络规则，以在虚拟网络中保护 Azure 服务资源。 服务终结点使用公共 IP。
    * **专用终结点**：是网络接口，可安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。
* 训练计算访问：使用公共 IP 地址安全访问训练计算目标，如 Azure 机器学习计算实例和 Azure 机器学习计算群集。 
* **推理计算访问**：使用专用 IP 地址访问 Azure Kubernetes 服务 (AKS) 计算群集。


接下来的部分介绍如何保护上述网络环境。 若要确保网络安全，必须：

1. 保护[工作区和关联的资源](#secure-the-workspace-and-associated-resources)。
1. 保护[训练环境](#secure-the-training-environment)。
1. 保护[推理环境](#secure-the-inferencing-environment)。
1. 可选：[启用工作室功能](#optional-enable-studio-functionality)。
1. 配置[防火墙设置](#configure-firewall-settings)。
1. 配置 [DNS 名称解析](#custom-dns)。
## <a name="secure-the-workspace-and-associated-resources"></a>保护工作区和关联的资源

使用以下步骤确保工作区和关联的资源安全。 这些步骤可使服务在虚拟网络中进行通信。

1. 创建[启用了专用链接的工作区](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)，以启用 VNet 与工作区之间的通信。
1. 使用服务终结点或专用终结点将以下服务添加到虚拟网络 。 还要允许受信任的 Microsoft 服务访问以下服务：

    | 服务 | 终结点信息 | 允许受信任的信息 |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [服务终结点](../key-vault/general/overview-vnet-service-endpoints.md)</br>[专用终结点](../key-vault/general/private-link-service.md) | [允许受信任的 Microsoft 服务绕过此防火墙](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure 存储帐户__ | [服务和专用终结点](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)</br>[专用终结点](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts) | [授予对受信任的 Azure 服务的访问权限](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure 容器注册表__ | [专用终结点](../container-registry/container-registry-private-link.md) | [允许受信任的服务](../container-registry/allow-access-trusted-services.md) |


![显示工作区和关联的资源如何通过服务终结点或 VNet 内的专用终结点相互通信的体系结构关系图](./media/how-to-network-security-overview/secure-workspace-resources.png)

有关如何完成这些步骤的详细说明，请参阅[确保 Azure 机器学习工作区安全](how-to-secure-workspace-vnet.md)。 

### <a name="limitations"></a>限制

确保工作区以及虚拟网络中关联的资源的安全具有以下限制：
- Azure 中国世纪互联区域不支持使用具有专用终结点的 Azure 机器学习工作区。
- 所有资源都必须位于同一个 VNet 中。 但允许使用同一 VNet 中的子网。

## <a name="secure-the-training-environment"></a>保护训练环境

此部分介绍如何保护 Azure 机器学习中的训练环境。 你还将了解 Azure 机器学习如何完成训练作业来了解网络配置协同工作的方法。

若要保护训练环境，请执行以下步骤：

1. [在虚拟网络中创建 Azure 机器学习计算实例和计算机群集](how-to-secure-training-vnet.md#compute-cluster)以运行训练作业。
1. [允许入站通信](how-to-secure-training-vnet.md#required-public-internet-access)，以便管理服务可以将作业提交到你的计算资源。 

![显示如何确保托管计算群集和实例安全的体系结构示意图](./media/how-to-network-security-overview/secure-training-environment.png)

有关如何完成这些步骤的详细说明，请参阅[确保训练环境安全](how-to-secure-training-vnet.md)。 

### <a name="example-training-job-submission"></a>训练作业提交示例 

本部分介绍 Azure 机器学习如何安全地在服务之间进行通信，以提交训练作业。 此内容将展示所有配置如何协同工作以确保通信安全。

1. 客户端将训练脚本和培训数据上传到使用服务或专用终结点保护的存储帐户。

1. 客户端通过专用终结点将训练作业提交到 Azure 机器学习工作区。

1. Azure Batch 服务从工作区接收作业。 然后，它通过计算资源的公共负载均衡器将训练作业提交到计算环境。 

1. 计算资源接收作业并开始训练。 计算资源访问安全存储帐户以下载训练文件和上传输出。

### <a name="limitations"></a>限制

- Azure 计算实例和 Azure 计算群集必须与工作区及其关联的资源位于同一 VNet、区域和订阅中。 

## <a name="secure-the-inferencing-environment"></a>保护推理环境

本部分介绍可用于确保推理环境安全的选项。 建议使用 Azure Kubernetes 服务 (AKS) 群集进行大规模的生产部署。

虚拟网络中的 AKS 群集有两个选项：

- 将默认 AKS 群集部署或附加到 VNet。
- 将专用 AKS 群集附加到 VNet。

**默认 AKS 群集**：具有一个带公共 IP 的控制平面。 可以在部署过程中将默认 AKS 群集添加到 VNet，也可以在创建群集后附加群集。

**专用 AKS 群集**：具有控制平面，只能通过专用 IP 对其进行访问。 创建群集后，必须附加专用 AKS 群集。

有关如何添加默认群集和专用群集的详细说明，请参阅[确保推理环境安全](how-to-secure-inferencing-vnet.md)。 

以下网络示意图显示了一个受保护的 Azure 机器学习工作区，其中包含附加到虚拟网络的专用 AKS 群集。

![显示如何将专用 AKS 群集附加到虚拟网络的体系结构关系图。 AKS 控制平面置于客户 VNet 之外](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>限制
- AKS 群集必须与工作区及其关联资源属于同一 VNet。 

## <a name="optional-enable-public-access"></a>可选：启用公共访问

你可以使用专用终结点保护 VNet 后面的工作区，并且仍允许通过公共 Internet 进行访问。 初始配置与[确保工作区和关联资源安全](#secure-the-workspace-and-associated-resources)的方式相同。 

使用专用终结点确保工作区的安全后，即可[启用公共访问](how-to-configure-private-link.md#enable-public-access)。 此后，你可以从公共 Internet 和 VNet 访问工作区。

### <a name="limitations"></a>限制

- 如果通过公共 Internet 使用 Azure 机器学习工作室，则设计器等某些功能可能无法访问你的数据。 当数据存储在 VNet 保护的服务中时则会出现此问题。 例如 Azure 存储帐户。
## <a name="optional-enable-studio-functionality"></a>可选：启用工作室功能

[保护工作区](#secure-the-workspace-and-associated-resources) > [保护训练环境](#secure-the-training-environment) > [保护推理环境](#secure-the-inferencing-environment) > **启用工作室功能** > [配置防火墙设置](#configure-firewall-settings)

如果存储在 VNet 中，则必须使用额外的配置步骤在工作室中启用全部功能。 默认情况下，禁用以下功能：

* 预览工作室中的数据。
* 在设计器中将数据可视化。
* 在设计器中部署模型。
* 提交 AutoML 试验。
* 启动标记项目。

若要启用完整的工作室功能，请参阅[在虚拟网络中使用 Azure 机器学习工作室](how-to-enable-studio-virtual-network.md)。

### <a name="limitations"></a>限制

[ML 辅助数据标记](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling)不支持虚拟网络背后的默认存储帐户。 相反，对于 ML 辅助数据标记，请使用存储帐户而不是默认帐户。 

> [!TIP]
> 只要不是默认的存储帐户，数据标签使用的帐户就可以受到虚拟网络的保护。 

## <a name="configure-firewall-settings"></a>配置防火墙设置

配置防火墙以控制 Azure 机器学习工作区资源和公共 Internet 之间的流量。 尽管我们建议使用 Azure 防火墙，但你也可以使用其他防火墙产品。 

有关防火墙设置的详细信息，请参阅[使用防火墙后面的工作区](how-to-access-azureml-behind-firewall.md)。

## <a name="custom-dns"></a>自定义 DNS

如果需要为虚拟网络使用自定义 DNS 解决方案，则必须为工作区添加主机记录。

有关所需域名和 IP 地址的详细信息，请参阅[如何将工作区与自定义 DNS 服务器配合使用](how-to-custom-dns.md)。

## <a name="next-steps"></a>后续步骤

本文是介绍如何保护 Azure 机器学习工作流系列文章的一部分。 请参阅本系列中的其他文章：

* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [启用工作室功能](how-to-enable-studio-virtual-network.md)
* [使用自定义 DNS](how-to-custom-dns.md)
* [使用防火墙](how-to-access-azureml-behind-firewall.md)