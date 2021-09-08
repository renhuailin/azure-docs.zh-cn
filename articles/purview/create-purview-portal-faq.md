---
title: 为 Azure Purview 创建 Azure Policy 例外
description: 本文介绍如何为 Purview 创建 Azure Policy 例外，同时保留现有策略以保持安全性。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 10ac52f62da8f8c20ca79ad4d5bb3073eb2bfe69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111902"
---
# <a name="create-an-azure-policy-exception-for-purview"></a>为 Purview 创建 Azure Policy 例外

许多订阅都有 [Azure 策略](../governance/policy/overview.md)，用于限制某些资源的创建。 这是为了保持订阅的安全性和纯净性。 但是，Purview 帐户在创建时会部署另外两个 Azure 资源：Azure 存储帐户和事件中心命名空间。 [创建 Purview 帐户](create-catalog-portal.md)时将部署这些资源。 它们将由 Azure 管理，因此你无需维护它们，但需要部署它们。

若要在订阅中维护策略，但仍允许创建这些托管资源，则可以创建策略例外。

## <a name="create-a-policy-exception-for-purview"></a>为 Purview 创建策略例外

1. 导航到 [Azure 门户](https://portal.azure.com)并搜索“策略”

    :::image type="content" source="media/create-purview-portal-faq/search-for-policy.png" alt-text="屏幕截图，显示了 Azure 门户搜索栏，在其中搜索“策略”关键字。":::

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
