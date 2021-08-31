---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/12/2021
ms.author: larryfr
ms.openlocfilehash: 8f60a29adcdf8e679a523748a97a24ba2f06cbdd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739143"
---
使用 Azure 机器学习计算实例或计算群集时，允许来自 Azure Batch 管理和 Azure 机器学习服务的入站流量 。 为此流量创建用户定义的路由时，可以使用 IP 地址或服务标记来路由流量 。

> [!IMPORTANT]
> 结合使用服务标记与用户定义的路由目前处于预览阶段，可能不完全受支持。 有关详细信息，请参阅[虚拟网络路由](/azure/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview)。

# <a name="ip-address-routes"></a>[IP 地址路由](#tab/ipaddress)

对于 Azure 机器学习服务，必须添加主要和次要区域的 IP 地址 。 若要查找次要区域，请参阅[使用 Azure 配对区域确保业务连续性和灾难恢复](/azure/best-practices-availability-paired-regions.md#azure-regional-pairs)。 例如，如果 Azure 机器学习服务位于“美国东部 2”，则次要区域是“美国中部”。 

若要获取 Batch 服务和 Azure 机器学习服务的 IP 地址列表，请使用以下方法之一：

* 下载 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)，并在文件中搜索 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`（其中 `<region>` 是你的 Azure 区域）。

* 使用 [Azure CLI](/cli/azure/install-azure-cli) 下载信息。 下面的示例下载 IP 地址信息，并筛选出“美国东部 2”区域（主要）和“美国中部”区域（次要）的信息：

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > 如果你使用的是美国-弗吉尼亚、美国-亚利桑那区域或中国东部 2 区域，则这些命令不会返回任何 IP 地址。 而如果使用以下链接之一下载 IP 地址列表：
    >
    > * [适用于 Azure 政府的 Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [适用于 Azure 中国的 Azure IP 范围和服务标记](https://www.microsoft.com//download/details.aspx?id=57062)

> [!IMPORTANT]
> IP 地址可能会随时间推移而改变。

创建 UDR 时，将“下一个跃点类型”设置为 Internet 。 下图显示了 Azure 门户中基于 IP 地址的 UDR 示例：

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="该图显示了用户定义的路由配置":::

# <a name="service-tag-routes"></a>[服务标记路由](#tab/servicetag)

为以下服务标记创建用户定义的路由：

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`，其中 `<region>` 是 Azure 区域。

以下命令演示了如何为这些服务标记添加路由：

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

有关配置 UDR 的信息，请参阅[使用路由表路由网络流量](/azure/virtual-network/tutorial-create-route-table-portal.md)。