---
title: 创建 Azure 机器学习数据集
titleSuffix: Azure Machine Learning
description: 了解如何创建 Azure 机器学习数据集以访问机器学习试验运行的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: contperf-fy21q1, data4ml
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 07/06/2021
ms.openlocfilehash: f640165420f06a85633d4db30d6338f4bfa205c4
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428362"
---
# <a name="create-azure-machine-learning-datasets"></a>创建 Azure 机器学习数据集

本文介绍如何使用 Azure 机器学习 Python SDK 创建 Azure 机器学习数据集，以访问本地或远程试验的数据。 若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据集，请参阅[安全地访问数据](concept-data.md#data-workflow)一文。

通过创建数据集，可以创建对数据源位置的引用及其元数据的副本。 由于数据保留在其现有位置中，因此不会产生额外的存储成本，也不会损害数据源的完整性。 此外，还会对数据集进行延迟计算，这有助于提高工作流执行速度。 可以从数据存储、公共 URL 和 [Azure 开放数据集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)创建数据集。

要获得低代码体验，请[使用 Azure 机器学习工作室创建 Azure 机器学习数据集。](how-to-connect-data-ui.md#create-datasets)

使用 Azure 机器学习数据集可以：

* 在存储中保留数据的单个副本，供数据集引用。

* 在模型训练期间无缝访问数据，而无需考虑连接字符串或数据路径。 [详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)。

* 与其他用户共享数据和展开协作。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集，需要做好以下准备：

* Azure 订阅。 如果没有订阅，请在开始之前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install)，其中包含 azureml-datasets 包。

    * 创建一个 [Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)，它是一个完全配置且托管的开发环境，其中包括集成的笔记本和已安装的 SDK。

    **或者**

    * 使用自己的 Jupyter 笔记本，并[自行安装 SDK](/python/api/overview/azure/ml/install)。

> [!NOTE]
> 某些数据集类依赖于 [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) 包，此包仅兼容64位 Python。 如果在 Linux 上进行开发，这些类将依赖于 .NET Core 2.1，且仅在特定的分发版上受支持。 有关支持的发行版的详细信息，请参阅[在 Linux 上安装 .NET](/dotnet/core/install/linux) 一文中的 .NET Core 2.1 列。

> [!IMPORTANT]
> 尽管包也许可以在较旧版本的 Linux 发行版上运行，但建议不要使用不含主要支持的发行版。 不含主要支持的发行版可能有安全漏洞，因为它们不会收到最新更新。 建议使用兼容的受支持的最新版本的发行版。

## <a name="compute-size-guidance"></a>计算大小指南

创建数据集时，请检查计算处理能力和内存中的数据大小。 存储中的数据大小不同于数据帧中的数据大小。 例如，CSV 文件中的数据在数据帧中最多可以扩展到 10 倍，因此，1 GB 的 CSV 文件在数据帧中可能会变成 10 GB。 

如果数据已压缩，则可进一步让其扩展；以压缩 parquet 格式存储的 20 GB 相对稀疏的数据可以在内存中扩展到大约 800 GB。 由于 Parquet 文件以纵栏格式存储数据，因此，如果只需要一半的列，则只需在内存中加载大约 400 GB。

[详细了解如何优化 Azure 机器学习中的数据处理](concept-optimize-data-processing.md)。

## <a name="dataset-types"></a>数据集类型

存在两种数据集类型：FileDataset 和 TabularDataset，具体取决于用户在训练中使用它们的方式。 这两种类型均可用于涉及估算器、AutoML、hyperDrive 和管道的 Azure 机器学习训练工作流。 

### <a name="filedataset"></a>FileDataset

[FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) 引用数据存储或公共 URL 中的单个或多个文件。 如果数据已经清理，并且可以在训练试验中使用，则可将文件作为 FileDataset 对象[下载或装载](how-to-train-with-datasets.md#mount-vs-download)到计算中。 

建议将 FileDataset 用于机器学习工作流，因为源文件可以采用任何格式，因此可以实现更广泛的机器学习方案，包括深度学习。

使用 [Python SDK](#create-a-filedataset) 或 [Azure 机器学习工作室](how-to-connect-data-ui.md#create-datasets)创建 FileDataset。
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) 通过分析提供的文件或文件列表来以表格格式表示数据。 这样你就可以将数据具体化为 pandas 或 Spark 数据帧，以便进行熟悉的数据准备并训练库，不需离开笔记本。 可以从 .csv、.tsv、[.parquet](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)、[.jsonl 文件](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)和 [SQL 查询结果](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)创建 `TabularDataset` 对象。

使用 TabularDataset 时，可以从数据中的列或者从数据所存储到的任何路径模式指定一个时间戳，以启用时序特征。 此规范允许按时间轻松有效地进行筛选。 有关示例，请参阅[使用 NOAA 天气数据的表格时序相关 API 演示](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

使用 [Python SDK](#create-a-tabulardataset) 或 [Azure 机器学习工作室](how-to-connect-data-ui.md#create-datasets)创建 TabularDataset。

>[!NOTE]
> 通过 Azure 机器学习工作室生成的[自动化 ML](concept-automated-ml.md) 工作流目前仅支持 TabularDataset。 

## <a name="access-datasets-in-a-virtual-network"></a>访问虚拟网络中的数据集

如果工作区位于虚拟网络中，则必须将数据集配置为跳过验证。 有关如何在虚拟网络中使用数据存储和数据集的详细信息，请参阅[保护工作区和相关资源](how-to-secure-workspace-vnet.md#datastores-and-datasets)。

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>从数据存储创建数据集

要使 Azure 机器学习可以访问数据，必须从 [Azure 机器学习数据存储](how-to-access-data.md)中的路径或 Web URL 创建数据集。 

> [!TIP] 
> 可以通过基于标识的数据访问直接从存储 URL 创建数据集。 若要了解详细信息，请参阅[通过基于标识的数据访问连接到存储（预览版）](how-to-identity-based-data-access.md)<br><br>
此功能是一个[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。 

 
若要使用 Python SDK 从数据存储创建数据集，请执行以下操作：

1. 确认你是否具有对注册的 Azure 机器学习数据存储的基础存储服务的 `contributor` 或 `owner` 访问权限。 [在 Azure 门户中检查存储帐户权限](../role-based-access-control/check-access.md)。

1. 通过引用数据存储中的路径创建数据集。 可以从多个数据存储中的多个路径创建数据集。 可以从其创建数据集的文件数量或数据大小没有硬性限制。 

> [!NOTE]
> 对于每个数据路径，会将几个请求发送到存储服务，以检查它是否指向文件或文件夹。 此开销可能会导致性能下降或故障。 数据集引用内含 1000 个文件的文件夹的行为被视为引用一个数据路径。 为了获得最佳性能，建议创建在数据存储中引用不到 100 个路径的数据集。

### <a name="create-a-filedataset"></a>创建 FileDataset

使用 `FileDatasetFactory` 类中的 [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) 方法可以加载任意格式的文件并创建未注册的 FileDataset。 

如果存储位于虚拟网络或防火墙后面，请在 `from_files()` 方法中设置参数 `validate=False`。 这会绕过初始验证步骤，确保可以从这些安全文件创建数据集。 详细了解如何[使用虚拟网络中的数据存储和数据集](how-to-secure-workspace-vnet.md#datastores-and-datasets)。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
若要在工作区的不同试验中重用和共享数据集，请[注册数据集](#register-datasets)。 

> [!TIP] 
> 在一个方法中使用公共预览版方法 [upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-) 从本地目录上传文件，并创建一个 FileDataset。 此方法是一个[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。 
> 
>  此方法会将数据上传到基础存储，因此会产生存储费用。 

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

使用 `TabularDatasetFactory` 类中的 [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) 方法可以读取 .csv 或 .tsv 格式的文件，以及创建未注册的 TabularDataset。 若要读取 .parquet 格式的文件，请使用 [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 方法。 如果从多个文件进行读取，结果将聚合为一种表格表示形式。 

有关支持的文件格式以及语法和设计模式（如[多行支持](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--)）的信息，请参阅 [TabularDatasetFactory 参考文档](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory)。 

如果存储位于虚拟网络或防火墙后面，请在 `from_delimited_files()` 方法中设置参数 `validate=False`。 这会绕过初始验证步骤，确保可以从这些安全文件创建数据集。 详细了解如何使用[虚拟网络中的数据存储和数据集](how-to-secure-workspace-vnet.md#datastores-and-datasets)。

以下代码按名称获取现有工作区和所需的数据存储。 然后将数据存储和文件位置传递给 `path` 参数以创建新的 TabularDataset `weather_ds`。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>设置数据架构

默认情况下，在创建 TabularDataset 时，将自动推断列数据类型。 如果推理类型不满足你的要求，可以通过使用以下代码指定列类型来更新数据集架构。 参数 `infer_column_type` 仅适用于从分隔文件创建的数据集。 [详细了解支持的数据类型](/python/api/azureml-core/azureml.data.dataset_factory.datatype)。


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|（索引）|PassengerId|Survived|Pclass|名称|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|女|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|女|26.0|0|0|STON/O2. 3101282|7.9250||S

若要在工作区的不同试验中重用和共享数据集，请[注册数据集](#register-datasets)。

## <a name="wrangle-data"></a>处理数据
创建并[注册](#register-datasets)数据集之后，可以在模型训练之前将其加载到笔记本中以进行数据处理和[浏览](#explore-data)。 

如果不需要进行任何数据处理或浏览，请参阅[使用数据集进行训练](how-to-train-with-datasets.md)，了解如何在训练脚本中使用数据集，以便提交 ML 试验。

### <a name="filter-datasets-preview"></a>筛选数据集（预览版）

筛选功能取决于你拥有的数据集的类型。 
> [!IMPORTANT]
> 使用预览方法筛选数据集，[`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) 是一个[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。 
> 
对于 TabularDatasets，可以使用 [keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) 和 [drop_columns()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) 方法保留或删除列。

若要按 TabularDataset 中的特定列值筛选行，请使用 [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) 方法（预览版）。 

以下示例基于指定的表达式返回未注册的数据集。

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

在 FileDatasets 中，每一行均对应一个文件路径，因此按列值筛选不起作用。 但是，可以按元数据（如 CreationTime、大小等）对行进行 [filter()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-)。

以下示例基于指定的表达式返回未注册的数据集。

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

从[图像标记项目](how-to-create-image-labeling-projects.md)创建的标记数据集是一种特殊情况。 这些数据集是由图像文件组成的 TabularDataset 的类型。 对于这两种类型的数据集，可以按元数据和列值（如 `label` 和 `image_details`）对图像进行 [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-)。

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>对数据进行分区（预览）

可以通过在创建 TabularDataset 或 FileDataset 时包含 `partitions_format` 参数来对数据集进行分区。 

> [!IMPORTANT]
> 创建数据集分区是一个[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。 

对数据集进行分区时，每个文件路径的分区信息都将根据指定的格式提取到列中。 格式应从文件路径结束之前的第一个分区键位置开始。 

例如，给定路径 `../Accounts/2019/01/01/data.jsonl`，其中分区是按部门名称和时间划分的；`partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` 创建值为“Accounts”的字符串列“Department”和值为 `2019-01-01` 的日期/时间列“PartitionDate”。

如果数据已有现有分区，并希望保留该格式，请在 [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) 方法中包含 `partitioned_format` 参数以创建 FileDataset。 

若要创建保留现有分区的 TabularDataset，请在 [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 或 [from_delimited_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) 方法中包含 `partitioned_format` 参数。

下面的示例，
* 从分区文件创建 FileDataset。
* 获取分区键
* 创建新的已编制索引的 FileDataset
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

还可以使用 [partitions_by()](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-) 方法为 TabularDatasets 创建新的分区结构。

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

## <a name="explore-data"></a>浏览数据

处理完数据后，可以[注册](#register-datasets)数据集，然后在模型训练之前将其加载到笔记本中以进行数据浏览。

对于 FileDataset，可以装载或下载数据集，并应用通常用于数据浏览的 python 库。 [详细了解如何装载和下载](how-to-train-with-datasets.md#mount-vs-download)。

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

对于 TabularDataset，请使用 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法来查看数据帧中的数据。 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|（索引）|PassengerId|Survived|Pclass|名称|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|女|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|女|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>从 pandas 数据帧创建数据集

若要从内存中 pandas 数据帧创建 TabularDataset，请将数据写入本地文件（例如 csv），然后从该文件创建数据集。 下面的代码演示了此工作流。

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> 在一个方法中使用公共预览版方法 [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) 和 [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) 从内存中 spark 或 pandas 数据帧创建并注册一个 TabularDataset。 这些注册方法属于[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。 
> 
>  这些方法会将数据上传到基础存储，因此会产生存储费用。 

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请将数据集注册到工作区。 使用 [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) 方法将数据集注册到工作区，以便与其他人共享，并在工作区中的实验中重复使用这些数据集：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>使用 Azure 资源管理器创建数据集

[https://github.com/Azure/azure-quickstart-templates/tree/master//quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices) 上有许多模板可用于创建数据集。

若要了解如何使用这些模板，请参阅[使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。


## <a name="create-datasets-from-azure-open-datasets"></a>从 Azure 开放数据集创建数据集

[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 数据集包括不受任何限制的天气、人口普查、节假日、公共安全和位置数据，有助于定型机器学习模型和扩充预测解决方案。 开放数据集位于 Microsoft Azure 的云中，SDK 和 Studio 中均包含。

了解如何[从 Azure 开放数据集创建 Azure 机器学习数据集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)。 

## <a name="train-with-datasets"></a>使用数据集进行训练

在机器学习试验中使用数据集来训练 ML 模型。 [详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)。

## <a name="version-datasets"></a>对数据集进行版本控制

可以通过创建新版本，以相同的名称注册新数据集。 数据集版本是为数据状态设置书签的一种方法，以便可以应用数据集的特定版本进行试验或者在将来重现该数据集。 详细了解[数据集版本](how-to-version-track-datasets.md)。
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>后续步骤

* 了解[如何使用数据集进行训练](how-to-train-with-datasets.md)。
* 通过自动化机器学习[使用 TabularDataset 进行训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)。
* 有关更多数据集训练示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)。
