---
title: 1000 基因组
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用 1000 基因组数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 52f948526b1575f43d722c856251eed83b2e66ef
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982764"
---
# <a name="1000-genomes"></a>1000 基因组

1000 基因组项目在 2008 年到 2015 年之间运作，创建了人类变体和基因型数据的最大公共目录。 最终数据集包含 2,504 个人的数据，他们来自 26 个种群和 8400 万可识别变体。 有关详细信息，请参阅 1000 基因组项目网站和以下发布内容：

试点分析：来自种群范围序列的人类基因组变体图 Nature 467, 1061-1073（2010 年 10 月 28 日）

阶段 1 分析：来自 1,092 个人类基因组的基因变体集成图 Nature 491, 56-65（2012 年 11 月 1 日）

阶段 3 分析：人类基因变体的全球参考 Nature 526, 68-74（2015 年 10 月 1 日）以及 2,504 个人类基因组的结构性变体的集成图 Nature 526, 75-81（2015 年 10 月 1 日）

有关数据格式的详细信息，请参阅 http://www.internationalgenome.org/formats

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

此数据集是 ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/ 的镜像

## <a name="data-volumes-and-update-frequency"></a>数据卷和更新频率

该数据集大约包含 815 TB 的数据，且每天都会更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，建议将计算资源分配到美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2： https://dataset1000genomes.blob.core.windows.net/dataset

美国中西部： https://dataset1000genomes-secondary.blob.core.windows.net/dataset

[SAS 令牌](../storage/common/storage-sas-overview.md): sv=2019-10-10&si=prod&sr=c&sig=9nzcxaQn0NprMPlSh4RhFQHcXedLQIcFgbERiooHEqM%3D

## <a name="use-terms"></a>使用条款

在最终发布之后，1000 基因组项目中的数据将公开提供，任何人都可以根据数据集源 ([http://www.internationalgenome.org/data](http://www.internationalgenome.org/data)) 提供的条款使用这些数据。 应根据 1000 基因组项目中的[常见问题解答]()提供的详细信息引用数据的使用。

## <a name="contact"></a>联系人

https://www.internationalgenome.org/contact

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
