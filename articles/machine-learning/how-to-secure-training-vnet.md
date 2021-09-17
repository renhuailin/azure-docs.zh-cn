---
title: 使用虚拟网络保护训练环境
titleSuffix: Azure Machine Learning
description: 使用独立的 Azure 虚拟网络保护 Azure 机器学习训练环境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/04/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: b4b7f35173b4f1d6d83d9b7ffd937704750f5502
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122603964"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>使用虚拟网络保护 Azure 机器学习训练环境

本文介绍如何在 Azure 机器学习中使用虚拟网络保护训练环境。

> [!TIP]
> 本文是介绍如何保护 Azure 机器学习工作流的系列文章的一部分。 请参阅本系列中的其他文章：
>
> * [虚拟网络概述](how-to-network-security-overview.md)
> * [保护工作区资源](how-to-secure-workspace-vnet.md)
> * [保护推理环境](how-to-secure-inferencing-vnet.md)
> * [启用工作室功能](how-to-enable-studio-virtual-network.md)
> * [使用自定义 DNS](how-to-custom-dns.md)
> * [使用防火墙](how-to-access-azureml-behind-firewall.md)
>
> 有关创建安全工作区、计算群集和计算实例的教程，请参阅[教程：创建安全工作区](tutorial-create-secure-workspace.md)。

本文介绍如何在虚拟网络中保护以下训练计算资源：
> [!div class="checklist"]
> - Azure 机器学习计算群集
> - Azure 机器学习计算实例
> - Azure Databricks
> - 虚拟机
> - HDInsight 群集

## <a name="prerequisites"></a>先决条件

+ 阅读[网络安全概述](how-to-network-security-overview.md)一文，了解常见的虚拟网络方案和总体虚拟网络体系结构。

+ 用于计算资源的现有虚拟网络和子网。

