---
title: 使用 Liquid 模板转换 JSON 和 XML
description: 通过使用 Liquid 模板作为 Azure 逻辑应用中的映射，来转换 JSON 和 XML。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/25/2021
ms.openlocfilehash: 01b5e9930353078406751001300a1baa3c23cf00
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690567"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>使用 Liquid 模板作为 Azure 逻辑应用中的映射来转换 JSON 和 XML

若要在逻辑应用中执行基本 JSON 转换，可以使用本机[数据操作](../logic-apps/logic-apps-perform-data-operations.md)，例如“撰写”或“分析 JSON”。 对于包含迭代、控制流和变量等元素的高级、复杂的 JSON 到 JSON 转换，请利用 [Liquid](https://shopify.github.io/liquid/) 开源模板语言来创建描述这些转换的模板并加以使用。 还可以[执行其他转换](#other-transformations)，例如 JSON 到文本、XML 到 JSON 以及 XML 到文本。

在逻辑应用中执行 Liquid 转换之前，必须先创建一个 Liquid 模板来定义所需的映射。 然后，[将模板作为映射上传](../logic-apps/logic-apps-enterprise-integration-maps.md)到[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)中。 向逻辑应用添加“将 JSON 转换为 JSON - Liquid”操作时，可以选择 Liquid 模板作为要使用的操作映射。

本文介绍了如何完成以下任务：

* 创建 Liquid 模板。
* 将模板添加到集成帐户。
* 将 Liquid 转换操作添加到逻辑应用。
* 选择模板作为要使用的映射。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 关于 [Liquid 模板语言](https://shopify.github.io/liquid/)的基本知识

  > [!NOTE]
  > “将 JSON 转换为 JSON - Liquid”操作遵循 [Liquid 的 DotLiquid 实现](https://github.com/dotliquid/dotliquid)，该实现在特定情况下不同于 [Liquid 的 Shopify 实现](https://shopify.github.io/liquid)。 有关详细信息，请参阅 [Liquid 模板注意事项](#liquid-template-considerations)。

## <a name="create-the-template"></a>创建模板

1. 创建要用作 JSON 转换映射的 Liquid 模板。 可以使用任何想要的编辑工具。

   对于此示例，请按照本部分中所述创建示例 Liquid 模板：

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. 使用 `.liquid` 扩展名保存模板。 本示例使用 `SimpleJsonToJsonTemplate.liquid`。

## <a name="upload-the-template"></a>上传模板

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中输入 `integration accounts`，然后选择“集成帐户”。

   ![查找“集成帐户”](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 找到并选择你的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. 在“概览”窗格的“组件”下，选择“映射”。

    ![选择“映射”磁贴](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. 在“映射”窗格上选择“添加”，并为映射提供以下详细信息：

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | `JsonToJsonTemplate` | 映射的名称，在此示例中为“JsonToJsonTemplate” |
   | **映射类型** | **liquid** | 你的映射的类型。 对于 JSON 到 JSON 转换，必须选择“liquid”。 |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | 用于转换的现有 Liquid 模板或映射文件，在此示例中为“SimpleJsonToJsonTemplate.liquid”。 若要查找此文件，可使用文件选取器。 有关映射大小限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)。 |
   |||

   ![添加 Liquid 模板](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>添加 Liquid 转换操作

1. 在 Azure 门户中，执行以下步骤[创建空的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在逻辑应用设计器中，向逻辑应用中添加[请求触发器](../connectors/connectors-native-reqres.md#add-request)。

1. 在触发器下，选择“新建步骤”。 在搜索框中，输入 `liquid` 作为筛选器，然后选择以下操作：**将 JSON 转换为 JSON - Liquid**

   ![查找并选择 Liquid 操作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. 打开“映射”列表，选择 Liquid 模板，即此示例中的“JsonToJsonTemplate”。

   ![选择映射](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果映射列表为空，很可能是因为逻辑应用未链接到集成帐户。 
   要将逻辑应用链接到具有 Liquid 模板或映射的集成帐户，请执行以下步骤：

   1. 在逻辑应用菜单中选择“工作流设置”。

   1. 从“选择集成帐户”列表中选择集成帐户，并选择“保存” 。

      ![将逻辑应用链接到集成帐户](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 现在，将 Content 属性添加到此操作。 打开“添加新参数”列表，选择“Content”。 

   ![向操作添加“Content”属性](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. 若要设置“Content”属性值，请在“内容”框中单击，以便显示动态内容列表。 选择“Body”标记（表示从触发器输出的正文内容）。

   ![为“Content”属性值选择“Body”标记](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   完成后，此操作如以下示例所示：

   ![已完成“将 JSON 转换为 JSON”操作](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>测试逻辑应用

1. 通过使用 [Postman](https://www.getpostman.com/postman) 或类似的工具和 `POST` 方法，向请求触发器的 URL 发送调用并包含要转换的 JSON 输入，例如：

   ```json
   {
      "devices": "Surface, Windows Phone, Desktop computer, Monitors",
      "firstName": "Dean",
      "lastName": "Ledet",
      "phone": "(111)5551111"
   }
   ```

1. 工作流完成运行后，转到工作流的运行历史记录，并检查“将 JSON 转换为 JSON”操作的输入和输出，例如：

   ![示例输出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Liquid 模板注意事项

* 在 Azure 逻辑应用中，Liquid 模板遵循[映射的文件大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)。

* “将 JSON 转换为 JSON - Liquid”操作遵循 [Liquid 的 DotLiquid 实现](https://github.com/dotliquid/dotliquid)。 此实现是从 [Liquid 的 Shopify 实现](https://shopify.github.io/liquid/)到 .NET Framework 的端口，在[特定情况下](https://github.com/dotliquid/dotliquid/issues)有所不同。

  已知区别包括：

  * “将 JSON 转换为 JSON - Liquid”操作是以本机方式输出字符串，其中可能包括 JSON、XML、HTML 等。 该 Liquid 操作仅指示来自 Liquid 模板的预期文本输出为 JSON 字符串。 该操作指示逻辑应用将输入作为 JSON 对象进行分析，并应用包装器以使 Liquid 可以解释 JSON 结构。 转换后，该操作会指示逻辑应用将来自 Liquid 的文本输出重新分析为 JSON。

    DotLiquid 不能以本机方式理解 JSON，因此请确保对反斜杠字符 (`\`) 和其他任何保留的 JSON 字符进行转义。

  * 如果模板使用了 [Liquid 筛选器](https://shopify.github.io/liquid/basics/introduction/#filters)，请确保遵循 [DotLiquid 和 C# 命名约定](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)，该约定要求“句子首字母大写”。 对于所有 Liquid 转换，请确保模板中的筛选器名称也遵循句子首字母大写。 否则，筛选器将不起作用。

    例如，使用 `replace` 筛选器时，请使用 `Replace`，而不是 `replace`。 如果是在 [DotLiquid online](http://dotliquidmarkup.org/try-online) 上试用示例，也适用相同的规则。 有关详细信息，请参阅 [Shopify Liquid 筛选器](https://shopify.dev/docs/themes/liquid/reference/filters)和 [DotLiquid Liquid 筛选器](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)。 Shopify 规范中包含了每个筛选器的示例，因此可以在 [DotLiquid - 在线试用](http://dotliquidmarkup.org/try-online)上试用这些示例，以作比较。

  * Shopify 扩展筛选器中的 `json` 筛选器当前[未在 DotLiquid 中实现](https://github.com/dotliquid/dotliquid/issues/384)。 通常情况下，可以使用此筛选器来准备用于 JSON 字符串分析的文本输出，但现在需要改用 `Replace` 筛选器。

  * [DotLiquid 实现](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425)中的标准 `Replace` 筛选器使用[正则表达式 (RegEx) 匹配](/dotnet/standard/base-types/regular-expression-language-quick-reference)，[Shopify 实现](https://shopify.github.io/liquid/filters/replace/)则使用[简单字符串匹配](https://github.com/Shopify/liquid/issues/202)。 这两种实现看似工作原理一样，但是当在匹配参数中使用 RegEx 保留字符或转义字符时，区别就显现出来了。

    例如，若要对 RegEx 保留的反斜杠 (`\`) 转义字符进行转义，请使用 `| Replace: '\\', '\\'`，而不是 `| Replace: '\', '\\'`。 以下示例显示了在尝试对反斜杠字符进行转义时，`Replace` 筛选器的行为方式有何不同。 此版本运行成功时：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    显示此结果：

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    此版本失败时：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    显示此错误：

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    有关详细信息，请参阅[替换标准筛选器使用 RegEx 模式匹配...](https://github.com/dotliquid/dotliquid/issues/385)。

  * [DotLiquid 实现](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326)中的 `Sort` 筛选器按属性对数组或集合中的各项进行排序，但存在以下差异：<p>

    * 遵循 [Shopify 的 sort_natural 行为](https://shopify.github.io/liquid/filters/sort_natural/)，而非 [Shopify 的 sort 行为](https://shopify.github.io/liquid/filters/sort/)。

    * 仅按字符串字母数字顺序排序。 有关详细信息，请参阅[数值排序](https://github.com/Shopify/liquid/issues/980)。

    * 使用“不区分大小写”的顺序，而不是区分大小写。 有关详细信息，请参阅 [Sort filter does not follow casing behavior from Shopify's specification]( https://github.com/dotliquid/dotliquid/issues/393)（Sort 筛选器不遵循 Shopify 规范中的大小写行为）。

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>使用 Liquid 的其他转换

Liquid 并不仅限于 JSON 转换。 还可以使用 Liquid 来执行其他转换，例如：

* [JSON 到文本](#json-text)
* [XML 到 JSON](#xml-json)
* [XML 到文本](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>将 JSON 转换为文本

下面是用于此示例的 Liquid 模板：

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

下面是示例输入和输出：

![将 JSON 输出为文本的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>将 XML 转换为 JSON

下面是用于此示例的 Liquid 模板：

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor` 循环是用于 XML 输入的自定义循环机制，允许创建避免尾部逗号的 JSON 有效负载。 此外，此自定义循环机制的 `where` 条件使用 XML 元素的名称进行比较，而不是像其他 Liquid 筛选器一样使用元素的值。 有关详细信息，请参阅[深度学习 set-body 策略 - 事项集合](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)。

下面是示例输入和输出：

![将 XML 输出为 JSON 的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>将 XML 转换为文本

下面是用于此示例的 Liquid 模板：

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

下面是示例输入和输出：

![将 XML 输出为文本的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>后续步骤

* [Shopify Liquid 语言和示例](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid - 在线试用](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub 问题](https://github.com/dotliquid/dotliquid/issues/)
* 详细了解[映射](../logic-apps/logic-apps-enterprise-integration-maps.md)
