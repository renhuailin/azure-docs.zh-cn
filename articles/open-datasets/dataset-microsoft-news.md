---
title: Microsoft 资讯推荐数据集
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的 Microsoft 资讯推荐数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 29a7865a06fd96ce4d27892775fb5af728633a02
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982467"
---
# <a name="microsoft-news-recommendation"></a>Microsoft 资讯推荐

Microsoft 资讯数据集（Microsoft News Dataset，MIND）是用于资讯推荐研究的大规模数据集  。 信息收集的来源是 Microsoft 新闻网站的匿名行为日志。 MIND 的使命是用作新闻推荐的基准数据集，并辅助新闻推荐和推荐器系统领域的研究。

MIND 包含大约 16 万篇英文新闻文章和由 100 万个用户生成的超过 1500 万条映像日志。 每篇新闻文章都包含丰富的文字内容，包括标题、摘要、正文、类别和实体。 每条印象日志都包含了该用户在此印象之前的点击事件、非点击事件和历史新闻点击行为。 为了保护用户隐私，每个用户在安全哈希为匿名 ID 时，都与生产系统解除关联。 有关 MIND 数据集的详细信息，请参考论文 [MIND:A Large-scale Dataset for News Recommendation](https://msnews.github.io/assets/doc/ACL2020_MIND.pdf)（MIND：用于新闻推荐的大规模数据集）。
 
## <a name="volume"></a>Volume

训练数据和验证数据都是一个压缩文件夹，其中包含四个不同的文件：

| 文件名 | DESCRIPTION |
|-|-|
| behaviors.tsv | 用户的点击历史记录和映像日志 |
| news.tsv | 新闻文章的信息 |
| entity_embedding.vec | 新闻中从知识图提取的实体的嵌入 |
| relation_embedding.vec | 从知识图提取的实体之间的关系的嵌入 |

### <a name="behaviorstsv"></a>behaviors.tsv

behaviors.tsv 文件包含印象日志和用户的新闻点击历史记录。 该文件具有五列，用制表符分隔：

- 印象 ID。 映像的 ID。
- 用户 ID。 用户的匿名 ID。
- 时间。 印象时间，格式为“MM/DD/YYYY HH:MM:SS AM/PM”。
- “历史记录”。 此用户在此映像之前的新闻点击历史记录（已点击新闻的 ID 列表）。
- 映像。 此印象中显示的新闻列表和用户对这些新闻的点击行为（1 表示点击，0 表示非点击）。

以下表为例：

| COLUMN | CONTENT |
|-|-|
| 印象 ID | 123 |
| 用户 ID | U131 |
| 时间 | 2019/11/13 上午 8:36:57 |
| 历史记录 | N11 N21 N103 |
| 曝光数 | N4-1 N34-1 N156-0 N207-0 N198-0 |

### <a name="newstsv"></a>news.tsv

news.tsv 文件包含 behaviors.tsv 文件中所涉及的新闻文章的详细信息。 该文件具有七列，用制表符分隔：

- 新闻 ID
- 类别
- Subcategory
- 标题
- 摘要
- 代码
- 标题实体（此新闻标题中包含的实体）
- 摘要实体（此新闻摘要中包含的实体）

由于许可结构的原因，MSN 新闻文章的全部内容正文不提供下载。 但为了方便大家，我们提供了一个[实用程序脚本](https://github.com/msnews/MIND/tree/master/crawler)，帮助从数据集中的 MSN URL 解析新闻网页。 由于时间限制，有些 URL 已经过期，因此无法成功访问。 目前，我们正在尽力解决这个问题。

下表中显示了一个示例：

| COLUMN | CONTENT |
|-|-|
| 新闻 ID | N37378 |
| 类别 | 运动 |
| 子类别 | 高尔夫球 |
| 标题 | PGA 巡回赛冠军 |
| 摘要 | PGA 巡回赛新晋冠军图库。 |
| 代码 | https://www.msn.com/en-us/sports/golf/pga-tour-winners/ss-AAjnQjj?ocid=chopendata |
| 标题实体 | [{“Label”: “PGA Tour”, “Type”: “O”, “WikidataId”: “Q910409”, “Confidence”: 1.0, “OccurrenceOffsets”: [0], “SurfaceForms”: [“PGA Tour”]}] |
| 摘要实体 | [{“Label”: “PGA Tour”, “Type”: “O”, “WikidataId”: “Q910409”, “Confidence”: 1.0, “OccurrenceOffsets”: [35], “SurfaceForms”: [“PGA Tour”]}] |

“实体”列中字典键的说明如下：

| 密钥 | DESCRIPTION |
|-|-|
| Label | 维基数据知识图中的实体名称 |
| 类型 | 维基数据中此实体的类型 |
| WikidataId | 维基数据中的实体 ID |
| 置信度 | 实体链接的置信度 |
| OccurrenceOffsets | 标题或摘要的文本中的字符级实体偏移 |
| SurfaceForms | 原文中的原始实体名称 |

### <a name="entity_embeddingvec--relation_embeddingvec"></a>entity_embedding.vec & relation_embedding.vec

entity_embedding.vec 和 relation_embedding.vec 文件包含 TransE 方法从子图（从维基数据知识图）学习到的实体和关系的 100 维嵌入。 在这两个文件中，第一列是实体/关系的 ID，其他列是嵌入向量值。 我们希望这些数据能够促进知识感知新闻推荐的研究。 示例如下所示：

| ID | 嵌入值 |
|-|-|
| Q42306013 | 0.014516 -0.106958 0.024590 … -0.080382 |

由于从子图学习嵌入方面的某些原因，一些实体可能在 entity_embedding.vec 文件中没有嵌入。

## <a name="storage-location"></a>存储位置

数据存储在美国西部/东部数据中心的 Blob 的以下 Blob 容器中： https://mind201910small.blob.core.windows.net/release/ 。

在该容器中，训练和验证数据集分别被压缩为 MINDlarge_train.zip 和 MINDlarge_dev.zip。

## <a name="additional-information"></a>其他信息

根据 [Microsoft 研究许可条款](https://github.com/msnews/MIND/blob/master/MSR%20License_Data.pdf)MIND 数据集可供免费下载用于研究。 如果对数据集有任何疑问，请联系 mind@microsoft.com。

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset)。

## <a name="demo-notebook-for-accessing-mind-data-on-azure"></a>用于访问 Azure 上的 MIND 数据的演示笔记本

此笔记本提供一个示例用于演示如何访问 Azure 上的 Blob 存储中的 MIND 数据。

MIND 数据存储在美国西部/东部数据中心，因此，在美国西部/东部的 Azure 计算上，此笔记本将以更高的效率运行。

### <a name="imports-and-environment"></a>导入和环境

```python
import os
import tempfile
import shutil
import urllib
import zipfile
import pandas as pd

# Temporary folder for data we need during execution of this notebook (we'll clean up
# at the end, we promise)
temp_dir = os.path.join(tempfile.gettempdir(), 'mind')
os.makedirs(temp_dir, exist_ok=True)

# The dataset is split into training and validation set, each with a large and small version.
# The format of the four files are the same.
# For demonstration purpose, we will use small version validation set only.
base_url = 'https://mind201910small.blob.core.windows.net/release'
training_small_url = f'{base_url}/MINDsmall_train.zip'
validation_small_url = f'{base_url}/MINDsmall_dev.zip'
training_large_url = f'{base_url}/MINDlarge_train.zip'
validation_large_url = f'{base_url}/MINDlarge_dev.zip'
```

### <a name="functions"></a>函数


```python
def download_url(url,
                 destination_filename=None,
                 progress_updater=None,
                 force_download=False,
                 verbose=True):
    """
    Download a URL to a temporary file
    """
    if not verbose:
        progress_updater = None
    # This is not intended to guarantee uniqueness, we just know it happens to guarantee
    # uniqueness for this application.
    if destination_filename is None:
        url_as_filename = url.replace('://', '_').replace('/', '_')
        destination_filename = \
            os.path.join(temp_dir,url_as_filename)
    if (not force_download) and (os.path.isfile(destination_filename)):
        if verbose:
            print('Bypassing download of already-downloaded file {}'.format(
                os.path.basename(url)))
        return destination_filename
    if verbose:
        print('Downloading file {} to {}'.format(os.path.basename(url),
                                                 destination_filename),
              end='')
    urllib.request.urlretrieve(url, destination_filename, progress_updater)
    assert (os.path.isfile(destination_filename))
    nBytes = os.path.getsize(destination_filename)
    if verbose:
        print('...done, {} bytes.'.format(nBytes))
    return destination_filename
```

### <a name="download-and-extract-the-files"></a>下载并提取文件

```python
# For demonstration purpose, we will use small version validation set only.
# This file is about 30MB.
zip_path = download_url(validation_small_url, verbose=True)
with zipfile.ZipFile(zip_path, 'r') as zip_ref:
    zip_ref.extractall(temp_dir)

os.listdir(temp_dir)
```

### <a name="read-the-files-with-pandas"></a>使用 pandas 读取文件

```python
# The behaviors.tsv file contains the impression logs and users' news click histories. 
# It has 5 columns divided by the tab symbol:
# - Impression ID. The ID of an impression.
# - User ID. The anonymous ID of a user.
# - Time. The impression time with format "MM/DD/YYYY HH:MM:SS AM/PM".
# - History. The news click history (ID list of clicked news) of this user before this impression.
# - Impressions. List of news displayed in this impression and user's click behaviors on them (1 for click and 0 for non-click).
behaviors_path = os.path.join(temp_dir, 'behaviors.tsv')
pd.read_table(
    behaviors_path,
    header=None,
    names=['impression_id', 'user_id', 'time', 'history', 'impressions'])
```

```python
# The news.tsv file contains the detailed information of news articles involved in the behaviors.tsv file.
# It has 7 columns, which are divided by the tab symbol:
# - News ID
# - Category
# - Subcategory
# - Title
# - Abstract
# - URL
# - Title Entities (entities contained in the title of this news)
# - Abstract Entities (entities contained in the abstract of this news)
news_path = os.path.join(temp_dir, 'news.tsv')
pd.read_table(news_path,
              header=None,
              names=[
                  'id', 'category', 'subcategory', 'title', 'abstract', 'url',
                  'title_entities', 'abstract_entities'
              ])
```

```python
# The entity_embedding.vec file contains the 100-dimensional embeddings
# of the entities learned from the subgraph by TransE method.
# The first column is the ID of entity, and the other columns are the embedding vector values.
entity_embedding_path = os.path.join(temp_dir, 'entity_embedding.vec')
entity_embedding = pd.read_table(entity_embedding_path, header=None)
entity_embedding['vector'] = entity_embedding.iloc[:, 1:101].values.tolist()
entity_embedding = entity_embedding[[0,
                                     'vector']].rename(columns={0: "entity"})
entity_embedding
```

```python
# The relation_embedding.vec file contains the 100-dimensional embeddings
# of the relations learned from the subgraph by TransE method.
# The first column is the ID of relation, and the other columns are the embedding vector values.
relation_embedding_path = os.path.join(temp_dir, 'relation_embedding.vec')
relation_embedding = pd.read_table(relation_embedding_path, header=None)
relation_embedding['vector'] = relation_embedding.iloc[:,
                                                       1:101].values.tolist()
relation_embedding = relation_embedding[[0, 'vector'
                                         ]].rename(columns={0: "relation"})
relation_embedding
```

### <a name="clean-up-temporary-files"></a>清理临时文件

```python
shutil.rmtree(temp_dir)
```

<!-- nbend -->

---

## <a name="examples"></a>示例

请参阅以下示例来了解如何使用 Microsoft 资讯推荐器数据集：

- [MIND 资讯推荐的挑战](https://www.microsoft.com/research/?post_type=msr-academic-program&p=676692&secret=hCWU3V)
- [Microsoft 推荐器存储库](https://github.com/microsoft/recommenders)
- [MIND：Microsoft 资讯数据集](https://msnews.github.io/index.html)


## <a name="next-steps"></a>后续步骤

在 [Microsoft 推荐器存储库](https://github.com/microsoft/recommenders)中查看在 MIND 上开发的多个基线资讯推荐模型


查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
