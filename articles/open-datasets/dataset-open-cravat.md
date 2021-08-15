---
title: OpenCravat
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用 OpenCravat 数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2047b5f5071f6166a7f30e7a04b79fa711c95624
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982112"
---
# <a name="opencravat-open-custom-ranked-analysis-of-variants-toolkit"></a>OpenCravat：开放式变体自定义排名分析工具包

OpenCRAVAT 是一种 python 包，可执行基因组变体解释，包括对变异影响、注释和评分的解释。 OpenCRAVAT 有一个结构化体系结构，其中包含各种分析模块和注释资源，你可根据给定研究的需求选择和安装/运行它们。

有关数据的详细信息，请参阅 [OpenCravat](https://opencravat.org/)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

此数据集是 https://store.opencravat.org 和 https://run.opencravat.org 的镜像

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

此数据集包含 500 GB 的数据，且每天都会更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，建议将计算资源分配到美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2： https://datasetopencravat.blob.core.windows.net/dataset

美国中西部： https://datasetopencravat-secondary.blob.core.windows.net/dataset

[SAS 令牌](../storage/common/storage-sas-overview.md)：sv=2020-04-08&st=2021-03-11T23%3A50%3A01Z&se=2025-07-26T22%3A50%3A00Z&sr=c&sp=rl&sig=J9J9wnJOXsmEy7TFMq9wjcxjXDE%2B7KhGpCUL4elsC14%3D

## <a name="use-terms"></a>使用条款

可通过 GPLv3 许可证使用 OpenCRAVAT。 大部分数据源免费用于非商业用途。 对于商业用途，请就每个数据源咨询相应的机构联系人。

## <a name="contact"></a>联系人

rkarchi1@jhmi.edu

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
