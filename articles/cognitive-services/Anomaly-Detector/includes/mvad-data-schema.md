---
title: MVAD 数据架构
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 3be9d5d50d34eee35de38b7b28dc1d42217ace24
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292658"
---
### <a name="input-data-schema"></a>输入数据架构

MVAD 会检测一组指标中的异常情况，我们将每个指标称作变量或时序。

* 可以从 Microsoft 下载示例数据文件，以检查接受的架构：[https://aka.ms/AnomalyDetector/MVADSampleData](https://aka.ms/AnomalyDetector/MVADSampleData)
* 每个变量必须且只能具有 `timestamp` 和 `value` 这两个字段，并且应存储在逗号分隔值 (CSV) 文件中。
* 该 CSV 文件的列名必须是 `timestamp` 和 `value`（区分大小写）。
* `timestamp` 值应符合 ISO 8601 标准；`value` 可为整数，也可为带有任意小数位的小数。
    CSV 文件内容的典型示例：

    |timestamp | 值|
    |-------|-------|
    |2019-04-01T00:00:00Z| 5|
    |2019-04-01T00:01:00Z| 3.6|
    |2019-04-01T00:02:00Z| 4|
    |...| ...|

    > [!NOTE]
    > 如果时间戳包含小时、分钟和/或秒，请确保在调用 API 之前正确舍入这些时间数字。
    >
    > 例如，如果数据频率预期是每隔 30 秒生成一个数据点，但你看到类似于“12:00:01”和“12:00:28”的时间戳，那么，这就相当肯定地表明，应将该时间戳预处理为类似于“12:00:00”和“12:00:30”的新值。
    >
    > 有关详细信息，请参阅最佳做法文档中的[“时间戳舍入”部分](../concepts/best-practices-multivariate.md#timestamp-round-up)。
* CSV 文件名称将用作变量名称，应是唯一的。 例如，“temperature.csv”和“humidity.csv”。
* 用于训练的变量和用于推理的变量应该一致。 例如，如果使用 `series_1`、`series_2`、`series_3`、`series_4` 和 `series_5` 进行训练，则应提供完全相同的变量进行推理。
* CSV 文件应压缩成 zip 文件并上传到 Azure Blob 容器。 zip 文件可采用任何所需名称。

#### <a name="folder-structure"></a>文件夹结构

数据准备过程中的一个常见错误是在 zip 文件包含多余的文件夹。 例如，假定 zip 文件的名称为 `series.zip`。 将文件解压缩到新文件夹 `./series` 后，CSV 文件的正确路径是 `./series/series_1.csv`，而错误的路径可能是 `./series/foo/bar/series_1.csv` 。

在 Windows 中解压缩 zip 文件后正确的目录树示例

```bash
.
└── series
    ├── series_1.csv
    ├── series_2.csv
    ├── series_3.csv
    ├── series_4.csv
    └── series_5.csv
```

在 Windows 中解压缩 zip 文件后错误的目录树示例

```bash
.
└── series
    └── series
        ├── series_1.csv
        ├── series_2.csv
        ├── series_3.csv
        ├── series_4.csv
        └── series_5.csv
```