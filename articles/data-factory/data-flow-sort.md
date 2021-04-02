---
title: 映射数据流中的排序转换
description: Azure 数据工厂映射数据排序转换
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81606323"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>映射数据流中的排序转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

借助排序转换，可对当前数据流上的传入行进行排序。 可以选择各个列，然后按升序或降序对其进行排序。

> [!NOTE]
> 映射数据流在 Spark 群集上执行，这些数据流跨多个节点和分区分布。 如果选择在后续转换中对数据进行重新分区，则可能会因为数据重排而丢失排序。

## <a name="configuration"></a>Configuration

![排序设置](media/data-flow/sort.png "排序")

**不区分大小写：** 对字符串或文本字段排序时是否要忽略大小写

**仅在分区中排序：** 数据流在 Spark 上运行时，所有数据流都将划分为多个分区。 此设置仅对传入分区中的数据排序，不对整个数据流排序。 

**排序条件：** 选择要排序的列以及排序顺序。 顺序决定排序优先级。 选择数据流的开头或结尾是否显示 null。

### <a name="computed-columns"></a>计算列

若要在排序前修改或提取列值，请将鼠标悬停在列上并选择“计算列”。 这将打开表达式生成器来为排序操作创建表达式，而不是使用列值。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>示例

![排序设置](media/data-flow/sort.png "排序")

以上排序配置的数据流脚本位于下面的代码片段中。

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>后续步骤

排序后，建议使用[聚合转换](data-flow-aggregate.md)
