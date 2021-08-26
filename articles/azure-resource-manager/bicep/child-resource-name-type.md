---
title: Bicep 中的子资源
description: 介绍如何在 Bicep 中设置子资源的名称和类型。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 879c325ee64307e7c548efa4ef7ba34eb68cc896
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444197"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>在 Bicep 中设置子资源的名称和类型

子资源是只存在于另一资源的上下文内的资源。 例如，如果没有[虚拟机](/azure/templates/microsoft.compute/virtualmachines)，则[虚拟机扩展](/azure/templates/microsoft.compute/virtualmachines/extensions)不能存在。 扩展资源是虚拟机的子项。

每个父资源仅接受特定的资源类型作为子资源。 子资源的资源类型包括父资源的资源类型。 例如，`Microsoft.Web/sites/config` 和 `Microsoft.Web/sites/extensions` 都是 `Microsoft.Web/sites` 的子资源  。 可接受的资源类型在父资源的 [模板架构](https://github.com/Azure/azure-resource-manager-schemas) 中指定。

在 Bicep 中，可以在父资源内部或外部指定子资源。 你为资源名称和资源类型提供的值取决于是在父资源内部还是在外部定义子资源。

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

在父资源类型内部定义时，可以将类型和名称值的格式设置为不带斜杠的单个段。 以下示例显示了一个包含文件服务和文件共享的存储帐户。 文件服务的名称设置为 default，其类型设置为 fileServices。  文件共享的名称设置为 exampleshare，其类型设置为 shares。 

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }

  resource service 'fileServices' = {
    name: 'default'

    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

完整的资源类型仍为 `Microsoft.Storage/storageAccounts/fileServices` 和 `Microsoft.Storage/storageAccounts/fileServices/shares`。 无需提供 `Microsoft.Storage/storageAccounts/`，因为已假设它继承父资源类型和版本。 嵌套资源可以选择使用语法 `<segment>@<version>` 声明 API 版本。 如果嵌套资源省略了 API 版本，则使用父资源的 API 版本。 如果嵌套资源指定了 API 版本，则使用指定的 API 版本。

子资源名称设置为 default 和 exampleshare，但完整名称包括父名称。  不提供 examplestorage 或 default，因为它们是从父资源继承的。 

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

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: storage
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = {
  name: 'exampleshare'
  parent: service
}
```

引用子资源符号名称的方式与引用父级相同。

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建 Bicep 文件，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
* 若要了解引用资源时的资源名称格式，请参阅[引用函数](./bicep-functions-resource.md#reference)。
