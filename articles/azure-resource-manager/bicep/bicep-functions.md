---
title: Bicep 函数
description: 介绍在 Bicep 文件中检索值、处理字符串和数字以及检索部署信息时所用的函数。
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: dcc0e71917431ac2a181ae87374499a89a27482c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303978"
---
# <a name="bicep-functions"></a>Bicep 函数

本文介绍可在 Bicep 文件中使用的所有函数。 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。

大多数函数在部署到资源组、订阅、管理组或租户时工作方式相同。 某些函数并非可以在所有范围内使用。 下表对这些函数进行了说明。

## <a name="any-function"></a>任何函数

Bicep 中提供 [any 函数](./bicep-functions-any.md)可帮助解决有关数据类型警告的问题。

## <a name="array-functions"></a>数组函数

以下函数可用于处理数组。

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

以下函数可用于处理日期。

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>部署值函数

以下函数可用于获取与部署相关的值：

* [部署](./bicep-functions-deployment.md#deployment)
* [环境](./bicep-functions-deployment.md#environment)

## <a name="logical-functions"></a>逻辑函数

以下函数可用于处理逻辑条件：

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>数值函数

以下函数可用于处理整数：

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>对象函数

以下函数可用于处理对象。

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>Resource functions

以下函数可用于获取资源值：

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

以下函数可用于获取作用域值。

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - 只能在部署到资源组时使用。
* [subscription](./bicep-functions-scope.md#subscription) - 只能在部署到资源组或订阅时使用。
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>字符串函数

Bicep 提供以下用于处理字符串的函数：

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
