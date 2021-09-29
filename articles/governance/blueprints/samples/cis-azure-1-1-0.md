---
title: CIS Microsoft Azure 基础基准检验 v1.1.0 蓝图示例
description: CIS Microsoft Azure 基础基准检验 v1.1.0 蓝图示例的概述。 此蓝图示例可帮助客户评估特定控制要求。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 8b9797691a84bf0aad0a50c604d53296b89d89bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614686"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v110-blueprint-sample"></a>CIS Microsoft Azure 基础基准检验 v1.1.0 蓝图示例

CIS Microsoft Azure 基础基准检验 v1.1.0 蓝图示例使用 [Azure Policy](../../policy/overview.md) 提供治理防护措施，帮助你评估特定 CIS Microsoft Azure 基础基准检验建议。 对于 Azure 部署的任何必须实施 CIS Microsoft Azure 基础基准检验 v1.1.0 建议的体系结构，此蓝图可帮助客户为其部署一组核心策略。

## <a name="recommendation-mapping"></a>建议映射

可在 [Azure Policy 建议映射](../../policy/samples/cis-azure-1-1-0.md)中详细了解此蓝图中包含的策略定义，以及这些策略定义映射到 CIS Microsoft Azure 基础基准检验 v1.1.0 中的建议的方式。 在将资源分配给体系结构时，Azure Policy 会评估这些资源是否不符合已分配的策略定义。 有关详细信息，请参阅 [Azure Policy](../../policy/overview.md)。

## <a name="deploy"></a>部署

若要部署 Azure 蓝图 CIS Microsoft Azure 基础基准检验 v1.1.0 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

### <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。 

1. 在“其他示例”下找到“CIS Microsoft Azure 基础基准 v1.1.0”蓝图示例，然后选择“使用此示例”。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：为 CIS Microsoft Azure 基础基准蓝图示例提供一个名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看蓝图示例中所包含项目的列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

### <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其 **发布**，然后才能分配和部署它。  可根据环境和需求自定义蓝图示例的副本，但这种修改可能不符合 CIS Microsoft Azure 基础基准检验 v1.1.0 建议。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的 **版本**。 以后做出修改时，此属性非常有用。 提供更改注释，例如，“基于 CIS Microsoft Azure 基础基准蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。

### <a name="assign-the-sample-copy"></a>分配示例副本

成功 **发布** 蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。

1. 提供蓝图分配的参数值：

   - 基础

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的 **已发布** 版本。

   - 锁分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../concepts/resource-locking.md)。

   - 托管标识

     保留默认的系统分配的托管标识选项。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。  随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是 **免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

### <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|审核 CIS Microsoft Azure 基础基准检验 v1.1.0 建议，并部署特定的支持 VM 扩展|策略分配|应启用网络观察程序的区域的列表|以分号分隔的区域列表。 若要查看区域的完整列表，请使用 Get-AzLocation。 例如：eastus; eastus2|
|审核 CIS Microsoft Azure 基础基准检验 v1.1.0 建议，并部署特定的支持 VM 扩展|策略分配|已批准使用的虚拟机扩展的列表|以分号分隔的扩展列表。 若要查看虚拟机扩展的完整列表，请使用 Get-AzVMExtensionImage。 例如：AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>后续步骤

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
