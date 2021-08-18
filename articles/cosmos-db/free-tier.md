---
title: Azure Cosmos DB 免费层
description: 使用 Azure Cosmos DB 免费层开始上手、开发和测试应用程序。 使用免费层，一开始你将在帐户中免费获得 1000 RU/秒的吞吐量和 25 GB 的存储空间。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: sngun
ms.openlocfilehash: 55b37ce3f96bc187e4420fc16924b02639c9a7df
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749940"
---
# <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 免费层 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

使用 Azure Cosmos DB 免费层，可以轻松上手、开发和测试应用程序，甚至免费运行小型生产工作负载。 在帐户上启用免费层后，一开始你将在该帐户中免费获得 1000 RU/秒的吞吐量和 25 GB 的存储空间。 超出这些限制的吞吐量和存储按常规价格计费。 免费层适用于所有具有预配吞吐量、自动缩放吞吐量、单个或多个写入区域的 API 帐户。

免费层在帐户的有效期内无限期提供，并具有常规 Azure Cosmos DB 帐户的各项[优势和功能](introduction.md#key-benefits)。 此类优势包括无限制的存储空间和吞吐量（RU/秒）、SLA、高可用性、支持在所有 Azure 区域进行统包式全局分发等。

每个 Azure 订阅最多支持一个免费层 Azure Cosmos DB 帐户，并且必须在创建帐户时选择启用。 如果看不到用于应用免费层折扣的选项，这意味着订阅中的另一个帐户已启用免费层。 如果创建免费层的帐户，然后将其删除，可以对新帐户应用免费层。 创建新帐户时，建议启用免费层折扣（如果可用）。

> [!NOTE]
> 无服务器帐户目前不提供免费层。

## <a name="free-tier-with-shared-throughput-database"></a>具有共享吞吐量数据库的免费层

在共享吞吐量模型中，对数据库预配吞吐量时，将在该数据库中的所有容器之间共享吞吐量。 使用免费层时，可以为共享数据库免费预配最多 1000 RU/秒的吞吐量。 数据库中的所有容器将共享吞吐量。 

与常规帐户一样，在免费层帐户中，一个共享吞吐量数据库最多可有 25 个容器。 任何具有共享吞吐量的额外数据库或专用吞吐量超过 1000 RU/秒的容器，均按常规定价计费。 在免费层帐户中，最多可以创建 5 个共享吞吐量数据库。

## <a name="free-tier-with-azure-discount"></a>具有 Azure 折扣的免费层

Azure Cosmos DB 免费层与 [Azure 免费帐户](optimize-dev-test.md#azure-free-account)兼容。 若要选择启用，请在 Azure 免费帐户订阅中创建 Azure Cosmos DB 免费层帐户。 在前 12 个月内，你将获得针对 1400 RU/秒吞吐量（Azure Cosmos DB 免费层的 1000 RU/秒和 Azure 免费帐户的 400 RU/秒）和 50 GB 存储（Azure Cosmos DB 免费层的 25 GB 和 Azure 免费帐户的 25 GB）的组合折扣。 12 个月到期后，在 Azure Cosmos DB 帐户有效期内，你可继续享用 Azure Cosmos DB 免费层提供的 1000 RU/秒吞吐量和 25 GB 存储空间。 有关如何叠加计算费用的示例，请参阅[免费层帐户计费示例](understand-your-bill.md#azure-free-tier)。

> [!NOTE]
> Azure Cosmos DB 免费层不同于 Azure 免费账户。 Azure 免费账户在有限时间内免费提供 Azure 额度和资源。 Azure Cosmos DB 作为此免费账户的一部分，在你使用它时会提供 25 GB 的存储和 400 RU/秒的预配吞吐量，使用期限为 12个月。

## <a name="best-practices-to-keep-your-account-free"></a>确保帐户免费的最佳做法

使用 Azure Cosmos DB 免费层时，若要使帐户完全免费，除免费层提供的吞吐量（RU/秒）或存储空间使用量外，帐户不应具有任何额外的吞吐量或存储空间。

例如，以下这些方案不会产生任何每月费用：

* 一个数据库，最大预配吞吐量为 1000 RU/秒。
* 两个容器，一个容器的最大预配吞吐量为 400 RU/秒，另一个为 600 RU/秒。
* 具有 2 个区域的帐户，其中一个区域有一个容器，最大预配吞吐量为 500 RU/秒。

## <a name="create-an-account-with-free-tier"></a>创建免费层帐户

可以使用 Azure 门户、PowerShell、CLI 或 Azure 资源管理器 (ARM) 模板创建免费层帐户。 可以在创建帐户时选择免费层，无法在帐户创建后对其进行设置。

### <a name="azure-portal"></a>Azure 门户

使用 Azure 门户创建帐户时，请将“应用免费层折扣”选项设置为“应用”。 请参阅[使用免费层创建新帐户](create-cosmosdb-resources-portal.md)文章，获取分步指南。

### <a name="arm-template"></a>ARM 模板

若要使用 ARM 模板创建免费层帐户，请设置属性 `"enableFreeTier": true`。 有关完整的模板，请参阅部署[支持免费层的 ARM 模板](manage-with-templates.md#free-tier)示例。

### <a name="cli"></a>CLI

若要使用 CLI 创建免费层帐户，请将 `--enable-free-tier` 参数设置为 true：

```azurecli-interactive
# Create a free tier account for SQL API
az cosmosdb create \
    -n "Myaccount" \
    -g "MyResourcegroup" \
    --enable-free-tier true \
    --default-consistency-level "Session"
    
```

### <a name="powershell"></a>PowerShell

若要使用 Azure PowerShell 创建免费层帐户，请将 `-EnableFreeTier` 参数设置为 true：

```powershell-interactive
# Create a free tier account for SQL API. 
New-AzCosmosDBAccount -ResourceGroupName "MyResourcegroup" `
    -Name "Myaccount" `
    -ApiKind "sql" `
    -EnableFreeTier true `
    -DefaultConsistencyLevel "Session" `
```

## <a name="next-steps"></a>后续步骤

创建免费层帐户后，可以参阅以下文章开始使用 Azure Cosmos DB 构建应用：

* [使用 .NET V4 SDK 构建控制台应用](create-sql-api-dotnet-v4.md)以管理 Azure Cosmos DB 资源。
* [使用适用于 MongoDB 的 Azure Cosmos DB API 构建 .NET Web 应用](mongodb/create-mongodb-dotnet.md)
* [从库中下载笔记本](publish-notebook-gallery.md#download-a-notebook-from-the-gallery)并分析数据。
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
