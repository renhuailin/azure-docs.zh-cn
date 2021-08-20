---
title: SnpEff
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用 SnpEff 数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 8072d3a48e0bcc0fea040a964aac579dc2061deb
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982377"
---
# <a name="snpeff-genomic-variant-annotations-and-functional-effect-prediction-toolbox"></a>SnpEff：基因变异注释和功能影响预测工具箱

[SnpEff](https://pcingola.github.io/SnpEff/) 基因变异注释和功能影响预测工具箱。 它可对基因和蛋白质上的遗传变异（例如氨基酸改变）进行注释并预测变异影响。

有关数据的详细信息，请参阅[用户手册](https://pcingola.github.io/SnpEff/se_introduction/)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

此数据集是 http://downloads.sourceforge.net/project/snpeff/databases/ 的镜像

## <a name="data-volumes-and-update-frequency"></a>数据卷和更新频率

该数据集大约包含 2 TB 的数据，且每月都会更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，建议将计算资源分配到美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2： https://datasetsnpeff.blob.core.windows.net/dataset

美国中西部： https://datasetsnpeff-secondary.blob.core.windows.net/dataset

[SAS 令牌](../storage/common/storage-sas-overview.md)：sv=2019-10-10&st=2020-09-01T00%3A00%3A00Z&se=2050-09-01T00%3A00%3A00Z&si=prod&sr=c&sig=isafOa9tGnYBAvsXFUMDGMTbsG2z%2FShaihzp7JE5dHw%3D

## <a name="use-terms"></a>使用条款
可随意使用该数据。 有关详细信息和引文详细信息，请参阅[访问和使用 ClinVar 中的数据](https://pcingola.github.io/SnpEff/se_introduction/)。

## <a name="contact"></a>联系人

有关此数据集的任何问题或反馈，请联系 [Pablo Cingolani](http://www.linkedin.com/in/pablocingolani)。


## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
