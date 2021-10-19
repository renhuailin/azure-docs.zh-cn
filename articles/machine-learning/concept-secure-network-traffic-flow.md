---
title: 保护网络流量流
titleSuffix: Azure Machine Learning
description: 了解当 Azure 机器学习工作区位于受保护的虚拟网络中时，网络流量在组件之间的流动方式。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/22/2021
ms.openlocfilehash: 9e35609a6e6d450b2938dfbb4feab319f5a3c9c2
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660211"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>使用受保护工作区时的网络流量流

当 Azure 机器学习工作区和关联的资源在 Azure 虚拟网络中受保护时，资源之间的网络流量会发生改变。 在没有虚拟网络的情况下，网络流量将通过公共 Internet 或在 Azure 数据中心内部流动。 引入虚拟网络 (VNet) 后，你可能还想要强化网络安全性。 例如，阻止 VNet 与公共 Internet 之间的入站和出站通信。 但是，Azure 机器学习需要在公共 Internet 上访问某些资源。 例如，使用 Azure 资源管理执行部署和管理操作。

本文列出了需要传入/传出公共 Internet 的流量。 其中还解释了在以下方案中，网络流量如何在客户端开发环境和受保护的 Azure 机器学习工作区之间流动：

* 通过 Azure 机器学习工作室来使用：

    * 工作区
    * 自动化 ML
    * Designer
    * 数据集和数据存储

    > [!TIP]
    > Azure 机器学习工作室是一个基于 Web 的 UI，它有一部分在 Web 浏览器中运行，并且它会调用 Azure 服务来执行各种任务，例如训练模型、使用设计器或查看数据集。 其中一些调用使用的通信流与使用 SDK、CLI、REST API 或 VS Code 时的通信流不同。

* 通过 Azure 机器学习工作室、SDK、CLI 或 REST API 来使用   ：

    * 计算实例和群集
    * Azure Kubernetes 服务
    * Azure 机器学习管理的 Docker 映像

> [!TIP]
> 如果此处未列出某个方案或任务，则不管是否使用受保护工作区，该方案或任务的工作方式都应该是相同的。

## <a name="assumptions"></a>假设

本文假设采用以下配置：

* Azure 机器学习工作区使用专用终结点来与 VNet 通信。
* 工作区使用的 Azure 存储帐户、密钥保管库和容器注册表也使用专用终结点来与 VNet 通信。
* 客户端工作站使用 VPN 网关或 Express Route 来访问 VNet。

## <a name="inbound-and-outbound-requirements"></a>入站和出站要求


