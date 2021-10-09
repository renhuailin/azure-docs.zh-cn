---
title: Azure 数字孪生查询语言参考 - 保留关键字
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言保留关键字的参考文档
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ffba46cacf452a4769709b115177bab41d5eca7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837640"
---
# <a name="azure-digital-twins-query-language-reference-reserved-keywords"></a>Azure 数字孪生查询语言参考：保留关键字

本文档包含 [Azure 数字孪生查询语言](concepts-query-language.md)中的保留关键字的列表。 不能在查询中将这些关键字用作标识符，除非它们已[用双引号进行转义](#escaping-reserved-keywords-in-queries)。 

## <a name="list-of-reserved-keywords"></a>保留关键字列表

下面是 Azure 数字孪生查询语言中的保留关键字：

* ALL 
* AND
* AS
* ASC
* AVG
* BY
* COUNT
* DESC
* DEVICES_JOBS
* DEVICES_MODULES
* 设备
* ENDS_WITH
* false
* FROM
* GROUP
* IN
* IS_BOOL
* IS_DEFINED
* IS_NULL
* IS_NUMBER
* IS_OBJECT
* IS_PRIMITIVE
* IS_STRING
* MAX
* MIN
* NOT
* NOT_IN
* Null
* OR
* ORDER
* SELECT
* STARTS_WITH
* SUM
* TOP
* TRUE
* WHERE
* IS_OF_MODEL

## <a name="escaping-reserved-keywords-in-queries"></a>在查询中对保留关键字进行转义

若要在查询中将保留关键字用作标识符，请用双引号将关键字括起来进行转义，如下所示：`[[<keyword>]]`

例如，请考虑使用一组带有 `GROUP` 属性的数字孪生体，该属性就是一个保留关键字。 若要对该属性值进行筛选，必须在查询中使用该属性的位置对属性名称进行转义，如下所示：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ReservedKeywordExample":::