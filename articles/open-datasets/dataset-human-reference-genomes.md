---
title: 人类参考基因组
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用人类参考基因组数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1e1b1fa785d4ab91329216983c5b694cc484860a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297717"
---
# <a name="human-reference-genomes"></a>人类参考基因组

此数据集包括由[基因组参考联盟](https://www.ncbi.nlm.nih.gov/grc)汇编的两个人类基因组参考：Hg19 和 Hg38。

要详细了解 Hg19 (GRCh37) 数据，请参阅 [NCBI 中的 GRCh37 报告](https://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.13/)。

要详细了解 Hg38 数据，请参阅 [NCBI 中的 GRCh38 报告](http://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.26/)。

有关该数据的其他详细信息，可访问 [NCBI RefSeq](https://www.ncbi.nlm.nih.gov/refseq/) 网站。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

该数据集来源于两个 FTP 位置：

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/

Blob 名称的前缀开头是 URI 的“vertebrate_mammalian”段。

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

该数据集大约包含 10 GB 的数据，且每天都会更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，建议将计算资源分配到美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2：“https://datasetreferencegenomes.blob.core.windows.net/dataset”

美国中西部：“https://datasetreferencegenomes-secondary.blob.core.windows.net/dataset”

[SAS 令牌](../storage/common/storage-sas-overview.md)：sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D

## <a name="use-terms"></a>使用条款

可随意使用该数据。 有关详细信息和引文的详细信息，请参阅 [NCBI 参考序列数据库站点](https://www.ncbi.nlm.nih.gov/refseq/)。

## <a name="contact"></a>联系人

有关此数据集的任何问题或反馈，请联系[基因组参考联盟](https://www.ncbi.nlm.nih.gov/grc/contact-us)。

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes)。

## <a name="getting-the-reference-genomes-from-azure-open-datasets"></a>从 Azure 开放数据集获取参考基因组

[此处](https://azure.microsoft.com/services/open-datasets/catalog/)已上传多个公用基因组学数据作为 Azure 开放数据集。 我们将创建一个链接到此开放数据集的 blob 服务。 在下面可以找到 Azure 开放数据集中 `Reference Genomes` 数据集的数据调用过程示例：

用户可以使用此笔记本调用并下载位于以下路径的数据：“https://datasetreferencegenomes.blob.core.windows.net/dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure/genomic_regions_definitions.txt”

重要说明：用户需要通过 Azure CLI 登录到其 Azure 帐户，以使用 Azure ML SDK 查看数据。 另一方面，他们无需执行任何操作即可下载数据。

[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="calling-the-data-from--reference-genome-datasets"></a>调用“参考基因组数据集”中的数据

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetreferencegenomes.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to genomic_regions_definitions.txt file
REF_DIR = 'vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, 'genomic_regions_definitions.txt')

# read genomic_regions_definitions.txt file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>下载特定文件

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetreferencegenomes',sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D')     
blob_service_client.get_blob_to_path('dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure', 'genomic_regions_definitions.txt', './genomic_regions_definitions.txt')
```

<!-- nbend -->

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。