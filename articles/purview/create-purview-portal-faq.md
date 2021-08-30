---
title: 与在门户中创建 Azure Purview 帐户相关的常见问题解答 (FAQ)
description: 本文解答了通过门户创建 Purview 帐户时的常见问题
author: nayenama
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: bf148408d733dffad862eecf51cc06455846b19a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398127"
---
# <a name="faq-about-creating-purview-accounts-via-portal"></a>通过门户创建 Purview 帐户的常见问题解答

本文解答了客户和现场团队在使用 Azure 门户[创建 Purview 帐户](create-catalog-portal.md)时经常询问的有关如何创建 Purview 帐户的常见问题。

## <a name="common-questions"></a>常见问题

查看以下常见问题的解答。

### <a name="azure-policy-blocking-from-creating-storage-and-event-hub-namespace"></a>Azure 策略阻止创建存储和事件中心命名空间 

* 如果 Azure Policy 阻止所有应用程序创建存储帐户和 EventHub 命名空间，则需要使用标记设置策略例外，可以在创建 Purview 帐户的过程中输入此信息  。 主要原因是，对于创建的每个 Purview 帐户，它需要创建一个托管资源组，并在此资源组中创建存储帐户和 EventHub 命名空间。

   >[!IMPORTANT]
   >如果你没有 Azure Policy 或现有 Azure Policy 未阻止创建存储帐户和 EventHub 命名空间，则无需执行此步骤 。

    1. 导航到 Azure 门户并搜索“策略”
    1. 遵循[创建自定义策略定义](../governance/policy/tutorials/create-custom-policy-definition.md)，或修改现有策略，以使用 `not` 运算符和 `resourceBypass` 标记添加两个例外：

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > 标记可以是 `resourceBypass` 旁边的任何内容，只要策略能够检测到标记，在后续步骤中创建 Purview 时，就可以定义值。

        :::image type="content" source="media/create-catalog-portal/policy-definition.png" alt-text="显示如何创建策略定义的屏幕截图。":::

    1. 使用创建的自定义策略[创建策略分配](../governance/policy/assign-policy-portal.md)。

       :::image type="content" source="media/create-catalog-portal/policy-assignment.png" alt-text="显示如何创建策略分配的屏幕截图" lightbox="./media/create-catalog-portal/policy-assignment.png":::

> [!Note] 
> 如果已有 Azure Policy，并需要在“先决条件”中添加例外，则需要添加正确的标记 。 例如，可以添加 `resourceBypass` 标记：:::image type="content" source="media/create-catalog-portal/add-purview-tag.png" alt-text="将标记添加到 Purview 帐户。":::

## <a name="next-steps"></a>后续步骤

若要使用专用链接设置 Azure Purview，请参阅[为 Azure Purview 帐户使用专用终结点](./catalog-private-link.md)。
