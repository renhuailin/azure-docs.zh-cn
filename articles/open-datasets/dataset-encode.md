---
title: ENCODE
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的 ENCODE 数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0dc10b580d1a7c6fed8559f15a11e7c54737692d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285162"
---
# <a name="encode-encyclopedia-of-dna-elements"></a>ENCODE：DNA 元件百科全书

[DNA 元件百科全书 (ENCODE) 联盟](https://www.encodeproject.org/help/project-overview/)是由美国国家人类基因组研究所 (NHGRI) 资助的一个持续运作的国际研究小组联盟。 ENCODE 的目标是制作出人类基因组功能元件的综合图谱，其中包含在蛋白质和 RNA 水平上发挥作用的元件，以及控制基因活跃的细胞和环境的调控元件。

ENCODE 研究人员采用各种分析和方法来识别功能元件。 基因元件的发现和标注主要是通过各种 RNA 来源测序、比较基因组学、综合生物信息学方法和人类管理来实现的。 调控元件通常是通过 DNA 超敏反应分析、DNA 甲基化分析，以及与 DNA 和 RNA 相互作用的蛋白质的免疫沉淀反应 (IP) 研究得到的；免疫沉淀反应是指修改组织蛋、转录因子、染色质调节物和 RNA 结合蛋白，然后进行测序。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

此数据集是 https://www.encodeproject.org/ 处的数据存储的镜像

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

此数据集大约包含 756 TB 的数据，每月更新一次，并在每月第一周进行更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，我们建议将计算资源定位在美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2： https://datasetencode.blob.core.windows.net/dataset

美国中西部： https://datasetencode-secondary.blob.core.windows.net/dataset

[SAS 令牌](../storage/common/storage-sas-overview.md)：?sv=2019-10-10&si=prod&sr=c&sig=9qSQZo4ggrCNpybBExU8SypuUZV33igI11xw0P7rB3c%3D

## <a name="use-terms"></a>使用条款

外部数据用户可以不受限制地（不管是在类型还是大小方面）基于任何 ENCODE 数据任意下载、分析和发布结果，并且不为 ENCODE 数据生成者（作为个人成员或作为联盟的一部分）提供宽限期。 对于使用尚未发布的 ENCODE 数据的研究人员，请联系数据生成者以讨论可能的出版。 该联盟将在独立出版中持续发布从其自己的分析工作中得出的结果。

对于在出版和演示中使用 ENCODE 数据集（无论是否已发布）的研究人员，ENCODE 要求按 [https://www.encodeproject.org/help/citing-encode/](https://www.encodeproject.org/help/citing-encode/) 上报告的下列各种方式指出内容引用自 ENCODE 联盟。

## <a name="contact"></a>联系人

如有任何问题、疑虑或看法，请向我们的支持人员发送电子邮件 (encode-help@lists.stanford.edu)。

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。