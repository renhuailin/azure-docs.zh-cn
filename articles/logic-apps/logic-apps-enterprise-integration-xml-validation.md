---
title: 在企业集成工作流中验证 XML
description: 使用 Azure 逻辑应用和 Enterprise Integration Pack 在工作流中通过架构验证 XML。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: f295054913dbf275533d4d14f39497071c6984a8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353221"
---
# <a name="validate-xml-in-workflows-with-azure-logic-apps"></a>使用 Azure 逻辑应用在工作流中验证 XML

在企业集成企业对企业 (B2B) 方案中，协议中的贸易合作伙伴通常需确保他们交换的消息有效，才能开始处理数据。 逻辑应用工作流可以使用 XML 验证操作和预定义[架构](logic-apps-enterprise-integration-schemas.md)来验证 XML 消息和文档。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用和 Enterprise Integration Pack 构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 逻辑应用工作流（空白或现有），在其中你希望使用 XML 验证操作。

  如果具有空白工作流，请使用所需的任何触发器。 此示例使用请求触发器。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与你打算在其中使用 XML 验证* 操作的逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，则集成帐户需要以下项：

    * [架构](logic-apps-enterprise-integration-schemas.md)，用于验证 XML 内容。

    * [逻辑应用资源的链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)。

  * 如果使用的是[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，则不在集成帐户中存储架构， 但可以使用 Azure 门户或 Visual Studio Code [将架构直接添加到逻辑应用资源](logic-apps-enterprise-integration-schemas.md)。 然后，可以在同一逻辑应用资源中跨多个工作流使用这些架构。

    你仍需要一个集成帐户，用来存储其他项目（例如合作伙伴、协议和证书），以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 但是，你不需要将逻辑应用资源链接到集成帐户，因此链接功能不存在。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

    > [!NOTE]
    > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

## <a name="add-xml-validation-action"></a>添加 XML 验证操作

1. 在 [Azure 门户](https://portal.azure.com)的设计器视图中打开逻辑应用和工作流。

1. 如果你有一个没有触发器的空白逻辑应用，请添加所需的任何触发器。 本例中使用的是请求触发器。 否则，继续执行下一步。

   若要添加请求触发器，请在设计器搜索框中输入 `HTTP request`，然后选择名为“收到 HTTP 请求时”的请求设计器。

1. 在你想要添加 XML 验证操作的工作流的步骤下，选择以下步骤之一：

   对于基于消耗或 ISE 计划的逻辑应用，请选择一个步骤：

   * 若要在工作流末尾添加 XML 验证操作，请选择“新建步骤” 。

   * 若要在现有步骤之间添加 XML 验证操作，请将指针移到连接这些步骤的箭头上，以便显示加号 (+) 。 选择该加号，然后选择“添加操作”。 

   对于基于计划的标准逻辑应用，请选择一个步骤：

   * 若要在工作流末尾添加 XML 验证操作，请选择加号 (+)，然后选择“添加操作”  。

   * 若要在现有步骤之间添加 XML 验证操作，请选择出现在这些步骤之间的加号 (+)，然后选择“添加操作”  。

1. 在“选择操作”下，选择“内置”。  在搜索框中输入 `xml validation`。 从操作列表中选择“XML 验证”  。

1. 若要指定验证的 XML 内容，请在“内容”框中单击，以显示动态内容列表。

   动态内容列表显示了属性标记，这些标记表示工作流中前面步骤的输出。 如果列表不显示预期属性，请检查列表中的触发器或操作标题，并看看你是否可以选择“查看详细信息”。

   对于基于消耗或 ISE 计划的逻辑应用，设计器如下面的示例所示：

   ![显示多租户设计器的屏幕截图，其中打开了动态内容列表，光标位于“内容”框中，并打开了动态内容列表。](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   对于基于标准计划的逻辑应用，设计器如下面的示例所示：

   ![显示单租户设计器的屏幕截图，其中打开了动态内容列表，光标位于“内容”框中，并打开了动态内容列表](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. 从动态内容列表中，选择要验证的内容的属性令牌。

   此示例从触发器中选择“正文”令牌。

1. 若要指定要用于验证的架构，请打开“架构名称”列表，然后选择之前添加的架构。

1. 完成后，请务必保存逻辑应用工作流。

   你现在已设置好 XML 验证操作。 在实际应用中，可能需要将已验证的数据存储在业务线 (LOB) 应用（如 SalesForce）中。 要将已验证的输出发送到 Salesforce，请添加 Salesforce 操作。

1. 若要测试验证操作，请触发并运行工作流。 例如，对于请求触发器，将请求发送到触发器的终结点 URL。

   XML 验证操作在工作流被触发后运行，以及在 XML 内容可用于验证时运行。

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中添加用于 XML 验证的架构](logic-apps-enterprise-integration-schemas.md)
* [在 Azure 逻辑应用中转换工作流的 XML](logic-apps-enterprise-integration-transform.md)
