---
title: 扩展资源类型的范围
description: 介绍如何在部署扩展资源类型时使用作用域属性。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681426"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>在 ARM 模板中设置扩展资源的作用域

扩展资源是修改其他资源的资源。 例如，可以将角色分配给资源以限制访问。 角色分配是扩展资源类型。

有关扩展资源类型的完整列表，请参阅 [扩展其他资源功能的资源类型](../management/extension-resource-types.md)。

本文介绍如何在使用 Azure 资源管理器模板（ (ARM 模板) 部署时设置扩展资源类型的作用域。 它介绍了在应用到资源时可用于扩展资源的作用域属性。

## <a name="apply-at-deployment-scope"></a>应用于部署范围

若要在目标部署范围内应用扩展资源类型，请将资源添加到模板中，就像任何资源类型一样。 可用范围包括 [资源组](deploy-to-resource-group.md)、 [订阅](deploy-to-subscription.md)、 [管理组](deploy-to-management-group.md)和 [租户](deploy-to-tenant.md)。 部署范围必须支持资源类型。

以下模板将部署一个锁。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

部署到资源组时，它会锁定资源组。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

下一个示例分配一个角色。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

部署到订阅时，会将角色分配给订阅。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>应用于资源

若要将扩展资源应用于资源，请使用 `scope` 属性。 将 "作用域" 属性设置为要将扩展添加到的资源的名称。 作用域属性是扩展资源类型的根属性。

下面的示例创建存储帐户并向其应用角色。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>后续步骤

* 若要了解如何在模板中定义参数，请参阅[了解 Azure Resource Manager 模板的结构和语法](template-syntax.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。
