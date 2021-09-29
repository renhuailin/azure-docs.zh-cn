---
title: 教程 - 在 Azure API 管理中创建和发布产品
description: 本教程将在 Azure API 管理中创建和发布产品。 产品发布后，开发人员便可以开始使用其 API。
author: dlepow
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: f9d4b1565cc217aaa149e34fc278da779a3d869e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643320"
---
# <a name="tutorial-create-and-publish-a-product"></a>教程：创建和发布产品  

在 Azure API 管理中，一个[产品](api-management-terminology.md#term-definitions)包含一个或多个 API 以及使用配额和使用条款。 一旦产品发布，开发人员可以订阅该产品，并开始使用产品的 API。  

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建和发布产品
> * 将 API 添加到产品

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="门户中的 API 管理产品":::


## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

## <a name="create-and-publish-a-product"></a>创建和发布产品

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录到 Azure 门户，并导航到 API 管理实例。
1. 在左侧导航栏中，选择“产品” > “+ 添加” 。
1.  在“添加产品”窗口中，输入下表中描述的值以创建产品。

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="在门户中添加产品":::

    | 名称                     | 说明                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 显示名称             | 要在[开发人员门户](api-management-howto-developer-portal.md)中显示的名称。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | 说明              | 提供有关该产品的信息，如其用途、它提供访问权的 API 和其他详细信息。                                                                                                                                               |
    | 州省/自治区/直辖市                    | 如果希望发布该产品，请选择“发布”。 在产品中的 API 可调用前，必须先发布该产品。 默认情况下新产品未发布，并仅对“管理员”组可见。                                                                                      |
    | 需要订阅    | 如果需要用户订阅才能使用该产品，请选中该项。                                                                                                                                                                                                                                   |
    | 需要批准        | 如果希望管理员审查并接受或拒绝此产品的订阅尝试，请选中该项。 如果未选中此项，则会自动批准订阅尝试。                                                                                                                         |
    | 订阅计数限制 | （可选）限制多个同步订阅的计数。                                                                                                                                                                                                                                |
    | 法律条款              | 可以包含产品的使用条款，订阅者必须接受这些条款才能使用该产品。                                                                                                                                                                                                             |
    | API                     | 选择一个或多个 API。 还可以在创建产品后添加 API。 有关详细信息，请参阅本文稍后的[将 API 添加到产品](#add-apis-to-a-product)。 |

3. 选择“创建”以创建新产品。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要开始使用 Azure CLI：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

若要创建产品，请运行 [az apim product create](/cli/azure/apim/product#az_apim_product_create) 命令：

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

可针对你的产品修改各个值：

   | 参数 | 说明 |
   |-----------|-------------|
   | `--product-name` | 要在[开发人员门户](api-management-howto-developer-portal.md)中显示的名称。 |
   | `--description`  | 提供有关该产品的信息，如其用途、它提供访问权的 API 和其他详细信息。 |
   | `--state`        | 如果希望发布该产品，请选择“发布”。 在产品中的 API 可调用前，必须先发布该产品。 默认情况下新产品未发布，并仅对“管理员”组可见。 |
   | `--subscription-required` | 如果需要用户订阅才能使用该产品，请选中该项。 |
   | `--approval-required` | 如果希望管理员审查并接受或拒绝此产品的订阅尝试，请选中该项。 如果未选中此项，则会自动批准订阅尝试。 |
   | `--subscriptions-limit` | （可选）限制多个同步订阅的计数。|
   | `--legal-terms`         | 可以包含产品的使用条款，订阅者必须接受这些条款才能使用该产品。 |

若要查看你的当前产品，请使用 [az apim product list](/cli/azure/apim/product#az_apim_product_list) 命令：

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

若要删除产品，可使用 [az apim product delete](/cli/azure/apim/product#az_apim_product_delete) 命令：

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>添加更多配置

保存产品后继续对其进行配置。 在 API 管理实例中，从“产品”窗口中选择产品。 添加或更新：

|项   |说明  |
|---------|---------|
|设置     |    产品元数据和状态     |
|API     |  与产品关联的 API       |
|[策略](api-management-howto-policies.md)     |  应用于产品 API 的策略      |
|访问控制     |  对开发人员或来宾可见的产品       |
|[订阅](api-management-subscriptions.md)    |    产品订阅者     |

## <a name="add-apis-to-a-product"></a>将 API 添加到产品

产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 可以在创建产品期间添加一个或多个现有的 API。 之后，也可以通过产品的“设置”页或在创建 API 时将 API 添加到产品。

开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。

### <a name="add-an-api-to-an-existing-product"></a>将 API 添加到现有产品

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 API 管理实例的左侧导航栏中，选择“产品”。
1. 选择一个产品，然后选择“API”。
1. 选择“+ 添加”  。
1. 选择一个或多个 API，然后点击“选择”。

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="将 API 添加到现有产品":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 若要查看托管的 API，请使用 [az apim api list](/cli/azure/apim/api#az_apim_api_list) 命令：

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. 若要向产品添加 API，请运行 [az apim product api add](/cli/azure/apim/product/api#az_apim_product_api_add) 命令：

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. 若要验证添加项，请使用 [az apim product api list](/cli/azure/apim/product/api#az_apim_product_api_list) 命令：

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

若要从产品中删除 API，可使用 [az apim product api delete](/cli/azure/apim/product/api#az_apim_product_api_delete) 命令：

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> 可以通过 [REST API](/rest/api/apimanagement/2020-12-01/subscription/create-or-update) 或 PowerShell 命令使用自定义订阅密钥来创建或更新用户对产品的订阅。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建和发布产品
> * 将 API 添加到产品

转到下一教程：

> [!div class="nextstepaction"]
> [创建空白 API 和模拟 API 响应](mock-api-responses.md)
