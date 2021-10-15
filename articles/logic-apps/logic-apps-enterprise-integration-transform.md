---
title: 在企业集成工作流中转换 XML
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中使用映射转换 XML。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 84d0e0b509505e6d56e39294b3e2819e4b03e024
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350016"
---
# <a name="transform-xml-in-workflows-with-azure-logic-apps"></a>使用 Azure 逻辑应用在工作流中转换 XML

在企业集成企业到企业 (B2B) 应用场景中，可能需要在不同格式之间转换 XML。 逻辑应用工作流可以使用“转换 XML”操作和预定义[映射](logic-apps-enterprise-integration-maps.md)来转换XML。 例如，假设你从使用 YearMonthDay 日期格式 (YYYYMMDD) 的客户那里定期接收 B2B 订单或发票。 但是，你的组织使用的是 MonthDayYear 日期格式 (MMDDYYYY)。 在将订单或发票详细信息存储在客户活动数据库中之前，可以创建并使用一个映射，将 YearMonthDay 格式转换为 MonthDayYear 格式。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用和 Enterprise Integration Pack 构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个逻辑应用工作流，已在开始时创建触发器，便于你在工作流中必要时添加“转换 XML”操作。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与你计划在其中使用“转换 XML”操作的逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，则集成帐户需要以下项：

    * 用于转换 XML 内容的[映射](logic-apps-enterprise-integration-maps.md)。

    * [逻辑应用资源的链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)。

  * 如果使用的是[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，则不在集成帐户中存储映射， 但可以使用 Azure 门户或 Visual Studio Code [将映射直接添加到逻辑应用资源](logic-apps-enterprise-integration-maps.md)。 目前仅支持 XSLT 1.0。 然后，可以在同一逻辑应用资源内的多个工作流中使用这些映射。

    你仍需要一个集成帐户，用来存储其他项目（例如合作伙伴、协议和证书），以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 但是，你不需要将逻辑应用资源链接到集成帐户，因此链接功能不存在。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

    > [!NOTE]
    > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

## <a name="add-transform-xml-action"></a>添加“转换 XML”操作

1. 在 [Azure 门户](https://portal.azure.com)的设计器视图中打开逻辑应用和工作流。

1. 如果你有一个没有触发器的空白逻辑应用，请添加所需的任何触发器。 本例中使用的是请求触发器。 否则，继续执行下一步。

   若要添加请求触发器，请在设计器搜索框中输入 `HTTP request`，然后选择名为“收到 HTTP 请求时”的请求设计器。

1. 在你想要添加“转换 XML”操作的工作流的步骤下，选择以下步骤之一：

   对于基于消耗或 ISE 计划的逻辑应用，请选择一个步骤：

   * 若要在工作流末尾添加“转换 XML”操作，请选择“新建步骤”。

   * 若要在现有步骤之间添加“转换 XML”操作，请将指针移到连接这些步骤的箭头上，以便显示加号 (+)。 选择该加号，然后选择“添加操作”。 

   对于基于标准计划的逻辑应用，请选择一个步骤：

   * 若要在工作流末尾添加“转换 XML”操作，请选择加号 (+)，然后选择“添加操作”。

   * 若要在现有步骤之间添加“转换 XML”操作，请选择出现在这些步骤之间的加号 (+)，然后选择“添加操作”。

1. 在“选择操作”下，选择“内置”。  在搜索框中输入 `transform xml`。 从操作列表中选择“转换 XML”。

1. 若要指定用于转换的 XML 内容，可以使用你在 HTTP 请求中收到的任何 XML 数据。 在“内容”框中单击，以显示动态内容列表。

   动态内容列表显示了属性标记，这些标记表示工作流中前面步骤的输出。 如果列表不显示预期属性，请检查列表中的触发器或操作标题，并看看你是否可以选择“查看详细信息”。

   对于基于消耗或 ISE 计划的逻辑应用，设计器如下面的示例所示：

   ![显示多租户设计器的屏幕截图，其中打开了动态内容列表，光标位于“内容”框中，并打开了动态内容列表。](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-multi-tenant.png)

   对于基于标准计划的逻辑应用，设计器如下面的示例所示：

   ![显示单租户设计器的屏幕截图，其中打开了动态内容列表，光标位于“内容”框中，并打开了动态内容列表](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-single-tenant.png)

1. 从动态内容列表中，选择要验证的内容的属性令牌。

   本示例从触发器中选择“正文”令牌。

   > [!NOTE]
   > 请确保所选内容是 XML。 如果内容不是 XML，或者是 base64 编码的，则必须指定一个用于处理该内容的表达式。 例如，可以使用[表达式函数](workflow-definition-language-functions-reference.md)（如 `base64ToBinary()`）来解码内容，或使用 `xml()` 来处理 XML 格式的内容。

1. 若要指定用于转换的映射，请打开“映射”列表，然后选择之前添加的映射。

1. 完成后，请务必保存逻辑应用工作流。

   你现在已经设置好了“转换 XML”操作。 在实际应用中，可能需要将已转换的数据存储在业务线 (LOB) 应用（如 SalesForce）中。 若要将已转换的输出发送到 Salesforce，请添加 Salesforce 操作。

1. 若要测试转换操作，请触发并运行工作流。 例如，对于请求触发器，将请求发送到触发器的终结点 URL。

   “转换 XML”操作在工作流被触发后运行，并且在 XML 内容可用于转换时运行。

## <a name="advanced-capabilities"></a>高级功能

### <a name="reference-assembly-or-custom-code-from-maps"></a>映射中的引用程序集或自定义代码

在“逻辑应用（消耗）”工作流中，“转换 XML”操作支持引用外部程序集的映射。 有关详细信息，请参阅[在 Azure 逻辑应用中添加工作流的 XSLT 映射](logic-apps-enterprise-integration-maps.md#add-assembly)。

### <a name="byte-order-mark"></a>字节顺序标记

默认情况下，转换的响应会以字节顺序标记 (BOM) 开头。 只有在代码视图编辑器中工作时，才能访问此功能。 若要禁用此功能，请将 `transformOptions` 属性设置为 `disableByteOrderMark`：

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中添加用于 XML 转换的 XSLT 映射](logic-apps-enterprise-integration-maps.md)
* [在 Azure 逻辑应用中验证工作流的 XML](logic-apps-enterprise-integration-xml-validation.md)
