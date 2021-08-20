---
title: COVID-19 开放式研究数据集
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“COVID-19 开放式研究数据集”。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1225cc9f88fb07766f8f6e89e5a2502bdb83653a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038701"
---
# <a name="covid-19-open-research-dataset"></a>COVID-19 开放式研究数据集

新冠肺炎 (COVID-19) 和冠状病毒相关学术文章的全文内容和元数据数据集已经过优化，便于计算机阅读，同时还提供给全球研究者使用。

为了应对 COVID-19 疫情，[Allen Institute for AI](https://allenai.org/) 与多家先进的研究组织合作，准备并分发 COVID-19 开放式研究数据集 (CORD-19)。 此数据集是一项免费资源，包含关于 COVID-19 和冠状病毒家族的超过 47,000 篇学术论文（其中超过 36,000 篇提供了全文），供全球研究者使用。

这一数据集可以动员研究人员应用自然语言处理方面的最新进展，得出新的见解，支持抗击这一传染性疾病。

随着新的研究成果在经过同行评审的出版物以及 [bioRxiv](https://www.biorxiv.org/) 和 [medRxiv](https://www.medrxiv.org/) 等存档服务上发布，该资料可能会更新。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="license-terms"></a>许可条款

此数据集由 Allen Institute of AI 和 [Semantic Scholar](https://pages.semanticscholar.org/coronavirus-research) 提供。 访问、下载或以其他方式使用新冠肺炎数据集中提供的任何内容即表示你同意与使用该数据集相关的[数据集许可](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/COVID.DATA.LIC.AGMT.pdf)内容。 数据集中单篇文章的特定许可信息请参见元数据文件。 [PMC 网站](https://www.ncbi.nlm.nih.gov/pmc/tools/openftlist/)、[medRxiv 网站](https://www.medrxiv.org/submit-a-manuscript)和 [bioRxiv 网站](https://www.biorxiv.org/about-biorxiv)上提供了更多许可信息。

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 JSON 格式存储，最新版本包含超过 36,000 篇完整论文。 每篇论文都呈现为单个 JSON 对象。 [查看架构](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)。

## <a name="storage-location"></a>存储位置
此数据集存储在美国东部 Azure 区域。 建议将计算资源定位在美国东部，以实现相关性。

## <a name="citation"></a>引文

在出版物或再分发的资料中包含 CORD-19 数据时，请按如下方式引用数据集：

在参考文献中：

COVID-19 开放式研究数据库 (CORD-19)。 2020 年。 版本 YYYY-MM-DD。 检索自 [COVID-19 开放式研究数据库 (CORD-19)](https://pages.semanticscholar.org/coronavirus-research)。 最后访问时间：YYYY-MM-DD。 doi:10.5281/zenodo.3715505

在文本中：（新冠肺炎，2020 年）

## <a name="contact"></a>联系人

若有关于此数据集的任何疑问，请联系 partnerships@allenai.org。

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research)。

## <a name="the-cord-19-dataset"></a>CORD-19 数据集

CORD-19 集合中包含关于 COVID-19、SARS-CoV-2 和相关冠状病毒的 50,000 多篇学术文章（其中超过 40,000 篇提供了全文）。 此数据集已免费提供，目的是帮助研究界对抗 COVID-19 疫情。

此笔记本有两个目标：

1. 演示如何在 Azure 上访问 CORD-19 数据集：连接到存储 CORD-19 数据集的 Azure blob 存储帐户。
2. 演练数据集的结构：数据集中的文章存储为 JSON 文件。 我们提供了示例，其中展示了：

  - 如何查找文章（导航容器）
  - 如何阅读文章（导航 JSON 架构）

依赖项：此笔记本需要以下库：

- Azure 存储（例如 `pip install azure-storage`）
- NLTK（[文档](https://www.nltk.org/install.html)）
- Pandas（例如 `pip install pandas`）

### <a name="getting-the-cord-19-data-from-azure"></a>从 Azure 获取 CORD-19 数据

CORD-19 数据已作为 Azure 开放数据集上传到[此处](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)。 我们将创建一个链接到此 CORD-19 开放数据集的 blob 服务。


```python
from azure.storage.blob import BlockBlobService

# storage account details
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = "sv=2019-02-02&ss=bfqt&srt=sco&sp=rlcup&se=2025-04-14T00:21:16Z&st=2020-04-13T16:21:16Z&spr=https&sig=JgwLYbdGruHxRYTpr5dxfJqobKbhGap8WUtKFadcivQ%3D"

# create a blob service
blob_service = BlockBlobService(
    account_name=azure_storage_account_name,
    sas_token=azure_storage_sas_token,
)
```

我们可以使用此 blob 服务作为数据的句柄。 我们可以利用 `BlockBlobService` API 导航数据集。 请参阅下面的内容了解详细信息：

* [Blob 服务概念](/rest/api/storageservices/blob-service-concepts)
* [对容器执行的操作](/rest/api/storageservices/operations-on-containers)

CORD-19 数据存储在 `covid19temp` 容器中。 下面是容器中的文件结构以及示例文件。

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

每个 .json 文件对应于数据集中的一篇文章。 标题、作者、摘要和（如适用）全文数据都存储在这里。

### <a name="using-metadatacsv"></a>使用 metadata.csv
CORD-19 数据集附带一个 `metadata.csv`，这个文件会记录有关 CORD-19 数据集中提供的所有论文的基本信息。 建议从这里开始探索！


```python
# container housing CORD-19 data
container_name = "covid19temp"

# download metadata.csv
metadata_filename = 'metadata.csv'
blob_service.get_blob_to_path(
    container_name=container_name,
    blob_name=metadata_filename,
    file_path=metadata_filename
)
```

```python
import pandas as pd

# read metadata.csv into a dataframe
metadata_filename = 'metadata.csv'
metadata = pd.read_csv(metadata_filename)
```

```python
metadata.head(3)
```

粗略看一下会发现内容太多了，所以我们来稍微精简一下。

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```

```
There are 51078 many entries in this dataset:
-- 42511 have full text entries
-- 47741 have DOIs
-- 41082 have PubMed Central (PMC) ids
-- 964 have Microsoft Academic paper ids
```

## <a name="example-read-full-text"></a>示例：阅读全文

`metadata.csv` 不包含全文本身。 我们来看一个如何阅读全文的示例。 找到并解压缩全文 JSON，并将其转换为句子的列表。

```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pdf_json/{1}.json'.format(example_entry['full_text_file'], example_entry['sha'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)
```

现在，我们可以读取与此 blob 关联的 JSON 内容，如下所示。

```python
import json
blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

在本例中，我们感兴趣的是 `body_text`，它按如下方式存储文本数据：

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

[此处](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)提供了完整的 JSON 架构。


```python
from nltk.tokenize import sent_tokenize

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```    

### <a name="pdf-vs-pmc-xml-parse"></a>PDF 与 PMC XML 分析

在上面的示例中，我们看到了一个使用 `has_pdf_parse == True` 的示例。 其中，blob 文件路径采用了如下格式：

```
'<full_text_file>/pdf_json/<sha>.json'
```

或者，对于使用 `has_pmc_xml_parse == True` 的示例，使用了以下格式：

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

例如：


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)

blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
```
Full text blob for this entry:
custom_license/pmc_json/PMC546170.xml.json
An example sentence: Double-stranded small interfering RNA (siRNA) molecules have drawn much attention since it was unambiguously shown that they mediate potent gene knock-down in a variety of mammalian cells (1).
```    

## <a name="iterate-through-blobs-directly"></a>直接循环访问 blob

在上述示例中，我们使用了 `metadata.csv` 文件导航数据、构造 blob 文件路径并从 blob 读取数据。 有一种替代方法是循环访问 blob 本身。


```python
# get and sort list of available blobs
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

现在，我们可以直接循环访问 blob。 例如，让我们来计算可用的 JSON 文件数。


```python
# we can now iterate directly though the blobs
count = 0
for blob in sorted_blobs:
    if blob.name[-5:] == ".json":
        count += 1
print("There are {} many json files".format(count))
```

```
There are 59784 many json files
```  

## <a name="appendix"></a>附录

### <a name="data-quality-issues"></a>数据质量问题

这是一个大型数据集，由于明显的原因，它在仓促的情况下被放在一起！ 下面是我们观察到的一些数据质量问题。

#### <a name="multiple-shas"></a>多个 sha

我们观察到，在某些情况下，给定条目有多个 sha。

```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```
```
There are 1999 many entries with multiple shas
```

## <a name="layout-of-the-container"></a>容器的布局

在这里，我们使用简单的正则表达式来浏览容器的文件结构，以防将来更新。

```python
container_name = "covid19temp"
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

```python
import re
dirs = {}

pattern = '([\w]+)\/([\w]+)\/([\w.]+).json'
for blob in sorted_blobs:
    
    m = re.match(pattern, blob.name)
    
    if m:
        dir_ = m[1] + '/' + m[2]
        
        if dir_ in dirs:
            dirs[dir_] += 1
        else:
            dirs[dir_] = 1
        
dirs
```

<!-- nbend -->

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research)。

## <a name="the-cord-19-dataset"></a>CORD-19 数据集

CORD-19 集合中包含关于 COVID-19、SARS-CoV-2 和相关冠状病毒的 50,000 多篇学术文章（其中超过 40,000 篇提供了全文）。 此数据集已免费提供，目的是帮助研究界对抗 COVID-19 疫情。

此笔记本有两个目标：

1. 演示如何在 Azure 上访问 CORD-19 数据集：我们使用 AzureML 数据集为 CORD-19 数据提供上下文。
2. 演练数据集的结构：数据集中的文章存储为 JSON 文件。 我们提供了示例，其中展示了：

  - 如何查找文章（导航目录结构）
  - 如何阅读文章（导航 JSON 架构）

依赖项：此笔记本需要以下库：

- AzureML Python SDK（例如 `pip install --upgrade azureml-sdk`）
- Pandas（例如 `pip install pandas`）
- NLTK（[文档](https://www.nltk.org/install.html)）（例如 `pip install nltk`）

如果 NLTK 没有 `punkt` 包，则需要运行：

```
import nltk
nltk.download('punkt')
```

### <a name="getting-the-cord-19-data-from-azure"></a>从 Azure 获取 CORD-19 数据

CORD-19 数据已作为 Azure 开放数据集上传到[此处](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)。 在此笔记本中，我们使用 AzureML [数据集](/python/api/azureml-core/azureml.core.dataset.dataset) 来引用 CORD-19 开放数据集。

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
cord19_dataset = Dataset.File.from_files('https://azureopendatastorage.blob.core.windows.net/covid19temp')
mount = cord19_dataset.mount()
```

`mount()` 方法创建了一个上下文管理器，用于将数据集定义的文件系统流装载为本地文件。

使用 `mount.start()` 和 `mount.stop()`，或者也可以使用 `with mount():` 来管理上下文。

仅在 Unix 或类似 Unix 的操作系统上支持装载，并且 libfuse 必须存在。 如果是在 docker 容器内运行，则 docker 容器必须以 `--privileged` 标记开头或以 `--cap-add SYS_ADMIN --device /dev/fuse` 开头。 有关详细信息，请参阅[文档](/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py#mount-mount-point-none----kwargs-&preserve-view=true)


```python
import os

COVID_DIR = '/covid19temp'
path = mount.mount_point + COVID_DIR

with mount:
    print(os.listdir(path))
```
```
['antiviral_with_properties_compressed.sdf', 'biorxiv_medrxiv', 'biorxiv_medrxiv_compressed.tar.gz', 'comm_use_subset', 'comm_use_subset_compressed.tar.gz', 'custom_license', 'custom_license_compressed.tar.gz', 'metadata.csv', 'noncomm_use_subset', 'noncomm_use_subset_compressed.tar.gz']
```

下面是 CORD-19 数据集中的文件结构以及示例文件。

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

每个 .json 文件对应于数据集中的一篇文章。 标题、作者、摘要和（如适用）全文数据都存储在这里。

## <a name="using-metadatacsv"></a>使用 metadata.csv

CORD-19 数据集附带一个 `metadata.csv`，这个文件会记录有关 CORD-19 数据集中提供的所有论文的基本信息。 建议从这里开始探索！


```python
import pandas as pd

# create mount context
mount.start()

# specify path to metadata.csv
COVID_DIR = 'covid19temp'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, COVID_DIR, 'metadata.csv')

# read metadata
metadata = pd.read_csv(metadata_filename)
metadata.head(3)
```

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```    

### <a name="example-read-full-text"></a>示例：阅读全文

`metadata.csv` 不包含全文本身。 我们来看一个如何阅读全文的示例。 找到并解压缩全文 JSON，并将其转换为句子的列表。


```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
filepath = '{0}/{1}/pdf_json/{2}.json'.format(path, example_entry['full_text_file'], example_entry['sha'])
print("Full text filepath:")
print(filepath)
```    

现在，我们可以读取与此文件关联的 JSON 内容，如下所示。

```python
import json

try:
    with open(filepath, 'r') as f:
        data = json.load(f)
except FileNotFoundError as e:
    # in case the mount context has been closed
    mount.start()
    with open(filepath, 'r') as f:
        data = json.load(f)
        
# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

```
Keys within data: paper_id, metadata, abstract, body_text, bib_entries, ref_entries, back_matter
```

在本例中，我们感兴趣的是 `body_text`，它按如下方式存储文本数据：

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

查看[完整的 JSON 架构](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)。


```python
from nltk.tokenize import sent_tokenize
# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
    

#### <a name="pdf-vs-pmc-xml-parse"></a>PDF 与 PMC XML 分析

在上面的示例中，我们看到了一个使用 `has_pdf_parse == True` 的示例。 其中，文件路径采用如下格式：

```
'<full_text_file>/pdf_json/<sha>.json'
```

或者，对于使用 `has_pmc_xml_parse == True` 的示例，使用了以下格式：

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

例如：


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
filename = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Path to file: {}\n".format(filename))

with open(mount.mount_point + '/' + COVID_DIR + '/' + filename, 'r') as f:
    data = json.load(f)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```

## <a name="appendix"></a>附录

### <a name="data-quality-issues"></a>数据质量问题

这是一个大型数据集，由于明显的原因，它在仓促的情况下被放在一起！ 下面是我们观察到的一些数据质量问题。


```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```

<!-- nbend -->

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。