+ 若要将资源部署到虚拟网络或子网中，你的用户帐户必须在 Azure 基于角色的访问控制 (Azure RBAC) 中具有以下操作的权限：

    - “Microsoft.Network/virtualNetworks/*/read”（在虚拟网络资源上）。 这对于 ARM 模板部署来说是不需要的
    - “Microsoft.Network/virtualNetworks/subnet/join/action”（在子网资源上）。

    若要详细了解如何将 Azure RBAC 与网络配合使用，请参阅[网络内置角色](../role-based-access-control/built-in-roles.md#networking)

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure 机器学习计算群集/实例

* 该虚拟网络必须与 Azure 机器学习工作区位于同一订阅。
* 用于计算实例或群集的子网必须有足够的未分配 IP 地址。

    * 计算群集可动态缩放。 如果没有足够的未分配 IP 地址，则只会为群集分配一部分资源。
    * 计算实例只需要一个 IP 地址。

* 确保没有任何安全策略或锁定限制管理虚拟网络所需的权限。 检查策略或锁定时，请查看虚拟网络的订阅和资源组。
* 检查对虚拟网络的订阅或资源组实施的安全策略或锁定是否限制了管理虚拟网络所需的权限。 
* 如果你打算通过限制流量来保护虚拟网络，请参阅[所需的公共 Internet 访问权限](#required-public-internet-access)部分。
* 用于部署计算群集/实例的子网不应委托给任何其他服务。 例如，不应将它委托给 ACI。

### <a name="azure-databricks"></a>Azure Databricks

* 该虚拟网络必须与 Azure 机器学习工作区位于同一订阅和区域。
* 如果工作区的 Azure 存储帐户也在虚拟网络中受保护，则它们必须与 Azure Databricks 群集位于同一虚拟网络中。

## <a name="limitations"></a>限制

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure 机器学习计算群集/实例

* 如果将多个计算实例或群集置于一个虚拟网络中，可能需要请求为一个或多个资源增加配额。 机器学习计算实例或群集自动在包含虚拟网络的资源组中分配更多网络资源。 对于每个计算实例或群集，此服务分配以下资源：

    * 一个网络安全组 (NSG)。 此 NSG 包含以下特定于计算群集和计算实例的规则：

        * 允许 `BatchNodeManagement` 服务标记中端口 29876-29877 上的入站 TCP 流量。
        * 允许 `AzureMachineLearning` 服务标记中端口 44224 上的入站 TCP 流量。

        下面的屏幕截图展示了这些规则的示例：

        :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="NSG 的屏幕截图":::

    * 一个公共 IP 地址。 如果 Azure Policy 分配禁止创建公共 IP，则群集/实例的部署将失败
    * 一个负载均衡器

    对于计算群集，每当群集纵向缩减到 0 个节点时，将删除这些资源，而当群集横向扩展时，将创建这些资源。

    对于计算实例，这些资源将一直保留，直到该实例被删除。 停止实例不会删除资源。 

    > [!IMPORTANT]
    > 这些资源受订阅的[资源配额](../azure-resource-manager/management/azure-subscription-service-limits.md)限制。 如果虚拟网络资源组被锁定，则删除计算群集/实例将失败。 在删除计算群集/实例之前，无法删除负载均衡器。 此外，请确保不存在禁止创建网络安全组的 Azure Policy 分配。

* 如果工作区的 Azure 存储帐户也在虚拟网络中，请遵循以下有关子网限制的指导：

    * 如果你打算使用 Azure 机器学习工作室来可视化数据或使用设计器，则存储帐户必须与计算实例或群集位于同一子网中 。
    * 如果你打算使用 SDK，则存储帐户可以位于不同的子网中。

    > [!NOTE]
    > 选中“允许信任的 Microsoft 服务访问此帐户”复选框不足以允许来自计算的通信。

* 当工作区使用专用终结点时，只能从虚拟网络内部访问计算实例。 如果使用自定义 DNS 或 hosts 文件，请为 `<instance-name>.<region>.instances.azureml.ms` 添加一个条目。 将此条目映射到工作区专用终结点的专用 IP 地址。 有关详细信息，请参阅 [自定义 DNS](./how-to-custom-dns.md) 一文。
* 虚拟网络服务终结点策略不适用于计算群集/实例系统存储帐户。
* 如果存储和计算实例位于不同的区域，则可能会出现间歇性超时。
* 如果要在计算实例上使用 Jupyter Notebook：

    * 不要禁用 websocket 通信。 确保网络允许与 `*.instances.azureml.net` 和 `*.instances.azureml.ms` 进行 websocket 通信。
    * 确保笔记本在与数据相同的虚拟网络和子网后的计算资源上运行。 创建计算实例时，请使用“高级设置” > “配置虚拟网络”选择网络和子网 。

* 可以在不包含你的工作区的其他区域中创建计算群集。 此功能处于预览版阶段，只可用于计算群集，不可用于计算实例 。 为群集使用不同的区域时，有以下限制：

    * 如果工作区关联的资源（例如存储）与群集位于不同的虚拟网络中，请在网络之间设置全局虚拟网络对等互连。 有关详细信息，请参阅[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。
    * 如果使用已启用专用终结点的工作区，则不支持在不同的区域中创建群集。
    * 你可能会看到网络延迟和数据传输成本增加。 在创建群集和在该群集上运行作业时，可能会产生延迟和成本。

    当使用不同于工作区的区域时，诸如使用 NSG 规则、用户定义的路由和输入/输出要求等指南可以正常应用。

### <a name="azure-databricks"></a>Azure Databricks

* 除了 Azure Databricks 使用的 __databricks-private__ 和 __databricks-public__ 子网以外，还需要为虚拟网络创建 __default__ 子网。
* Azure Databricks 不使用专用终结点来与虚拟网络通信。

有关在虚拟网络中使用 Azure Databricks 的详细信息，请参阅[在 Azure 虚拟网络中部署 Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight 或虚拟机

* Azure 机器学习只支持运行 Ubuntu 的虚拟机。

## <a name="required-public-internet-access"></a>所需的公共 Internet 访问权限

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

有关使用防火墙解决方案的信息，请参阅[将防火墙和 Azure 机器学习结合使用](how-to-access-azureml-behind-firewall.md)。

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>计算群集和实例 

使用以下选项卡选择计算群集的创建方式：

# <a name="studio"></a>[工作室](#tab/azure-studio)

使用以下步骤在 Azure 机器学习工作室中创建计算群集：

1. 登录 [Azure 机器学习工作室](https://ml.azure.com/)，然后选择你的订阅和工作区。
1. 在左侧选择“计算”，从中心选择“计算群集”，然后选择“+ 新建”。  

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="创建群集的屏幕截图":::

1. 在“创建计算群集”对话中，选择所需的 VM 大小和配置，然后选择“下一步”。 

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="设置 VM 配置的屏幕截图":::

1. 在“配置设置”部分中，设置“计算名称”、“虚拟网络”和“子网”。   

    > [!TIP]
    > 如果工作区使用专用终结点连接到虚拟网络，则“虚拟网络”选择字段将显示为灰色。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="虚拟网络设置的屏幕快照":::

1. 选择“创建”以创建计算群集。

# <a name="python"></a>[Python](#tab/python)

以下代码在名为 `mynetwork` 的虚拟网络的 `default` 子网中创建新的机器学习计算群集：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           location="westus2",
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

---

创建过程完成后，请在试验中使用该群集训练模型。 有关详细信息，请参阅[选择并使用用于训练的计算目标](how-to-set-up-training-targets.md)。

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="inbound-traffic"></a>入站流量

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

有关 Azure 机器学习输入和输出流量要求的详细信息，请参阅[在防火墙后面使用工作区](how-to-access-azureml-behind-firewall.md)。

## <a name="azure-databricks"></a>Azure Databricks

若要详细了解如何结合使用 Azure Databricks 和虚拟网络，请参阅[在 Azure 虚拟网络中部署 Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>虚拟机或 HDInsight 群集

本部分介绍如何将虚拟网络中的虚拟机或 Azure HDInsight 群集用于工作区。

### <a name="create-the-vm-or-hdinsight-cluster"></a>创建 VM 或 HDInsight 群集

使用 Azure 门户或 Azure CLI 创建 VM 或 HDInsight 群集，然后将群集置于 Azure 虚拟网络中。 有关详细信息，请参阅以下文章：
* [为 Linux VM 创建和管理 Azure 虚拟网络](../virtual-machines/linux/tutorial-virtual-network.md)

* [使用 Azure 虚拟网络扩展 HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>配置网络端口 

允许 Azure 机器学习与 VM 或群集上的 SSH 端口进行通信，为网络安全组配置源条目。 SSH 端口通常是端口 22。 若要允许来自此源的流量，请执行以下操作：

1. 在“源”下拉列表中，选择“服务标记”。

1. 在“源服务标记”下拉列表中，选择“AzureMachineLearning”。 

    ![用于在虚拟网络中的 VM 或 HDInsight 群集上执行试验的入站规则](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. 在“源端口范围”下拉列表中，选择 __*__ 。

1. 在“目标”下拉列表中，选择“任何”。 

1. 在“目标端口范围”下拉列表中，选择“22”。 

1. 在“协议”下，选择“任何”。 

1. 在“操作”下，选择“允许”。 

保留网络安全组的默认出站规则。 有关详细信息，请参阅[安全组](../virtual-network/network-security-groups-overview.md#default-security-rules)中的“默认安全规则”。

如果你不想要使用默认的出站规则，同时想要限制虚拟网络的出站访问，请参阅[所需公共 Internet 访问](#required-public-internet-access)部分。

### <a name="attach-the-vm-or-hdinsight-cluster"></a>附加 VM 或 HDInsight 群集

将 VM 或 HDInsight 群集附加到 Azure 机器学习工作区。 有关详细信息，请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)。

## <a name="next-steps"></a>后续步骤

本文是介绍如何保护 Azure 机器学习工作流系列文章的一部分。 请参阅本系列中的其他文章：

* [虚拟网络概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [启用工作室功能](how-to-enable-studio-virtual-network.md)
* [使用自定义 DNS](how-to-custom-dns.md)
* [使用防火墙](how-to-access-azureml-behind-firewall.md)