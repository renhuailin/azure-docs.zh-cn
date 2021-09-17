---
title: 托管虚拟网络和托管专用终结点
description: 了解 Azure 数据工厂中的托管虚拟网络和托管专用终结点。
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions, devx-track-azurepowershell
ms.date: 07/20/2021
ms.openlocfilehash: 29bd9cf165ef8247a4185b17d479b01c4e14fa87
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638348"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure 数据工厂托管虚拟网络（预览版）

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍 Azure 数据工厂中的托管虚拟网络和托管专用终结点。


## <a name="managed-virtual-network"></a>托管虚拟网络

当你在 Azure 数据工厂托管虚拟网络 (VNET) 中创建 Azure Integration Runtime (IR) 时，系统会使用托管虚拟网络预配集成运行时，后者将利用专用终结点安全地连接到支持的数据存储。 

在托管虚拟网络中创建 Azure IR 可确保隔离和保护数据集成过程。 

使用托管虚拟网络的好处：

- 使用托管虚拟网络，可以将管理虚拟网络的负担转移给 Azure 数据工厂。 不需要为 Azure Integration Runtime 创建子网，它最终可能会使用虚拟网络中的多个专用 IP，需要事先进行网络基础结构规划。 
- 它不需要深入的 Azure 网络知识即可安全地进行数据集成。 而对数据工程师来说，安全 ETL 的入门要简单得多。 
- 托管虚拟网络与托管专用终结点一起防止数据外泄。 

> [!IMPORTANT]
>当前，托管虚拟网络仅在与 Azure 数据工厂区域相同的区域中受支持。

> [!Note]
>由于 Azure 数据工厂托管虚拟网络仍以公共预览版提供，因此没有 SLA 保证。

> [!Note]
>现有的公共 Azure 集成运行时无法切换到 Azure 数据工厂托管虚拟网络中的 Azure 集成运行时，反之亦然。
 

:::image type="content" source="./media/managed-vnet/managed-vnet-architecture-diagram.png" alt-text="ADF 托管虚拟网络体系结构":::

## <a name="managed-private-endpoints"></a>托管专用终结点

托管专用终结点是在 Azure 数据工厂托管虚拟网络中创建的专用终结点，用于建立与 Azure 资源的专用链接。 Azure 数据工厂会代表你管理这些专用终结点。 

:::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="新托管专用终结点":::

Azure 数据工厂支持专用链接。 通过专用链接，你可以访问 Azure (PaaS) 服务（如 Azure 存储、Azure Cosmos DB、Azure Synapse Analytics）。

使用专用链接时，数据存储与托管虚拟网络之间的流量将完全通过 Microsoft 主干网络进行传输。 专用链接可防止数据泄露风险。 可以通过创建专用终结点来建立到资源的专用链接。

专用终结点使用托管虚拟网络中的专用 IP 地址将服务有效接入托管虚拟网络中。 专用终结点映射到 Azure 中的特定资源，而不是整个服务。 客户可以将连接限制到其组织批准的特定资源。 详细了解[专用链接和专用终结点](../private-link/index.yml)。

> [!NOTE]
> 建议你创建托管专用终结点来连接到所有 Azure 数据源。 
 
> [!WARNING]
> 如果 PaaS 数据存储（Blob、ADLS Gen2、Azure Synapse Analytics）已针对它创建了专用终结点，即使它允许从所有网络进行访问，ADF 也只能使用托管专用终结点对其进行访问。 如果专用终结点尚不存在，则在这种情况下必须创建一个。 

在 Azure 数据工厂中创建托管专用终结点时，创建的专用终结点连接将处于“挂起”状态。 将启动审批工作流。 专用链接资源所有者负责批准或拒绝该连接。

:::image type="content" source="./media/tutorial-copy-data-portal-private/manage-private-endpoint.png" alt-text="管理专用终结点":::

如果所有者批准该连接，则会建立专用链接。 否则，将不会建立专用链接。 在任一情况下，都会以连接状态更新托管专用终结点。

:::image type="content" source="./media/tutorial-copy-data-portal-private/approve-private-endpoint.png" alt-text="批准托管专用终结点":::

只有处于已批准状态的托管专用终结点才能将流量发送到给定的专用链接资源。

## <a name="interactive-authoring"></a>交互式创作
交互式创作功能用于测试连接、浏览文件夹列表和表列表、获取架构和预览数据等功能。 可以在创建或编辑 ADF 托管虚拟网络中的 Azure Integration Runtime 时启用交互式创作。 后端服务将为交互式创作功能预先分配计算。 否则，每次执行任何交互式操作都将分配计算，这将花费更多时间。 交互式创作的生存时间 (TTL) 为 60 分钟，这意味着在上一次交互式创作操作 60 分钟后它将自动被禁用。

