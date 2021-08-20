---
title: GATK 资源包
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用 GATK 资源包数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 6ee4987e21acc280a9835bec3457b567f689433f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285124"
---
# <a name="gatk-resource-bundle"></a>GATK 资源包

[GATK 资源包](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)是标准文件的集合，用于与 GATK 的人类基因序列数据结合使用。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

此数据集是 https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle 处的数据存储的镜像

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

1. datasetgatkbestpractices：542 GB
1. datasetgatklegacybundles：61 GB
1. datasetgatktestdata：2 TB
1. datasetpublicbroadref：477 GB
1. datasetbroadpublic：3 TB

数据集每月更新一次，在每月第一周进行更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，建议将计算资源分配到美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

1. datasetgatkbestpractices

    美国西部 2： https://datasetgatkbestpractices.blob.core.windows.net/dataset
    
    美国中西部： https://datasetgatkbestpractices-secondary.blob.core.windows.net/dataset
    
    [SAS 令牌](../storage/common/storage-sas-overview.md)：?sv=2020-04-08&si=prod&sr=c&sig=6SaDfKtXAIfdpO%2BkvNA%2FsTNmNij%2Byh%2F%2F%2Bf98WAUqs7I%3D

2. datasetgatklegacybundles

    美国西部 2： https://datasetgatklegacybundles.blob.core.windows.net/dataset
    
    美国中西部： https://datasetgatklegacybundles-secondary.blob.core.windows.net/dataset
    
    [SAS 令牌](../storage/common/storage-sas-overview.md)：?sv=2020-04-08&si=prod&sr=c&sig=xBfxOPBqHKUCszzwbNCBYF0k9osTQjKnZbEjXCW7gU0%3D

3. datasetgatktestdata

    美国西部 2： https://datasetgatktestdata.blob.core.windows.net/dataset
    
    美国中西部： https://datasetgatktestdata-secondary.blob.core.windows.net/dataset
    
    [SAS 令牌](../storage/common/storage-sas-overview.md)：?sv=2020-04-08&si=prod&sr=c&sig=fzLts1Q2vKjuvR7g50vE4HteEHBxTcJbNvf%2FZCeDMO4%3D

4. datasetpublicbroadref
    
    美国西部 2： https://datasetpublicbroadref.blob.core.windows.net/dataset
    
    美国中西部： https://datasetpublicbroadref-secondary.blob.core.windows.net/dataset
    
    [SAS 令牌](../storage/common/storage-sas-overview.md)：?sv=2020-04-08&si=prod&sr=c&sig=DQxmjB4D1lAfOW9AxIWbXwZx6ksbwjlNkixw597JnvQ%3D

5. datasetbroadpublic

    美国西部 2： https://datasetpublicbroadpublic.blob.core.windows.net/dataset
    
    美国中西部： https://datasetpublicbroadpublic-secondary.blob.core.windows.net/dataset
    
    [SAS 令牌](../storage/common/storage-sas-overview.md)：?sv=2020-04-08&si=prod&sr=c&sig=u%2Bg2Ab7WKZEGiAkwlj6nKiEeZ5wdoJb10Az7uUwis%2Fg%3D

## <a name="use-terms"></a>使用条款

访问 [GATK 资源包官方网站](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)。

## <a name="contact"></a>联系人

访问 [GATK 资源包官方网站](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)。

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。