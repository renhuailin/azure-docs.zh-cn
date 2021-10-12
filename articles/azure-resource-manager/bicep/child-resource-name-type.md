---
title: Bicep 中的子资源
description: 介绍如何在 Bicep 中设置子资源的名称和类型。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: 2d928ec83559a1bd57adde3cbae98c589bb1cd15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621992"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>在 Bicep 中设置子资源的名称和类型

子资源是只存在于另一资源的上下文内的资源。 例如，如果没有[虚拟机](/azure/templates/microsoft.compute/virtualmachines)，则[虚拟机扩展](/azure/templates/microsoft.compute/virtualmachines/extensions)不能存在。 扩展资源是虚拟机的子项。

每个父资源仅接受特定的资源类型作为子资源。 [Bicep 资源参考](/azure/templates/)中介绍了资源类型的层次结构。

本文介绍声明子资源的不同方法。

### <a name="microsoft-learn"></a>Microsoft Learn

若要了解有关子资源以及实践指南，请参阅在 Microsoft Learn 中的 [使用 Bicep 部署子资源和扩展资源](/learn/modules/child-extension-bicep-templates) **。**

## <a name="name-and-type-pattern"></a>名称和类型模式

在 Bicep 中，可以在父资源内部或外部指定子资源。 为资源名称和资源类型提供的值根据你声明子资源的方式而有所不同。 但是，全名和类型始终解析为相同的模式。 

子资源的全名使用模式：

```bicep
{parent-resource-name}/{child-resource-name}
```

如果层次结构中具有两个级别以上，请继续重复父名称：

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

子资源的完整类型使用模式：

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

如果层次结构中具有两个级别以上，请继续重复父资源类型：

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

如果对 `/` 字符之间的段进行计数，则类型中的段数始终比名称中的段数多一。 

## <a name="within-parent-resource"></a>在父资源内部

以下示例显示子资源包括在父资源的资源属性中。

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

嵌套资源声明必须出现在父资源语法的顶级。 只要每个级别都是其父资源的子类型，就可以以任意深度嵌套声明。

在父资源类型内部定义时，可以将类型和名称值的格式设置为不带斜杠的单个段。 以下示例演示一个存储帐户，该帐户具有文件服务的子资源，并且文件服务具有文件共享的子资源。 文件服务的名称设置为 `default`，其类型设置为 `fileServices`。 文件共享的名称设置为 `exampleshare`，其类型设置为 `shares`。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

完整的资源类型仍为 `Microsoft.Storage/storageAccounts/fileServices` 和 `Microsoft.Storage/storageAccounts/fileServices/shares`。 无需提供 `Microsoft.Storage/storageAccounts/`，因为已假设它继承父资源类型和版本。 嵌套资源可以选择使用语法 `<segment>@<version>` 声明 API 版本。 如果嵌套资源省略了 API 版本，则使用父资源的 API 版本。 如果嵌套资源指定了 API 版本，则使用指定的 API 版本。

子资源名称设置为 `default` 和 `exampleshare`，但完整名称包括父名称。 不用提供 `examplestorage` 和 `default`，因为假设它继承父资源。

嵌套资源可以访问其父资源的属性。 在同一父资源的正文中声明的其他资源可以通过符号名称互相引用。 父资源无法访问它所包含的资源的属性，尝试那样做会导致循环依赖。

若要引用父资源外部的嵌套资源，必须使用包含它的资源的名称和 `::` 运算符对它进行限定。 例如，若要输出子资源中的属性，请执行以下操作：

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>父资源外部

以下示例显示了父资源外部的子资源。 如果父资源未部署在同一模板中，或者你想要通过[循环](loop-resources.md)来创建多个子资源，则可以使用此方法。 请指定子级的父属性，将其值设置为父级的符号名称。 使用此语法，你仍然需要声明完整的资源类型，但子资源的名称就是子级的名称。

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

在父资源外部定义时，请使用斜杠来格式化类型和名称值，使之包含父类型和名称。

以下示例展示了全都在根级别定义的存储帐户、文件服务和文件共享。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

引用子资源符号名称的方式与引用父级相同。

## <a name="full-resource-name-outside-parent"></a>父级外部的完整资源名称

在父资源外部声明子资源时，也可以使用完整的资源名称和类型。 不用在子资源上设置父属性。 由于无法推断依赖项，因此必须显式设置。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> 不建议设置完整的资源名称和类型。 它的安全性与使用其他方法时的安全性不同。

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建 Bicep 文件，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
* 若要了解引用资源时的资源名称格式，请参阅[引用函数](./bicep-functions-resource.md#reference)。