:::image type="content" source="./media/managed-vnet/interactive-authoring.png" alt-text="交互式创作":::

## <a name="activity-execution-time-using-managed-virtual-network"></a>使用托管虚拟网络的活动执行时间
按照设计，托管虚拟网络中的 Azure 集成运行时的队列时间比公共 Azure 集成运行时长，因为我们不会为每个数据工厂都保留一个计算节点，因此每个活动都需要预热后才能启动，并且主要在虚拟网络联接而不是在 Azure 集成运行时上发生。 对于非复制活动（包括管道活动和外部活动），首次触发它们时有 60 分钟的生存时间 (TTL)。 在 TTL 内，排队时间较短，因为节点已预热。 
> [!NOTE]
> 复制活动尚不支持 TTL。

## <a name="create-managed-virtual-network-via-azure-powershell"></a>通过 Azure PowerShell 创建托管虚拟网络
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}" -Properties @{}

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>限制和已知问题
### <a name="supported-data-sources"></a>支持的数据源
以下数据源具有本机专用终结点支持，并可通过专用链接从 ADF 托管虚拟网络连接。
- Azure Blob 存储（不包括存储帐户 V1）
- Azure 表存储（不包括存储帐户 V1）
- Azure 文件（不包括存储帐户 V1）
- Azure Data Lake Gen2
- Azure SQL 数据库（不包括 Azure SQL 托管实例）
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure 专用链接服务
- Azure 搜索
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB
- Azure 机器学习

> [!Note]
> 仍可以通过公共网络访问数据工厂支持的所有数据源。

> [!NOTE]
> 由于 Azure SQL 托管实例目前不支持本机专用终结点，因此可以使用专用链接服务和负载均衡器从托管虚拟网络访问它。 请参阅[如何使用专用终结点从数据工厂托管 VNET 访问 SQL 托管实例](tutorial-managed-virtual-network-sql-managed-instance.md)。

### <a name="on-premises-data-sources"></a>本地数据源
若要使用专用终结点从托管虚拟网络访问本地数据源，请参阅教程[如何使用专用终结点从数据工厂托管 VNET 访问本地 SQL Server](tutorial-managed-virtual-network-on-premise-sql-server.md)。

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure 数据工厂托管虚拟网络在以下 Azure 区域可用：
- 澳大利亚东部
- 澳大利亚东南部
- 巴西南部
- 加拿大中部
- 加拿大东部
- 印度中部
- 美国中部
- 中国东部 2
- 中国北部 2
- 东亚
- 美国东部
- 美国东部 2
- 法国中部
- 德国中西部
- 日本东部
- 日本西部
- 韩国中部
- 美国中北部
- 北欧
- 挪威东部
- 南非北部
- 美国中南部
- 东南亚
- 瑞士北部
- 阿拉伯联合酋长国北部
- US Gov 亚利桑那州
- US Gov 德克萨斯州
- US Gov 弗吉尼亚州
- 英国南部
- 英国西部
- 美国中西部
- 西欧
- 美国西部
- 美国西部 2


### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>通过公共终结点从 ADF 托管虚拟网络进行出站通信
- 打开所有端口进行出站通信。
- 不支持通过公共终结点从 ADF 托管虚拟网络连接到 Azure 存储和 Azure Data Lake Gen2。

### <a name="linked-service-creation-of-azure-key-vault"></a>为 Azure Key Vault 创建链接服务 
- 为 Azure Key Vault 创建链接服务时，没有 Azure Integration Runtime 引用。 因此，在为 Azure Key Vault 创建链接服务的过程中，无法创建专用终结点。 但是，当你为引用 Azure Key Vault 链接服务的数据存储创建链接服务并且此链接服务引用已启用托管虚拟网络的 Azure Integration Runtime 时，你就可以在创建过程中为 Azure Key Vault 链接服务创建专用终结点。 
- Azure Key Vault 的链接服务的“测试连接”操作仅验证 URL 格式，而不执行任何网络操作。
- 即使你为 Azure Key Vault 创建专用终结点，“使用专用终结点”列也始终显示为空白。

### <a name="linked-service-creation-of-azure-hdi"></a>为 Azure HDI 创建链接服务
- 即使使用专用链接服务和带有端口转发的负载均衡器为 HDI 创建专用终结点，“使用专用终结点”列也始终显示为空白。

:::image type="content" source="./media/managed-vnet/akv-pe.png" alt-text="AKV 的专用终结点":::

## <a name="next-steps"></a>后续步骤

- 教程：[使用托管虚拟网络和专用终结点构建复制管道](tutorial-copy-data-portal-private.md) 
- 教程：[使用托管虚拟网络和专用终结点构建映射数据流管道](tutorial-data-flow-private.md)
