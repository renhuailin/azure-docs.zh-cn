---
title: Azure 角色分配条件格式和语法（预览版）- Azure RBAC
description: 获取 Azure 基于特性的访问控制 (Azure ABAC) 的 Azure 角色分配条件格式和语法概述。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: b271da3cf8e591df8557133abcff248a737645e5
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489561"
---
# <a name="azure-role-assignment-condition-format-and-syntax-preview"></a>Azure 角色分配条件格式和语法（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

条件是一项额外的检查，你可选择性地将其添加到角色分配，以提供更精细的访问控制。 例如，为了读取某个对象，可以添加要求该对象具有特定标记的条件。 本文介绍角色分配条件的格式和语法。

## <a name="condition-format"></a>条件格式

为了更好地了解角色分配条件，最好是先了解格式。

### <a name="simple-condition"></a>简单条件

最基本的条件包含目标操作和表达式。 操作是用户可以针对资源类型执行的操作。 表达式是求值结果为 true 或 false 的语句，此求值结果确定了是否允许执行相应的操作。

下面显示了简单条件的格式。

![包含单个操作和单个表达式的简单条件的格式。](./media/conditions-format/format-simple.png)

以下条件包含“读取 Blob”操作。 该表达式检查容器名称是否为 blobs-example-container。

![包含 Blob 读取操作和容器名称表达式的简单条件示例。](./media/conditions-format/format-simple-example.png)

![该示意图显示对具有特定容器名称的 Blob 进行的读取访问。](./media/conditions-format/format-simple-example-diagram.png)

### <a name="how-a-condition-is-evaluated"></a>条件的求值方式

如果用户尝试执行角色分配中并非 `<action>` 的操作，则 `!(ActionMatches)` 的求值结果为 true，并且整个条件的求值结果也为 true，以允许执行该操作。

如果用户尝试执行角色分配中的 `<action>`，则 `!(ActionMatches)` 的求值结果为 false，因此将进行表达式求值。 如果表达式求值结果为 true，则整个条件的求值结果将为 true，以允许执行 `<action>`。 否则不允许执行 `<action>`。

以下伪代码演示了读取此条件的另一种方式。

```
if a user tries to perform an action in the role assignment that does not match <action>
{
    Allow action to be performed
}
else
{
    if <attribute> <operator> <value> is true
    {
        Allow <action> to be performed
    }
    else
    {
        Do not allow <action> to be performed
    }
}
```

### <a name="suboperations"></a>子操作

某些操作具有子操作。 例如，“读取 Blob”数据操作具有“从具有标记条件的 Blob 读取内容”子操作。 包含子操作的条件采用以下格式。

![具有子操作的操作的格式。](./media/conditions-format/format-suboperation.png)

### <a name="multiple-actions"></a>多个操作

一个条件可以包含当其求值结果为 true 时你要允许执行的多个操作。 如果为单个条件选择多个操作，则可为条件选择的特性可能较少，因为这些特性必须在所选操作中可用。

![条件求值结果为 true 时允许执行的多个操作的格式。](./media/conditions-format/format-multiple-actions.png)

### <a name="multiple-expressions"></a>多个表达式

一个条件可以包含多个表达式。 可以根据运算符针对多个值检查特性。

![使用布尔运算符和多个值的多个表达式的格式。](./media/conditions-format/format-multiple-expressions.png)

### <a name="multiple-conditions"></a>多个条件

还可以组合多个条件以将多个操作指定为目标。

![使用布尔运算符的多个条件的格式。](./media/conditions-format/format-multiple-conditions.png)

## <a name="condition-syntax"></a>条件语法

下面显示了角色分配条件的语法。

```
(
    (
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
(
    (
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
...
```

## <a name="actions"></a>操作

目前，可将条件添加到包含存储 Blob 数据操作的内置或自定义角色分配。 这包括以下内置角色：