| __方案__ | 所需的入站配置 | 所需的出站配置 | __其他配置__ | 
| ----- | ----- | ----- | ----- |
| [从工作室访问工作区](#scenario-access-workspace-from-studio) | NA | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Azure 机器学习服务</li></ul> | 可能需要使用自定义 DNS 服务器。 有关详细信息，请参阅 [将工作区与自定义 DNS 结合使用](how-to-custom-dns.md)。 | 
| [从工作室使用 AutoML、设计器、数据集和数据存储](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | NA | NA | <ul><li>工作区服务主体配置</li><li>允许从受信任的 Azure 服务进行访问</li></ul>有关详细信息，请参阅[如何在虚拟网络中保护工作区](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)。 | 
| [使用计算实例和计算群集](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>端口 44224 上的 Azure 机器学习服务</li><li>端口 29876-29877 上的 Azure Batch Management 服务</li></ul> | <ul><li>Azure Active Directory</li><li>Azure 资源管理器</li><li>Azure 机器学习服务</li><li>Azure 存储帐户</li><li>Azure Key Vault</li></ul> | 如果使用防火墙，请创建用户定义的路由。 有关详细信息，请参阅[配置入站和出站流量](how-to-access-azureml-behind-firewall.md)。 | 
| [使用 Azure Kubernetes 服务](#scenario-use-azure-kubernetes-service) | NA | 有关 AKS 的出站配置的信息，请参阅[如何部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)。 | 配置内部负载均衡器。 有关详细信息，请参阅[如何部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)。 | 
| [使用 Azure 机器学习管理的 Docker 映像](#scenario-use-docker-images-managed-by-azure-ml) | NA | <ul><li>Microsoft 容器注册表</li><li>`viennaglobal.azurecr.io` 全局容器注册表</li></ul> | 如果工作区的 Azure 容器注册表位于 VNet 后面，请将工作区配置为使用计算群集来生成映像。 有关详细信息，请参阅[如何在虚拟网络中保护工作区](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)。 | 


## <a name="scenario-access-workspace-from-studio"></a>方案：从工作室访问工作区

> [!NOTE]
> 本部分中的信息专用于从 Azure 机器学习工作室使用工作区。 如果你使用 Azure 机器学习 SDK、REST API、CLI 或 Visual Studio Code，则本部分中的信息不适用。

从工作室访问工作区时，网络流量的流如下所示：

* 为了向资源进行身份验证，将使用 Azure Active Directory。
* 对于管理和部署操作，将使用 Azure 资源管理器。
* 对于 Azure 机器学习特定的任务，将使用 Azure 机器学习服务
* 为了访问 Azure 机器学习工作室 (https://ml.azure.com) ，将使用 Azure FrontDoor。
* 对于大多数存储操作，流量将流经工作区默认存储的专用终结点。 [使用 AutoML、设计器、数据集和数据存储](#scenario-use-automl-designer-dataset-and-datastore-from-studio)部分介绍了例外情况。
* 此外，还需要配置一个 DNS 解决方案，以便能够解析 VNet 中的资源名称。 有关详细信息，请参阅 [将工作区与自定义 DNS 结合使用](how-to-custom-dns.md)。

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="使用工作室时客户端和工作区之间的网络流量示意图":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>方案：从工作室使用 AutoML、设计器、数据集和数据存储

Azure 机器学习工作室的以下功能使用数据事件探查：

* 数据集：从工作室探索数据集。
* 设计器：可视化模块输出数据。
* AutoML：查看数据预览/配置文件并选择目标列。
* 标记

数据事件探查依赖于 Azure 机器学习托管服务能够访问工作区的默认 Azure 存储帐户。 该托管服务并不存在于你的 VNet 中，因此无法直接访问 VNet 中的存储帐户。 工作区使用服务主体来访问存储。

> [!TIP]
> 可以在创建工作区时提供服务主体。 如果你不这样做，系统将为你创建一个与工作区同名的服务主体。

若要允许访问存储帐户，请将存储帐户配置为允许工作区的“资源实例”或选择“允许受信任服务列表中的 Azure 服务访问此存储帐户” 。 此设置允许托管服务通过 Azure 数据中心网络访问存储。 

接下来，将工作区的服务主体添加到存储帐户专用终结点的“读取者”角色。 此角色用于验证工作区和存储子网信息。 如果它们相同，则允许访问。 最后，服务主体还需要对存储帐户拥有“Blob 数据参与者”访问权限。

有关详细信息，请参阅[如何在虚拟网络中保护工作区](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)的“Azure 存储帐户”部分。

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="客户端、数据事件探查和存储之间的流量示意图":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>方案：使用计算实例和计算群集

Azure 机器学习计算实例和计算群集是由 Microsoft 托管的托管服务。 它们构建于 Azure Batch 服务的基础之上。 它们虽然存在于 Microsoft 托管环境中，但也会注入到你的 VNet 中。

创建计算实例或计算群集时，还会在 VNet 中创建以下资源：

* 具有所需出站规则的网络安全组。 这些规则允许从 Azure 机器学习（在端口 44224 上使用 TCP）和 Azure Batch 服务（在端口 29876-29877 上使用 TCP）进行入站访问。

    > [!IMPORTANT]
    > 如果使用防火墙来阻止从 Internet 访问 VNet，则必须配置该防火墙以允许此流量。 例如，可以使用 Azure 防火墙创建用户定义的路由。 有关详细信息，请参阅[如何将 Azure 机器学习与防火墙结合使用](how-to-access-azureml-behind-firewall.md#inbound-configuration)。

* 具有公共 IP 的负载均衡器。

还允许对以下服务标记进行出站访问。 对于每个标记，请将 `region` 替换为计算实例/群集的 Azure 区域：

* `Storage.region` - 此出站访问用于连接到 Azure Batch 服务管理的 VNet 中的 Azure 存储帐户。
* `Keyvault.region` - 此出站访问用于连接到 Azure Batch 服务管理的 VNet 中的 Azure 密钥保管库帐户。

来自计算实例或群集的数据访问将通过 VNet 存储帐户的专用终结点进行。

如果在计算实例上使用 Visual Studio Code，则必须允许其他出站流量。 有关详细信息，请参阅[如何将 Azure 机器学习与防火墙结合使用](how-to-access-azureml-behind-firewall.md)。

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="使用计算实例或群集时的流量示意图":::

## <a name="scenario-use-azure-kubernetes-service"></a>方案：使用 Azure Kubernetes 服务

有关 Azure Kubernetes 服务所需的出站配置的信息，请参阅[如何部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)的“连接要求”部分。

> [!NOTE]
> Azure Kubernetes 服务负载均衡器不是 Azure 机器学习创建的负载均衡器。 若要将模型作为仅在 VNet 上可用的受保护应用程序来托管，请使用 Azure 机器学习创建的内部负载均衡器。 若要允许公共访问，请使用 Azure 机器学习创建的公共负载均衡器。

如果模型需要额外的入站或出站连接（例如连接到外部数据源），请使用网络安全组或防火墙来允许流量。

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>方案：使用 Azure ML 管理的 Docker 映像

Azure 机器学习提供可用于训练模型或执行推理的 Docker 映像。 如果你不指定自己的映像，则会使用 Azure 机器学习提供的映像。 这些映像托管在 Microsoft Container Registry (MCR) 上。 它们还托管在名为 `viennaglobal.azurecr.io` 的异地复制的 Azure 容器注册表上。

如果你提供自己的 Docker 映像（例如，在你提供的 Azure 容器注册表上），则不需要使用 MCR 或 `viennaglobal.azurecr.io` 进行出站通信。

> [!TIP]
> 如果 Azure 容器注册表在 VNet 中受保护，则 Azure 机器学习无法使用它来生成 Docker 映像。 在这种情况下，必须指定一个 Azure 机器学习计算群集来生成映像。 有关详细信息，请参阅[如何在虚拟网络中保护工作区](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)。

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="使用提供的 Docker 映像时的流量流示意图":::
## <a name="next-steps"></a>后续步骤

了解网络流量在受保护配置中的流动方式后，接下来请阅读[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)一文，详细了解如何在虚拟网络中保护 Azure ML。

有关最佳做法的信息，请参阅 [Azure 机器学习的企业安全性最佳做法](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)一文。