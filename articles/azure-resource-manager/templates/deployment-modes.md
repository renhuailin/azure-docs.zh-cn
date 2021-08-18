---
title: 部署模式
description: 介绍如何使用 Azure 资源管理器指定是使用完整部署模式还是增量部署模式。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 98e92cfc58acd85d1ed49cec0e29d1d73b5b8efb
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112320867"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure 资源管理器部署模式

部署资源时，可以指定部署为增量更新还是完整更新。 这两种模式之间的区别在于资源管理器如何处理资源组中不在模板中的现有资源。

对于这两种模式，资源管理器都会尝试创建模板中指定的所有资源。 如果资源已存在于资源组中且其设置未更改，则不会对该资源执行任何操作。 如果更改资源的属性值，则使用这些新值更新资源。 如果尝试更新现有资源的位置或类型，则部署会失败并出现错误。 请改用所需的位置或类型部署新资源。

默认模式为增量模式。

## <a name="complete-mode"></a>完整模式

在完整模式下，资源管理器删除资源组中已存在但尚未在模板中指定的资源  。

> [!NOTE]
> 在完全模式下部署模板之前，请始终使用 [what-if 操作](./deploy-what-if.md)。 如果显示哪些资源将被创建、删除或修改。 使用 what-if 以避免意外删除资源。

如果模板包含由于[条件](conditional-resource-deployment.md)的计算结果为 false 而未部署的资源，则结果取决于用于部署模板的 REST API 版本。 如果使用 2019-05-10 之前的版本，则 **不会删除** 该资源。 如果使用 2019-05-10 或更高版本，则 **会删除** 该资源。 最新版本的 Azure PowerShell 和 Azure CLI 会删除该资源。

将完整模式与[复制循环](copy-resources.md)一起使用时要小心。 在解析复制循环后会删除模板中未指定的任何资源。

如果部署到[模板中的多个资源组](./deploy-to-resource-group.md)，则可以删除部署操作中指定的资源组中的资源。 辅助资源组中的资源不会被删除。

资源类型处理完整模式删除的方式有所不同。 当父资源不在以完整模式部署的模板中时，将自动删除该资源。 而某些子资源不在模板中时，不会将其自动删除。 但是，如果删除父资源，则会删除这些子资源。

例如，如果资源组包含 DNS 区域（`Microsoft.Network/dnsZones` 资源类型）和 CNAME 记录（`Microsoft.Network/dnsZones/CNAME` 资源类型），则 DNS 区域是 CNAME 记录的父资源。 如果使用完整模式部署并且模板中不包含 DNS 区域，则 DNS 区域和 CNAME 记录都将被删除。 如果在模板中包含 DNS 区域但不包含 CNAME 记录，则不会删除 CNAME。

有关资源类型如何处理删除的列表，请参阅[针对完全模式部署的 Azure 资源删除](./deployment-complete-mode-deletion.md)。

如果资源组被[锁定](../management/lock-resources.md)，则完整模式不会删除资源。

> [!NOTE]
> 仅根级别模板支持完整部署模式。 对于[链接模板或嵌套模板](linked-templates.md)，必须使用增量模式。
>
> [订阅级别部署](deploy-to-subscription.md)不支持完整模式。
>
> 目前，门户不支持完整模式。
>

## <a name="incremental-mode"></a>增量模式

在增量模式下，资源管理器保留资源组中已存在但尚未在模板中指定的未更改资源。 模板中的资源添加到资源组。

> [!NOTE]
> 在增量模式下重新部署现有资源时，所有属性都将重新应用。 **属性不会以增量方式添加**。 一个常见的误解是认为未在模板中指定的属性会保持不变。 如果未指定某些属性，资源管理器会将部署解释为覆盖这些值。 未包含在模板中的属性将重置为默认值。 指定资源的所有非默认值，而不仅仅是要更新的属性。 模板中的资源定义始终包含资源的最终状态。 它不能表示对现有资源的部分更新。
>
> 在极少数情况下，为资源指定的属性是作为子资源实现的。 例如，当为 Web 应用提供站点配置值时，会在子资源类型 `Microsoft.Web/sites/config` 中实现这些值。 如果重新部署 Web 应用并为站点配置值指定了空对象，则子资源不会更新。 但是，如果提供新的站点配置值，则子资源类型会更新。

## <a name="example-result"></a>示例结果

为了说明增量模式和完整模式的差异，请考虑以下方案。

**资源组** 包含：

* 资源 A
* 资源 B
* 资源 C

**模板** 包含：

* 资源 A
* 资源 B
* 资源 D

在“增量”模式下部署时，资源组具有：

* 资源 A
* 资源 B
* 资源 C
* 资源 D

在“完整”模式下部署时，会删除资源 C。 资源组具有：

* 资源 A
* 资源 B
* 资源 D

## <a name="set-deployment-mode"></a>设置部署模式

在使用 PowerShell 部署时若要设置部署模式，请使用 `Mode` 参数。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

在使用 Azure CLI 部署时若要设置部署模式，请使用 `mode` 参数。

```azurecli-interactive
az deployment group create \
  --mode Complete \
  --name ExampleDeployment \
  --resource-group ExampleResourceGroup \
  --template-file storage.json
```

以下示例显示了设置为增量部署模式的链接模板：

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建资源管理器模板，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
* 若要了解部署资源，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)。
* 若要查看资源提供程序的操作，请参阅 [Azure REST API](/rest/api/)。
