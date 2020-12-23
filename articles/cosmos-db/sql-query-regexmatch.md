---
title: Azure Cosmos DB 查询语言中的 RegexMatch
description: 了解 Azure Cosmos DB 中的 RegexMatch SQL 系统函数
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341631"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

提供正则表达式功能。 正则表达式是用于查找文本模式的简洁而灵活的表示法。 Azure Cosmos DB 使用 [ (PCRE) 的 PERL 兼容正则表达式 ](http://www.pcre.org/)。 

## <a name="syntax"></a>语法
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   是要搜索的字符串表达式。  
  
*str_expr2*  
   正则表达式。

*str_expr3*  
   要用于正则表达式的选定修饰符的字符串。 此字符串值是可选的。 如果要运行不带修饰符的 RegexMatch，可以添加一个空字符串或完全省略。 

您可以了解有关 [在 Perl 中创建正则表达式的语法](https://perldoc.perl.org/perlre)。 

Azure Cosmos DB 支持以下四个修饰符：

| 修饰符 | 说明 |
| ------ | ----------- |
| `m` | 将要搜索的字符串表达式视为多行。 如果没有此选项，则 "^" 和 "$" 将在字符串的开头或结尾处匹配，而不是与每个单独的行匹配。 |
| `s` | 允许 "." 匹配任何字符，包括换行符。 | 
| `i` | 模式匹配时忽略大小写。 |
| `x` | 忽略所有空白字符。 |

## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。 如果要搜索的字符串表达式、正则表达式或所选修饰符无效，则返回 undefined。
  
## <a name="examples"></a>示例
  
以下简单的 RegexMatch 示例使用几个不同的修饰符来检查字符串 "abcd" 中是否存在正则表达式匹配。
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 下面是结果集。  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

使用 RegexMatch，可以使用元字符执行更复杂的字符串搜索，而不能使用 StartsWith、EndsWith、Contains 或 StringEquals 系统函数。 下面是一些其他示例，可使用通过 [Azure Cosmos DB 查询操场](https://www.documentdb.com/sql/demo)提供的营养数据集运行。 

> [!NOTE] 
> 如果需要在正则表达式中使用元字符，而不希望它具有特殊意义，则应该使用对元字符进行转义 `\` 。

**检查其说明中包含单词 "salt" 的项是否恰好一次：**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**检查其说明包含0到99之间的一个数字的项：**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**检查其说明中包含四个字母单词（以 "S" 或 "s" 开头）的项：**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>备注

如果正则表达式可以分解为 StartsWith、EndsWith、Contains 或 StringEquals 系统函数，则此系统函数将从 [范围索引](index-policy.md#includeexclude-strategy) 中受益。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
