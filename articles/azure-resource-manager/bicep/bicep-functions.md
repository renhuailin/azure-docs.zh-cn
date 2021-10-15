---
title: Bicep 函数
description: 介绍在 Bicep 文件中检索值、处理字符串和数字以及检索部署信息时所用的函数。
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 6fabd0bdbe6341382344c0e63ca711519275a3f4
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351677"
---
# <a name="bicep-functions"></a>Bicep 函数

本文介绍可在 Bicep 文件中使用的所有函数。 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。

大多数函数在部署到资源组、订阅、管理组或租户时工作方式相同。 某些函数并非可以在所有范围内使用。 下表对这些函数进行了说明。

## <a name="namespaces-for-functions"></a>函数的命名空间

所有 Bicep 函数都包含在两个命名空间中 - `az` 和 `sys`。 通常，使用函数时，无需指定命名空间。 仅当函数名称与在 Bicep 文件中定义的另一项相同时，才需要指定命名空间。 例如，如果创建一个名为 `range` 的参数，则需要通过添加 `sys` 命名空间来区分 `range` 函数。

```bicep
// Parameter contains the same name as a function
param range int

// Must use sys namespace to call the function. 
// The second use of range refers to the parameter.
output result array = sys.range(1, range)
```

`az` 命名空间包含特定于 Azure 部署的函数。 `sys` 命名空间包含用于构造值的函数。 `sys` 命名空间还包括参数和资源循环的修饰器。 本文对这些命名空间进行了说明。

## <a name="any-function"></a>任何函数

Bicep 中提供 [any 函数](./bicep-functions-any.md)可帮助解决有关数据类型警告的问题。 此函数位于 `sys` 命名空间中。

## <a name="array-functions"></a>数组函数

以下函数可用于处理数组。 所有这些函数都位于 `sys` 命名空间中。

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>日期函数

以下函数可用于处理日期。 所有这些函数都位于 `sys` 命名空间中。

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>部署值函数

以下函数可用于获取与部署相关的值。 所有这些函数都位于 `az` 命名空间中。

* [部署](./bicep-functions-deployment.md#deployment)
* [环境](./bicep-functions-deployment.md#environment)

## <a name="file-functions"></a>文件函数

以下函数可用于将外部文件的内容加载到 Bicep 文件中。 所有这些函数都位于 `sys` 命名空间中。

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>逻辑函数

以下函数可用于处理逻辑条件。 此函数位于 `sys` 命名空间中。

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>数值函数

以下函数可用于处理整数。 所有这些函数都位于 `sys` 命名空间中。

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>对象函数

以下函数可用于处理对象。 所有这些函数都位于 `sys` 命名空间中。

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>Resource functions

以下函数可用于获取资源值。 其中的大多数函数位于 `az` 命名空间中。 列表函数和 getSecret 函数在资源类型上直接调用，因此它们没有命名空间限定符。

* [extensionResourceId](./bicep-functions-resource.md#extensionresourceid)
* [getSecret](./bicep-functions-resource.md#getsecret)
* [listAccountSas](./bicep-functions-resource.md#list)
* [listKeys](./bicep-functions-resource.md#listkeys)
* [listSecrets](./bicep-functions-resource.md#list)
* [list*](./bicep-functions-resource.md#list)
* [pickZones](./bicep-functions-resource.md#pickzones)
* [提供程序（已弃用）](./bicep-functions-resource.md#providers)
* [reference](./bicep-functions-resource.md#reference)
* [resourceId](./bicep-functions-resource.md#resourceid) - 可以在任何范围内使用，但有效参数会根据范围而发生变化。
* [subscriptionResourceId](./bicep-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](./bicep-functions-resource.md#tenantresourceid)

## <a name="scope-functions"></a>作用域函数

以下函数可用于获取作用域值。 所有这些函数都位于 `az` 命名空间中。

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - 只能在部署到资源组时使用。
* [subscription](./bicep-functions-scope.md#subscription) - 只能在部署到资源组或订阅时使用。
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>字符串函数

Bicep 提供以下函数用于处理字符串。 所有这些函数都位于 `sys` 命名空间中。

* [base64](./bicep-functions-string.md#base64)
* [base64ToJson](./bicep-functions-string.md#base64tojson)
* [base64ToString](./bicep-functions-string.md#base64tostring)
* [concat](./bicep-functions-string.md#concat)
* [contains](./bicep-functions-string.md#contains)
* [dataUri](./bicep-functions-string.md#datauri)
* [dataUriToString](./bicep-functions-string.md#datauritostring)
* [empty](./bicep-functions-string.md#empty)
* [endsWith](./bicep-functions-string.md#endswith)
* [first](./bicep-functions-string.md#first)
* [format](./bicep-functions-string.md#format)
* [guid](./bicep-functions-string.md#guid)
* [indexOf](./bicep-functions-string.md#indexof)
* [last](./bicep-functions-string.md#last)
* [lastIndexOf](./bicep-functions-string.md#lastindexof)
* [length](./bicep-functions-string.md#length)
* [newGuid](./bicep-functions-string.md#newguid)
* [padLeft](./bicep-functions-string.md#padleft)
* [replace](./bicep-functions-string.md#replace)
* [skip](./bicep-functions-string.md#skip)
* [split](./bicep-functions-string.md#split)
* [startsWith](./bicep-functions-string.md#startswith)
* [string](./bicep-functions-string.md#string)
* [substring](./bicep-functions-string.md#substring)
* [take](./bicep-functions-string.md#take)
* [toLower](./bicep-functions-string.md#tolower)
* [toUpper](./bicep-functions-string.md#toupper)
* [trim](./bicep-functions-string.md#trim)
* [uniqueString](./bicep-functions-string.md#uniquestring)
* [uri](./bicep-functions-string.md#uri)
* [uriComponent](./bicep-functions-string.md#uricomponent)
* [uriComponentToString](./bicep-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Bicep 中部署多个资源实例](./loop-resources.md)。
* 若要了解如何部署已创建的 Bicep 文件，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](./deploy-powershell.md)。
