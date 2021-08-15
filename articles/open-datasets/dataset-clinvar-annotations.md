---
title: ClinVar 注释
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的 ClinVar 注释数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: e5bad3f24d5692bbebb668e29b5ea229294b46f9
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982737"
---
# <a name="clinvar-annotations"></a>ClinVar 注释

[ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/) 是一个可免费访问的公共存档，它对人类变异与相关疾病表型之间关系的报告进行存档，并提供支持证据。 它促进了对所谓的人类变异与观察到的健康状况之间关系的了解和交流，还促进了对这种解释的发展史的了解和交流。 通过它，可访问一组更广泛的临床解释，这些解释可整合到基因组工作流和应用程序中。

有关数据的详细信息，请参阅[数据字典](https://www.ncbi.nlm.nih.gov/projects/clinvar/ClinVarDataDictionary.pdf)和[常见问题解答](https://www.ncbi.nlm.nih.gov/clinvar/docs/faq/)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>数据源

该数据集是 ftp://ftp.ncbi.nlm.nih.gov/pub/clinvar/xml/ 的镜像

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

该数据集大约包含 56 GB 的数据，且每天都会更新。

## <a name="storage-location"></a>存储位置

此数据集存储在 Azure 美国西部 2 和美国中西部区域。 为实现相关性，建议将计算资源分配到美国西部 2 或美国中西部区域。

## <a name="data-access"></a>数据访问

美国西部 2： https://datasetclinvar.blob.core.windows.net/dataset

美国中西部： https://datasetclinvar-secondary.blob.core.windows.net/dataset

[SAS 令牌](../storage/common/storage-sas-overview.md)：sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D

## <a name="use-terms"></a>使用条款
可随意使用该数据。 有关详细信息和引文详细信息，请参阅[访问和使用 ClinVar 中的数据](https://www.ncbi.nlm.nih.gov/clinvar/docs/maintenance_use/)。

## <a name="contact"></a>联系人

有关此数据集的任何问题或反馈，请联系 clinvar@ncbi.nlm.nih.gov。

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar)。

## <a name="getting-the-clinvar-data-from-azure-open-dataset"></a>获取 Azure 开放数据集中的 ClinVar 数据

[此处](https://azure.microsoft.com/services/open-datasets/catalog/)已上传多个公用基因组学数据作为 Azure 开放数据集。 我们将创建一个链接到此开放数据集的 Blob 服务。 在下面可以找到 Azure 开放数据集中 `ClinVar` 数据集的数据调用过程示例：

用户可以使用此笔记本调用和下载位于以下路径的数据： https://datasetclinvar.blob.core.windows.net/dataset/ClinVarFullRelease_00-latest.xml.gz.md5

> [!NOTE]
> 用户需要通过 Azure CLI 登录到其 Azure 帐户，以使用 Azure ML SDK 查看数据。 另一方面，它们无需执行任何操作即可下载数据。

有关安装 Azure CLI 的详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)

### <a name="calling-the-data-from--clinvar-data-set"></a>调用“ClinVar 数据集”中的数据

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetclinvar.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/dataset'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to README file
REF_DIR = '/dataset'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, '_README')

# read README file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>下载特定的文件

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetclinvar', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D')     
blob_service_client.get_blob_to_path('dataset', 'ClinVarFullRelease_00-latest.xml.gz.md5', './ClinVarFullRelease_00-latest.xml.gz.md5')
```

<!-- nbend -->

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
