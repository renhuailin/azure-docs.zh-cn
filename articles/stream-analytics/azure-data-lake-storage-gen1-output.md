---
title: Azure 流分析的 Azure Data Lake Storage Gen 1 输出
description: 本文介绍作为 Azure 流分析输出选项的 Azure Data Lake Storage Gen 1。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 629a154c89ad301a3e200b1d6cd04c62057d9959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016535"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure 流分析的 Azure Data Lake Storage Gen 1 输出

流分析支持 [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) 输出。 Azure Data Lake Storage 是一个企业范围的超大规模存储库，适用于大数据分析工作负荷。 可以使用 Data Lake Storage 存储任何大小、类型和引入速度的数据，以便进行运营分析和探索分析。 流分析需要经过授权才能访问 Data Lake Storage。

流分析中的 Azure Data Lake Storage 输出不可在 Azure 中国世纪互联和 Azure 德国 (T-Systems International) 区域中使用。

## <a name="output-configuration"></a>输出配置

下表列出了用于配置 Data Lake Storage Gen 1 输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 | 查询中使用的易记名称，用于将查询输出定向到 Data Lake Store。 |
| 订阅 | 包含 Azure Data Lake Storage 帐户的订阅。 |
| 帐户名 | Data Lake Store 帐户的名称（将向该帐户发送输出）。 此时会显示订阅中提供的 Data Lake Store 帐户的下拉列表。 |
| 路径前缀模式 | 用于对指定 Data Lake Store 帐户中的文件进行写入的文件路径。 可以指定 {date} 和 {time} 变量的一个或多个实例：<br /><ul><li>示例 1：folder1/logs/{date}/{time}</li><li>示例 2：folder1/logs/{date}</li></ul><br />创建的文件夹结构的时间戳遵循 UTC 而不是本地时间。<br /><br />如果文件路径模式不包含结尾斜杠 (/)，文件路径中的最后一个模式将被视为文件名前缀。 <br /><br />在这些情况下会创建新文件：<ul><li>在输出架构中进行更改</li><li>在外部或内部重启作业</li></ul> |
| 日期格式 | 可选。 如果在前缀路径中使用日期令牌，可以选择组织文件所采用的日期格式。 示例：YYYY/MM/DD |
|时间格式 | 可选。 如果在前缀路径中使用时间令牌，可指定组织文件所采用的时间格式。 目前唯一支持的值是 HH。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV 和 Avro。|
| 编码 | 如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。|
| 分隔符 | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。|
| 格式 | 仅适用于 JSON 序列化。 分隔行指定通过新行分隔各个 JSON 对象，从而格式化输出。 如果选择“行分隔”，则读取 JSON 时，一次读取一个对象。 整个内容本身将不是有效的 JSON。  数组指定输出会被格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。|
| 身份验证模式 | 可以使用[托管标识](stream-analytics-managed-identities-adls.md)（预览版）或用户令牌来授权对 Data Lake Storage 帐户的访问。 授予访问权限后，可以通过更改用户帐户密码、删除此作业的 Data Lake Storage 输出或删除流分析作业，来撤销访问权限。 |

## <a name="partitioning"></a>分区

对于分区键，请在 path 前缀模式中使用 {date} 和 {time} 标记。 选择日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 使用 HH 作为时间格式。 输出编写器的数量与[可完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区一致。

## <a name="output-batch-size"></a>输出批大小

有关最大消息大小，请参阅 [Data Lake Storage 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits)。 若要优化批大小，每个写入操作最多使用 4 MB。

## <a name="next-steps"></a>后续步骤

* [使用托管标识在 Azure Data Lake Storage Gen1 中对流分析进行身份验证（预览）](stream-analytics-managed-identities-adls.md)
* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)