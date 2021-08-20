---
title: Illumina Platinum Genomes
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用 Illumina Platinum Genomes 数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0a1b9d5a41a0dcc45bea0456c2c3714b787a5af8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284241"
---
# <a name="illumina-platinum-genomes"></a>Illumina Platinum Genomes

通过全基因组测序，世界各地的研究人员能够更完整、更准确地描绘人类基因组的特征。 这需要在一套基因组中调用高置信度变体的综合全基因组目录用作基准。 Illumina 对三代遗传系谱中的 17 个个体生成了深入的全基因组测序数据。 Illumina 使用一系列目前可用的算法在每个基因组中调用了变体。

有关数据的详细信息，请参阅 [Illumina 官网](https://www.illumina.com/platinumgenomes.html)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

该数据集是 ftp://ussd-ftp.illumina.com/ 的镜像

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

该数据集大约包含 2 GB 的数据，且每天都会更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，我们建议将计算资源定位在美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2： https://datasetplatinumgenomes.blob.core.windows.net/dataset

美国中西部： https://datasetplatinumgenomes-secondary.blob.core.windows.net/dataset

[SAS 令牌](../storage/common/storage-sas-overview.md)：sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D

## <a name="use-terms"></a>使用条款

可随意使用该数据。 有关详细信息和引文细节，请参阅 [Illumina 官网](https://www.illumina.com/platinumgenomes.html)。

## <a name="contact"></a>联系人

有关此数据集的任何问题或反馈，请联系 platinumgenomes@illumina.com。

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes -->

> [!TIP]
> [改为下载数据集](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes)。

## <a name="getting-the-illumina-platinum-genomes-from-azure-open-datasets-and-doing-initial-analysis"></a>从 Azure 开放数据集中获取 Illumina Platinum Genomes 并进行初步分析 

使用 Jupyter 笔记本、GATK 和 Picard 执行以下操作：

1. 使用 VariantFiltration 为基因型添加批注
2. 选择特定变体
3. 筛选相关变体 - no call 或特定区域
4. 执行索引分析
5. 将最终的 VCF 文件转换为表 

**依赖项：**

此笔记本需要以下库：

- Azure 存储 `pip install azure-storage-blob`

- numpy `pip install numpy`

- 基因组分析工具包 (GATK)（用户需要从 Broad Institute 的网页将 GATK 下载到与此笔记本相同的计算环境中： https://github.com/broadinstitute/gatk/releases ）

重要信息：此笔记本使用的是 Python 3.6 内核

## <a name="getting-the-genomics-data-from-azure-open-datasets"></a>从 Azure 开放数据集中获取基因组学数据

[此处](https://azure.microsoft.com/services/open-datasets/catalog/)已上传多个公用基因组学数据作为 Azure 开放数据集。 我们将创建一个链接到此开放数据集的 blob 服务。 在下面可以找到 Azure 开放数据集中 `Illumina Platinum Genomes` 数据集的数据调用过程示例：

### <a name="downloading-the-specific-illumina-platinum-genomes"></a>下载特定的“Illumina Platinum Genomes”

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetplatinumgenomes', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D')     
blob_service_client.get_blob_to_path('dataset/2017-1.0/hg38/small_variants/NA12877', 'NA12877.vcf.gz', './NA12877.vcf.gz')
```

### <a name="1-annotate-genotypes-using-variantfiltration"></a>1. 使用 VariantFiltration 为基因型添加批注

重要说明：检查 GATK 是否在系统上运行。

若想筛选杂合基因型，则使用 VariantFiltration 的 `--genotype-filter-expression isHet == 1` 选项。 我们可以指定工具的批注值以使用 `--genotype-filter-name` 选项标记杂合基因型。 此处，此参数的值设置为 `isHetFilter`。 在第一个示例中，我们使用了来自 Illimina Platinum Genomes 的 `NA12877.vcf.gz`，但用户可以使用来自其他数据集的任何 vcf 文件：`Platinum Genomes`

```python
run gatk VariantFiltration -V NA12877.vcf.gz -O outputannot.vcf --genotype-filter-expression "isHet == 1" --genotype-filter-name "isHetFilter"
```

### <a name="2-select-specific-variants"></a>2. 选择特定的变体

从 VCF 文件中选择变体子集。 使用此工具可以根据各种条件选择变体子集，以便于进行某些分析。 此类分析的示例包括比较和对比案例与对照、提取满足特定要求的变体或非变体基因座，或排除一些意外结果等。

有多种不同的选项可用于从较大的调用集中选择变体子集：

根据完整的示例名称或模式匹配从调用集中提取一个或多个示例。
指定包含对批注值施加阈值的条件，例如“DP > 1000”（覆盖率大于 1000 倍）、“AF < 0.25”（等位基因频率小于 0.25 的位点）。 这些条件以“JEXL 表达式”的形式编写，这些表达式在有关使用 JEXL 表达式的文章中进行了介绍。
提供一致或不一致的跟踪，以包括或排除其他给定调用集中也存在的变体。
根据类型（例如，仅 INDEL）、mendelian violation 证据、筛选状态、等位基因等标准选择变体。还有几个选项可用于记录特定批注的原始值，当对新调用集进行子集化、修剪等位基因等时，会重新计算这些值。

输入：VCF 格式的变体调用集，可以从中选择子集。

输出：包含所选变体子集的新 VCF 文件。

```python
run gatk SelectVariants -R Homo_sapiens_assembly38.fasta -V outputannot.vcf --select-type-to-include SNP --select-type-to-include INDEL -O selective.vcf
```

### <a name="3-transform-filtered-genotypes-to-no-call"></a>3. 将筛选后的基因型转化为 no call 

使用 --set-filtered-gt-to-nocall 运行 SelectVariants 将进一步转换带有空基因型调用的标记基因型。 

这种转换是必要的，因为下游工具不解析 FORMAT 级别的筛选器字段。

如何使用“No call”筛选变体

```python
run gatk SelectVariants -V outputannot.vcf --set-filtered-gt-to-nocall -O outputnocall.vcf
```

### <a name="4-check-the-concordance-of-vcf-file-with-ground-truth"></a>4. 检查 VCF 文件与真实情况的一致性

评估输入 VCF 与 truth VCF 的站点级一致性。
此工具会相互评估两个变体调用集，并生成一个包含六列的汇总指标表。 

此函数将：

1. 分层 SNP 和 INDEL 调用
2. 报告真正、假正和假负调用
3. 计算敏感度和精度

该工具假定 --truth VCF 中的所有记录都在传递真实变体。 对于 -eval VCF，该工具只使用未筛选的传递调用。

（可选）可以将该工具设置为生成以下变体记录的 VCF，并以每个变体的索引状态进行批注：

真正和假负（即 truth VCF 中的所有变体）：用于计算敏感度

真正和假正（即 eval VCF 中的所有变体）：用于获取机器学习生成工件分类器的定型数据集

可以将这些输出 VCF 传递到 VariantsToTable 以生成 TSV 文件，以便在 R 或 Python 中进行统计分析。

```python
 run gatk Concordance -R Homo_sapiens_assembly38.fasta -eval outputannot.vcf --truth outputnocall.vcf  --summary summary.tsv 
```

### <a name="5-variantstotable"></a>5. VariantsToTable

将字段从 VCF 文件提取到以制表符分隔的表中。 此工具将 VCF 文件中每个变体的指定字段提取到以制表符分隔的表中，这可能比 VCF 更易于使用。 默认情况下，该工具仅提取 VCF 文件中的 PASS 或（未筛选）变体。 通过添加--show Filtered 标志，可以在输出中包括筛选的变体。 该工具可以提取 INFO（即站点级）字段和 FORMAT（即示例级）字段。

INFO/站点级字段：

使用 `-F` 参数提取 INFO 字段；每个字段将占用输出文件中的一列。 该字段可以是任何标准 VCF 列（例如 CHROM、ID、QUAL）或 INFO 字段中的任何批注名称（例如 AC、AF）。 该工具还支持以下字段：

EVENTLENGTH（事件的长度）、TRANSITION（1 表示双等位转换 (SNP)，0 表示双等位颠换 (SNP)，-1 表示 INDEL 和多等位基因）、HET（杂合子基因型的数量）、HOM-REF（纯合子参考基因型的数量）、HOM-VAR（纯合子变体基因型的数量）、NO-CALL（非调用基因型的数量）、TYPE（变体的类型，可能的值有 NO_VARIATION、SNP、MNP、INDEL、SYMBOLIC 和 MIXED VAR（非参考基因型的数量）、NSAMPLES（样本数）、NCALLED（调用样本的数量）、MULTI-ALLELIC（此样本是否为多等位基因）

FORMAT/示例级字段：

使用 `-GF` 参数提取 FORMAT/示例级字段。 该工具将为每个示例创建一个名为“SAMPLE_NAME.FORMAT_FIELD_NAME”的新列，例如 NA12877.GQ, NA12878.GQ。

输入：

要转换为表的 VCF 文件

输出：

以制表符分隔的文件，包含 VCF 文件中请求字段的值。

```python
run gatk VariantsToTable -V NA12877.vcf.gz -F CHROM -F POS -F TYPE -F AC -F AD -F AF -GF DP -GF AD -O outputtable.table
```

## <a name="references"></a>参考

1. VariantFiltration： https://gatk.broadinstitute.org/hc/en-us/articles/360036827111-VariantFiltration 
2. 选择变体： https://gatk.broadinstitute.org/hc/en-us/articles/360037052272-SelectVariants
3. 索引： https://gatk.broadinstitute.org/hc/en-us/articles/360041851651-Concordance
4. 表的变体： https://gatk.broadinstitute.org/hc/en-us/articles/360036882811-VariantsToTable 
5. Illumina Platinum Genomes： https://www.illumina.com/platinumgenomes.html 

<!-- nbend -->

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。