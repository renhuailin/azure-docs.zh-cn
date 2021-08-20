---
title: 基因组聚合数据库 (gnomAD)
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用基因组聚合数据库 (gnomAD) 数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fe194101c42974e4822c088f7255bfcec5e7a65d
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982629"
---
# <a name="genome-aggregation-database-gnomad"></a>基因组聚合数据库 (gnomAD)

[基因组聚合数据库 (gnomAD)](https://gnomad.broadinstitute.org/) 是一种由国际研究人员联盟开发的资源，其目标是聚合和协调来自各种大型测序项目的外显子组和基因组测序数据，并为更广泛的科学社区提供汇总数据。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

此数据集是与布罗德研究所合作托管的，完整的 gnomAD 数据目录可在 https://gnomad.broadinstitute.org/downloads 上查看

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

此数据集大约包含 30 TB 的数据，且随着每个 gnomAD 版本而更新。

## <a name="storage-location"></a>存储位置

托管此数据集的存储帐户位于美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="data-access"></a>数据访问

存储帐户： https://azureopendatastorage.blob.core.windows.net/gnomad

这些数据是公开提供的，没有任何限制，建议使用 azcopy 工具进行批量操作。 例如，查看 gnomAD 3.0 版中的 VCF：

```powershell
$ azcopy ls https://azureopendatastorage.blob.core.windows.net/gnomad/release/3.0/vcf/genomes
```

以递归方式下载所有 VCF：

```powershell
$ azcopy cp --recursive=true https://azureopendatastorage.blob.core.windows.net/gnomad/release/3.0/vcf/genomes .
```

[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)也是一个浏览 gnomAD 版本中的文件列表的有用工具。

## <a name="use-terms"></a>使用条款

可随意使用该数据。 有关详细信息和引文细节，请参阅 [gnomAD 的“关于”页](https://gnomad.broadinstitute.org/about)。

## <a name="contact"></a>联系人

有关此数据集的任何问题或反馈，请联系 [gnomAD 团队](https://gnomad.broadinstitute.org/contact)。

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
