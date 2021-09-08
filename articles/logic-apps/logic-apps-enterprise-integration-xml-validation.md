---
title: 验证 B2B 企业集成的 XML
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中使用架构验证 XML。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 87650a1ab950f8e88fe08a1c4555c98652776730
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099272"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>在 Azure 逻辑应用中验证工作流的 XML

通常在企业集成企业对企业 (B2B) 方案中，协议中的贸易合作伙伴需确保他们交换的消息有效，才能开始处理数据。 可使用 Azure 逻辑应用中的 XML 验证操作针对预定义的架构验证文档。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 逻辑应用工作流（空白或现有），在其中你希望使用 XML 验证操作。

  如果具有空白工作流，请使用所需的任何触发器。 此示例使用请求触发器。

  如果你刚接触逻辑应用，请查看以下文档：

  * [什么是 Azure 逻辑应用](logic-apps-overview.md)

  * [快速入门：创建第一个逻辑应用工作流](quickstart-create-first-logic-app-workflow.md)

  * [创建单租户逻辑应用工作流](create-single-tenant-workflows-azure-portal.md)

  * [适用于 Azure 逻辑应用的使用量计量、计费和定价模型](logic-apps-pricing.md)

* 如果使用的是逻辑应用（消耗）资源类型，则需要具有满足以下要求的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与你打算在其中使用 XML 验证操作的逻辑应用资源位于同一个位置或 Azure 区域。

  * [链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)到逻辑应用资源。

  * 包含[架构](logic-apps-enterprise-integration-schemas.md)以用于验证 XML 内容。

  如果使用的是逻辑应用（标准）资源类型，则不需要链接集成帐户。 然而，仍必须添加[架构](logic-apps-enterprise-integration-schemas.md)以用于验证逻辑应用资源的 XML 内容。 你可以在“架构”部分“设置”下的逻辑应用资源的菜单上完成此任务 。

## <a name="add-xml-validation-action"></a>添加 XML 验证操作

1. 在 [Azure 门户](https://portal.azure.com)的设计器视图中打开逻辑应用和工作流。

1. 如果有一个没有触发器的空白逻辑应用，请添加所需的任何触发器。 此示例使用请求触发器。 否则，继续执行下一步。

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

   动态内容列表显示了属性标记，这些标记表示工作流中前面步骤的输出。 如果列表不显示预期属性，请检查列表中的触发器或操作标题，以及你是否可以选择“查看详细信息”。

   对于基于消耗或 ISE 计划的逻辑应用，设计器如下例所示：

   ![显示多租户设计器的屏幕截图，其中打开了动态内容列表，光标在“内容”框中，并打开了动态内容列表。](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   对于基于计划的标准逻辑应用，设计器如下例所示：

   ![显示单租户设计器的屏幕截图，其中打开了动态内容列表，光标在“内容”框中，并打开了动态内容列表](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. 从动态内容列表中，选择要验证的内容的属性令牌。

   此示例从触发器中选择“正文”令牌。

1. 若要指定要用于验证的架构，请打开“架构名称”列表，然后选择之前添加的架构。

1. 完成后，请务必保存逻辑应用工作流。

   你现在已设置好 XML 验证操作。 在实际应用中，可能需要将已验证的数据存储在业务线 (LOB) 应用（如 SalesForce）中。 要将已验证的输出发送到 Salesforce，请添加 Salesforce 操作。

1. 若要测试验证操作，请触发并运行工作流。 例如，对于请求触发器，将请求发送到触发器的终结点 URL。

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中添加用于 XML 验证的架构](logic-apps-enterprise-integration-schemas.md)
* [在 Azure 逻辑应用中转换工作流的 XML](logic-apps-enterprise-integration-transform.md)