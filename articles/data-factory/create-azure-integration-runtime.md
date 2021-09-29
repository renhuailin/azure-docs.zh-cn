---
title: 创建 Azure 集成运行时
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 Azure 数据工厂和 Azure Synapse Analytics 中创建 Azure 集成运行时，用于复制数据并调度转换活动。
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 09/09/2021
author: lrtoyou1223
ms.author: lle
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: 063d4138a86fb5235562ebb0d2592640f370806e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760289"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>如何创建和配置 Azure 集成运行时
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集成运行时 (IR) 是 Azure 数据工厂和 Synapse 管道用于在不同的网络环境中提供数据集成功能的计算基础结构。 有关 IR 的更多信息，请参阅[集成运行时](concepts-integration-runtime.md)。

Azure IR 提供完全托管的计算，以本机执行数据移动和调度数据转换活动来计算 HDInsight 这样的服务。 它在 Azure 环境中托管，并支持连接到具有公共可访问端点的公共网络环境中的资源。

本文档介绍了如何创建和配置 Azure 集成运行时。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>默认 Azure IR
默认情况下，每个数据工厂或 Synapse 工作区在后端都有一个 Azure IR，可支持云数据存储上的操作和公用网络中的计算服务。 该 Azure IR 的位置会自动解析。 如果链接服务定义中未指定 **connectVia** 属性，则会使用默认的 Azure IR。 如果想要显式定义 IR 的位置，或对不同 IR 上的活动执行进行虚拟分组以进行管理，只需显式创建 Azure IR。 

## <a name="create-azure-ir"></a>创建 Azure IR

若要创建并设置 Azure IR，可以使用以下过程。

### <a name="create-an-azure-ir-via-azure-powershell"></a>通过 Azure PowerShell 创建 Azure IR
可以使用 **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet 创建集成运行时。 若要创建 Azure IR，请指定命令的名称、位置和类型。 下面的示例命令创建了位置设为“西欧”的 Azure IR。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR 的类型必须设置为 **托管**。 无需指定计算详细信息，因为它在云中是完全弹性托管的。 如果要创建 Azure-SSIS IR，请指定计算详细信息，如节点大小和节点数量。 有关详细信息，请参阅[创建和配置 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。

可以使用 Set-AzDataFactoryV2IntegrationRuntime PowerShell cmdlet 来配置现有 Azure IR 以更改其位置。 有关 Azure IR 的位置的详细信息，请参阅[集成运行时简介](concepts-integration-runtime.md)。

### <a name="create-an-azure-ir-via-ui"></a>通过 UI 创建 Azure IR
使用以下步骤通过 UI 来创建 Azure IR。

1. 在服务的主页上，从最左侧的窗格选择[“管理”选项卡](./author-management-hub.md)。

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)
    
    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="主页“管理”按钮":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button-synapse.png" alt-text="主页“管理”按钮":::

2. 在左窗格中选择“集成运行时”，然后选择“+ 新建” 。

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="此屏幕截图突出显示了左窗格中的“集成运行时”和“+新建”按钮。":::
   
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime-synapse.png" alt-text="此屏幕截图突出显示了左窗格中的“集成运行时”和“+新建”按钮。":::

3. 在“集成运行时安装”页中选择“Azure，自承载”，然后选择“继续”。  

1. 在下一页上选择“Azure”以创建 Azure IR，然后选择“继续”。
   :::image type="content" source="media/create-azure-integration-runtime/new-azure-integration-runtime.png" alt-text="创建集成运行时":::

1. 输入 Azure IR 的名称，然后选择“创建”。
   :::image type="content" source="media/create-azure-integration-runtime/create-azure-integration-runtime.png" alt-text="创建 Azure IR":::

1. 创建完成后，会显示一个弹出通知。 在“集成运行时”页上，确保在列表中看到新建的 IR。

## <a name="use-azure-ir"></a>使用 Azure IR

创建 Azure IR 后，您可以在链接的服务定义中引用它。 以下示例说明如何从 Azure 存储链接服务引用上面创建的 Azure 集成运行时：

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>后续步骤
有关如何创建其他类型的集成运行时，请参阅以下文章：

- [创建自承载集成运行时](create-self-hosted-integration-runtime.md)
- [创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)