- [存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)
- [存储 Blob 数据所有者](built-in-roles.md#storage-blob-data-owner)
- [存储 Blob 数据读者](built-in-roles.md#storage-blob-data-reader)

有关可在条件中使用的存储 Blob 操作的列表，请参阅 [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](../storage/common/storage-auth-abac-attributes.md)

## <a name="attributes"></a>属性

根据所选的操作，特性可能出现在不同的位置。 如果为单个条件选择多个操作，则可为条件选择的特性可能较少，因为这些特性必须在所选操作中可用。 若要指定特性，必须包含源作为前缀。

> [!div class="mx-tableFixed"]
> | 特性源 | 说明 | 代码 |
> | --- | --- | --- |
> | 资源 | 指示特性位于资源（例如容器名称）上。 | `@Resource` |
> | 请求 | 指示特性是操作请求（例如设置 Blob 索引标记）的一部分。 | `@Request` |

有关可在条件中使用的存储 Blob 特性的列表，请参阅 [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](../storage/common/storage-auth-abac-attributes.md)

## <a name="operators"></a>运算符

下表列出了可用于构造条件的运算符。

| 类别 | 操作员 | 说明 |
| --- | --- | --- |
| 逻辑比较 |`AND`<br/>`&&` | “与”运算符。 |
|  | `OR`<br/>`||` | “或”运算符。 |
|  | `NOT`<br/>`!` | “非”或求反运算符。 |
| 字符串比较 | `StringEquals`<br/>`StringEqualsIgnoreCase` | 区分大小写（或不区分大小写）匹配。 值必须与字符串完全匹配。 |
|  | `StringNotEquals`<br/>`StringNotEqualsIgnoreCase` | `StringEquals`（或 `StringEqualsIgnoreCase`）运算符求反 |
|  | `StringStartsWith`<br/>`StringStartsWithIgnoreCase` | 区分大小写（或不区分大小写）匹配。 值以字符串开头。 |
|  | `StringNotStartsWith`<br/>`StringNotStartsWithIgnoreCase` | `StringStartsWith`（或 `StringStartsWithIgnoreCase`）运算符求反 |
|  | `StringLike`<br/>`StringLikeIgnoreCase` | 区分大小写（或不区分大小写）匹配。 值可以包含字符串中任意位置的多字符匹配通配符 (`*`) 或单字符匹配通配符 (`?`)。 如果需要，可以通过添加反斜杠 `\*` 和 `\?` 来转义这些字符。 |
|  | `StringNotLike`<br/>`StringNotLikeIgnoreCase` | `StringLike`（或 `StringLikeIgnoreCase`）运算符求反 |
| 数值比较 | `NumericEquals`<br/>`NumericNotEquals`<br/>`NumericLessThan`<br/>`NumericLessThanEquals`<br/>`NumericGreaterThan`<br/>`NumericGreaterThanEquals` | 目前仅支持整数。 |
| 高阶函数 | `ActionMatches` | 检查 Action[ID] 值是否与指定的操作模式相匹配。 此运算符等效于 SDK 在将某个操作与 Permission 中的操作模式进行比较时使用的操作匹配逻辑。 |
| 叉积比较 | `ForAnyOfAnyValues:StringEquals`<br/>`ForAnyOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotEquals`<br/>`ForAnyOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringLike`<br/>`ForAnyOfAnyValues:StringLikeIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotLike`<br/>`ForAnyOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAnyValues:NumericEquals`<br/>`ForAnyOfAnyValues:NumericNotEquals`<br/>`ForAnyOfAnyValues:NumericGreaterThan`<br/>`ForAnyOfAnyValues:NumericGreaterThanEquals`<br/>`ForAnyOfAnyValues:NumericLessThan`<br/>`ForAnyOfAnyValues:NumericLessThanEquals` | 如果左侧至少有一个值满足与右侧至少一个值进行比较的条件，则表达式求值结果为 true。 格式为：`ForAnyOfAnyValues:<BooleanFunction>`。 支持多个字符串和数字。 |
|  | `ForAllOfAnyValues:StringEquals`<br/>`ForAllOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringNotEquals`<br/>`ForAllOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringLike`<br/>`ForAllOfAnyValues:StringLikeIgnoreCase`<br/>`ForAllOfAnyValues:StringNotLike`<br/>`ForAllOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAnyValues:NumericEquals`<br/>`ForAllOfAnyValues:NumericNotEquals`<br/>`ForAllOfAnyValues:NumericGreaterThan`<br/>`ForAllOfAnyValues:NumericGreaterThanEquals`<br/>`ForAllOfAnyValues:NumericLessThan`<br/>`ForAllOfAnyValues:NumericLessThanEquals` | 如果左侧每个值都满足与右侧至少一个值进行比较的条件，则表达式求值结果为 true。 格式为：`ForAllOfAnyValues:<BooleanFunction>`。 支持多个字符串和数字。 |
|  | `ForAnyOfAllValues:StringEquals`<br/>`ForAnyOfAllValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringNotEquals`<br/>`ForAnyOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringLike`<br/>`ForAnyOfAllValues:StringLikeIgnoreCase`<br/>`ForAnyOfAllValues:StringNotLike`<br/>`ForAnyOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAllValues:NumericEquals`<br/>`ForAnyOfAllValues:NumericNotEquals`<br/>`ForAnyOfAllValues:NumericGreaterThan`<br/>`ForAnyOfAllValues:NumericGreaterThanEquals`<br/>`ForAnyOfAllValues:NumericLessThan`<br/>`ForAnyOfAllValues:NumericLessThanEquals` | 如果左侧至少有一个值满足与右侧每个值进行比较的条件，则表达式求值结果为 true。 格式为：`ForAnyOfAllValues:<BooleanFunction>`。 支持多个字符串和数字。 |
|  | `ForAllOfAllValues:StringEquals`<br/>`ForAllOfAllValues:StringEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringNotEquals`<br/>`ForAllOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringLike`<br/>`ForAllOfAllValues:StringLikeIgnoreCase`<br/>`ForAllOfAllValues:StringNotLike`<br/>`ForAllOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAllValues:NumericEquals`<br/>`ForAllOfAllValues:NumericNotEquals`<br/>`ForAllOfAllValues:NumericGreaterThan`<br/>`ForAllOfAllValues:NumericGreaterThanEquals`<br/>`ForAllOfAllValues:NumericLessThan`<br/>`ForAllOfAllValues:NumericLessThanEquals` | 如果左侧每个值都满足与右侧每个值进行比较的条件，则表达式求值结果为 true。 格式为：`ForAllOfAllValues:<BooleanFunction>`。 支持多个字符串和数字。 |

## <a name="operator-examples"></a>运算符示例

> [!div class="mx-tableFixed"]
> | 示例 | 结果 |
> | --- | --- |
> | `ActionMatches{'Microsoft.Authorization/roleAssignments/*'}` | 如果所要检查的操作等于“Microsoft.Authorization/roleAssignments/write”，则结果为 true |
> | `ActionMatches{'Microsoft.Authorization/roleDefinitions/*'}` | 如果所要检查的操作等于“Microsoft.Authorization/roleAssignments/write”，则结果为 false |
> | `Resource[name1] StringLike 'a*c?'` | 如果 Resource [name1] 等于“abcd”，则结果为 true |
> | `Resource[name1] StringLike 'A*C?'` | 如果 Resource [name1] 等于“abcd”，则结果为 false |
> | `Resource[name1] StringLike 'a*c'` | 如果 Resource [name1] 等于“abcd”，则结果为 false |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'blue', 'green'}` | true |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'orange', 'green'}` | false |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'orange', 'red', 'blue'}` | true |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'red', 'green'}` | false |
> | `{10, 20} ForAnyOfAllValues:NumericLessThan {15, 18}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {5, 15, 18}` | false |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {25, 30}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {15, 25, 30}` | false |

## <a name="special-characters"></a>特殊字符

| 字符 | 说明 |
| --- | --- |
| `*` | 星号 (*) 表示可与 `Like` 运算符结合使用的多字符通配符匹配。 如果需要，可以通过添加反斜杠 `\*` 来转义星号。 |
| `?` | 问号 (?) 表示可与 `Like` 运算符结合使用的单字符通配符匹配。 如果需要，可以通过添加反斜杠 `\?` 来转义问号。 |
| `$` | 美元符号 ($) 用于帮助描述标记键。 在 Azure PowerShell 中，如果括在双引号 (") 中的字符串包含美元符号，则必须使用反撇号 (\`) 作为该字符串的前缀。 例如：``tags:Project<`$key_case_sensitive`$>``。 | 

## <a name="grouping-and-precedence"></a>组合与优先级

可以在条件中使用括号 `()` 来组合表达式并定义优先级。 如果目标操作有三个或更多个表达式，则必须添加括号以定义表达式的求值顺序。 括在括号中的表达式优先级更高。 例如，如果有以下表达式：

```
a AND b OR c
```

必须按以下方式之一添加括号：

```
(a AND b) OR c
```

```
a AND (b OR c)
```

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
- [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](../storage/common/storage-auth-abac-attributes.md)
