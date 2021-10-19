---
title: 如何跨区域移动服务资源
titleSuffix: Azure Cognitive Search
description: 本文介绍如何在 Azure 云中将 Azure 认知搜索资源从一个区域移动到另一个区域。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 10/06/2021
ms.openlocfilehash: 6dddc7e5a2492aeaf0c15c954f685e10ce475fa7
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612933"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>将 Azure 认知搜索服务移动到另一个 Azure 区域

客户偶尔会询问如何将搜索服务移动到另一个区域。 目前没有内置机制或工具可帮助执行该任务，但本文可帮助你了解在不同区域中的新搜索服务上重新创建索引和其他对象的手动步骤。

> [!NOTE]
> 在 Azure 门户中，所有服务都有 Export template 命令。 对于 Azure 认知搜索，此命令生成服务的基本定义（名称、位置、层、副本和分区计数），但不识别服务的内容，也不移动密钥、角色或日志。 尽管存在该命令，但我们不建议用它来移动搜索服务。

## <a name="prerequisites"></a>先决条件

+ 确保帐户使用的服务和功能在目标区域中受支持。

+ 对于预览功能，请确保订阅在目标区域已获得批准。

## <a name="prepare-and-move"></a>准备并移动

1. 识别依赖项和相关服务，以了解重新定位服务的全部影响，以防需要移动的不仅仅是 Azure 认知搜索。

   Azure 存储用于记录和创建知识库，并且是 AI 扩充和索引的常用外部数据源。 认知服务是 AI 扩充中的依赖项。 如果使用 AI 扩充，认知服务和搜索服务都要求位于相同区域。

1. 为服务上的所有对象创建清单，以了解要移动的内容：索引、同义词映射、索引器、数据源和技能组。 如果已启用日志记录，则请创建历史记录可能需要的所有报表，并将其存档。

1. 查看新区域中的定价和可用性，确保新区域中 Azure 认知搜索以及所有相关服务的可用性。 大多数功能在所有区域都可用，但一些预览功能的可用性受限。

1. 在新区域中创建服务，然后从源代码重新发布任何现有索引、同义词映射、索引器、数据源和技能组。 请记住，服务名称必须是唯一的，因此无法重复使用现有名称。 检查每个技能组，查看与认知服务的连接在相同区域要求下是否仍然有效。 此外，如果已创建知识存储，则在使用其他服务时，请检查 Azure 存储的连接字符串。

1. 重载索引和知识存储（如果适用）。 你可以使用应用程序代码将 JSON 数据推送到索引中，也可以重新运行索引器以从外部源拉取文档。 

1. 启用日志记录，如果要使用日志记录，请重新创建安全角色。

1. 更新客户端应用程序和测试套件以使用新的服务名称和 API 密钥，并对所有应用程序进行测试。

## <a name="discard-or-clean-up"></a>丢弃或清理

新服务经过全面测试并可操作后，删除旧服务。 删除服务将自动删除与该服务相关的所有内容。

## <a name="next-steps"></a>后续步骤

以下链接有助于你在完成上述步骤时查找相关详细信息。

+ [Azure 认知搜索定价和区域](https://azure.microsoft.com/pricing/details/search/)
+ [选择层](search-sku-tier.md)
+ [创建搜索服务](search-create-service-portal.md)
+ [加载搜索文档](search-what-is-data-import.md)
+ [启用日志记录](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is allowlisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->