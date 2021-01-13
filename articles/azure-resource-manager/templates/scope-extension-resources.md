---
title: 扩展资源类型的范围
description: 介绍如何在部署扩展资源类型时使用 scope 属性。
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 75c2c8b8409cc9f8e7a8e71965589ece6660607a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179973"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>在 ARM 模板中设置扩展资源的范围

扩展资源是用于修改其他资源的资源。 例如，可以将角色分配给资源。 角色分配是扩展资源类型。

有关扩展资源类型的完整列表，请参阅[用于扩展其他资源的功能的资源类型](../management/extension-resource-types.md)。

本文介绍如何在使用 Azure 资源管理器模板（ARM 模板）进行部署时设置扩展资源类型的范围。 它介绍了在应用到资源时可用于扩展资源的 scope 属性。

> [!NOTE]
> 作用域属性仅适用于扩展资源类型。 若要为非扩展类型的资源类型指定不同的作用域，请使用嵌套或链接的部署。 有关详细信息，请参阅 [资源组部署](deploy-to-resource-group.md)、 [订阅部署](deploy-to-subscription.md)、 [管理组部署](deploy-to-management-group.md)和 [租户部署](deploy-to-tenant.md)。

## <a name="apply-at-deployment-scope"></a>在部署范围内应用

若要在目标部署范围内应用扩展资源类型，请将该资源添加到模板中，就像应用任何资源类型一样。 可用的范围是[资源组](deploy-to-resource-group.md)、[订阅](deploy-to-subscription.md)、[管理组](deploy-to-management-group.md)和[租户](deploy-to-tenant.md)。 部署范围必须支持该资源类型。

以下模板会部署一个锁。

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

下一个示例会分配角色。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

部署到订阅时，它会为订阅分配角色。

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

若要将扩展资源应用于资源，请使用 `scope` 属性。 将 scope 属性设置为要将扩展添加到其中的资源的名称。 scope 属性是扩展资源类型的根属性。

下面的示例创建一个存储帐户并对其应用角色。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>后续步骤

* 若要了解如何在模板中定义参数，请参阅[了解 Azure Resource Manager 模板的结构和语法](template-syntax.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。
