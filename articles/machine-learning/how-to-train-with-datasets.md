---
title: 使用机器学习数据集进行训练
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure 机器学习数据集使数据可用于本地或远程计算以训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: yogipandey
author: ynpandey
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 191c76c6ec67112df71d8d5525b2c5938627b3d6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458531"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>使用 Azure 机器学习数据集来训练模型 

本文介绍如何使用 [Azure 机器学习数据集](/python/api/azureml-core/azureml.core.dataset%28class%29)来训练机器学习模型。  可以在本地或远程计算目标中使用数据集，而不必考虑连接字符串或数据路径。 

Azure 机器学习数据集提供了与 Azure 机器学习训练功能（如 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)、[HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) 和 [Azure 机器学习管道](./how-to-create-machine-learning-pipelines.md)）的无缝集成。

如果尚未准备好为模型训练提供数据，但需要将数据加载到笔记本进行数据浏览，请了解如何[浏览数据集中的数据](how-to-create-register-datasets.md#explore-data)。 

## <a name="prerequisites"></a>先决条件

若要创建数据集并使用数据集进行训练，需要具有以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install) (>= 1.13.0)，其中包含 `azureml-datasets` 包。

> [!Note]
> 某些数据集类依赖于 [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) 包。 对于 Linux 用户，只有以下分发版支持这些类：Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>在机器学习训练脚本中使用数据集

如果有尚未注册为数据集的结构化数据，请创建一个 TabularDataset，并在训练脚本中直接使用它进行本地或远程实验。

在此示例中，你创建一个未注册的 [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)，并在 [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) 对象中将其指定为脚本参数，以便进行训练。 如果要在工作区中的其他实验中重用此 TabularDataset，请参见[如何将数据集注册到工作区](how-to-create-register-datasets.md#register-datasets)。

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

下面的代码从 Web URL 创建未注册的 TabularDataset。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 对象提供将 TabularDataset 中的数据加载到 pandas 或 Spark 数据帧中的功能，以便你可以使用熟悉的数据准备和训练库，且无需离开笔记本。

### <a name="access-dataset-in-training-script"></a>在训练脚本中访问数据集

下面的代码将配置脚本参数 `--input-data`，你将在配置训练运行时指定该参数（请参阅下一节）。 在将表格数据集作为参数值传入时，Azure ML 会将其解析为该数据集的 ID，然后你可以在训练脚本中使用该 ID 来访问该数据集（无需在脚本中对该数据集的名称或 ID 进行硬编码）。 然后，该脚本使用 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法将该数据集加载到 pandas 数据帧中，以便在训练之前进一步探索数据并做好准备。

> [!Note]
> 如果原始数据源包含 NaN、空字符串或空值，则当你使用 `to_pandas_dataframe()` 时，这些值会被替换为“Null”值。

如果需要从内存中的 pandas 数据帧将准备好的数据加载到新数据集中，请将这些数据写入到一个本地文件（如 parquet），然后从该文件创建新数据集。 详细了解[如何创建数据集](how-to-create-register-datasets.md)。

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>配置训练运行

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) 对象用于配置和提交训练运行。

此代码创建 ScriptRunConfig 对象 `src`，该对象指定以下内容：

* 脚本的脚本目录。 此目录中的所有文件都上传到群集节点以便执行。
* 训练脚本 *train_titanic.py*。
* 用于训练的输入数据集 `titanic_ds`，作为脚本参数。 在将此数据集传递到脚本时，Azure ML 会将其解析为该数据集的相应 ID。
* 该运行的计算目标。
* 该运行的环境。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>将文件装载到远程计算目标

如果你有非结构化数据，请创建一个 [FileDataset](/python/api/azureml-core/azureml.data.filedataset)，然后装载或下载数据文件，使它们可在训练中用于远程计算目标。 了解何时使用[装载与下载](#mount-vs-download)进行远程训练实验。 

以下示例， 

* 为训练数据创建输入 FileDataset `mnist_ds`。
* 指定在何处写入训练结果，以及将这些结果作为 FileDataset 进行提升。
* 将输入数据集装载到计算目标。

> [!Note]
> 如果使用的是自定义 Docker 基础映像，则需要通过 `apt-get install -y fuse` 安装 fuse，作为让数据集装载正常工作所需的依赖项。 了解如何[生成自定义生成映像](./how-to-deploy-custom-container.md)。

有关笔记本示例，请参阅[如何使用数据输入和输出配置训练运行](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)。

### <a name="create-a-filedataset"></a>创建 FileDataset

以下示例从 Web URL 创建未注册的 FileDataset `mnist_data`。 此 FileDataset 是训练运行的输入数据。

从其他来源详细了解[如何创建数据集](how-to-create-register-datasets.md)。

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>在何处写入训练输出

可以使用 [OutputFileDatasetConfig 对象](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)指定在何处写入训练结果。 

OutputFileDatasetConfig 对象使你可以： 

* 将运行的输出装载或上传到指定的云存储。
* 以 FileDataset 的形式将输出保存到支持的存储类型：
    * Azure blob
    * Azure 文件共享
    * Azure Data Lake Storage 第 1 代和第 2 代
* 跟踪训练运行之间的数据世系。

下面的代码指定训练结果应以 FileDataset 的形式保存在默认 blob 数据存储 `def_blob_store` 的 `outputdataset` 文件夹中。 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>配置训练运行

建议在装载时通过 `ScriptRunConfig` 构造函数的 `arguments` 参数将该数据集作为参数传递。 这样，你就可以通过参数在训练脚本中获取数据路径（装入点）。 这样，便能够在任何云平台上使用相同的训练脚本进行本地调试和远程训练。

下面的示例创建一个 ScriptRunConfig，它通过 `arguments` 传入 FileDataset。 提交运行后，由数据集 `mnist_ds` 引用的数据文件会装载到计算目标，而训练结果会保存到默认数据存储中的指定 `outputdataset` 文件夹。

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>简单训练脚本

下面的脚本通过 ScriptRunConfig 进行提交。 它将 `mnist_ds ` 数据集读取为输入，并将该文件写入默认 blob 数据存储 `def_blob_store` 中的 `outputdataset` 文件夹。

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>装载和下载

对于从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 创建的数据集，可以装载或下载任何格式的文件。 

装载数据集时，请将数据集引用的文件附加到目录（装入点），并使其在计算目标上可用。 基于 Linux 的计算支持装载，这些计算包括 Azure 机器学习计算、虚拟机和 HDInsight。 如果数据大小超出计算磁盘大小，则无法下载。 对于此方案，我们建议装载，因为在处理时只会加载脚本使用的数据文件。

下载数据集时，数据集引用的所有文件都将下载到计算目标。 所有计算类型都支持下载。 如果脚本处理数据集引用的所有文件，并且计算磁盘可以容纳整个数据集，则建议下载，以避免从存储服务流式传输数据的开销。 有关多节点下载，请参阅[如何避免限制](#troubleshooting)。 

> [!NOTE]
> 对于 Windows 操作系统，下载路径名称不应超过 255 个字母数字字符。 对于 Linux 操作系统，下载路径名称不应超过 4,096 个字母数字字符。 此外，对于 Linux 操作系统，文件名（是指下载路径 `/path/to/file/{filename}` 中的最后一段）不应超过 255 个字母数字字符。

以下代码将 `dataset` 装载到 `mounted_path` 的临时目录

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>在机器学习脚本中获取数据集

可以在本地以及在 Azure 机器学习计算等计算群集上远程访问已注册的数据集。 若要跨试验访问已注册的数据集，请使用以下代码来访问工作区并获取在以前提交的运行中使用过的数据集。 默认情况下，`Dataset` 类中的 [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) 方法返回已注册到工作区的数据集的最新版本。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>在训练期间访问源代码

Azure Blob 存储具有比 Azure 文件共享更快的吞吐速度，并将扩展到大量并行启动的作业。 出于此原因，我们建议配置运行以使用 Blob 存储来传输源代码文件。

下面的代码示例在运行配置中指定用于源代码传输的 Blob 数据存储。

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook 示例

+ 有关其他数据集示例和概念，请参阅[数据集笔记本](https://aka.ms/dataset-tutorial)。
+ 请参阅[如何在 ML 管道中参数化数据集](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)。

## <a name="troubleshooting"></a>疑难解答

**数据集初始化失败：“等待装入点准备完毕”已超时**： 
  * 如果你没有任何出站[网络安全组](../virtual-network/network-security-groups-overview.md)规则并且正在使用 `azureml-sdk>=1.12.0`，请将 `azureml-dataset-runtime` 及其依赖项更新为特定次要版本的最新版本，而如果你正在运行中使用该版本，请重新创建环境，以便获得具有修补程序的最新补丁。 
  * 如果使用的是 `azureml-sdk<1.12.0`，请升级到最新版本。
  * 如果具有出站 NSG 规则，请确保存在允许服务标记 `AzureResourceMonitor` 的所有流量的出站规则。

**数据集初始化失败: ThrottlingException 引起 StreamAccessException**

对于多节点文件下载，所有节点可能会尝试从 Azure 存储服务下载文件数据集中的所有文件，这会导致限制错误。 若要避免限制，请将环境变量 `AZUREML_DOWNLOAD_CONCURRENCY` 初始设置为 8 倍 CPU 核心数除以节点数的值。 设置此环境变量的值可能需要一些试验，因此上述指导是起点。

以下示例假定有 32 个核心和 4 个节点。

```python
from azureml.core.environment import Environment 
myenv = Environment(name="myenv")
myenv.environment_variables = {"AZUREML_DOWNLOAD_CONCURRENCY":64}
```

### <a name="azurefile-storage"></a>AzureFile 存储

无法将项目文件上传到 AzureFile 中的工作目录，因为该存储已过载：

* 如果对其他工作负荷（例如数据传输）使用文件共享，则我们建议使用 Blob，以便可以自由使用文件共享来提交运行。

* 另一种选择是在两个不同的工作区之间拆分工作负载。

ConfigException：由于缺少凭据，无法建立到 AzureFileService 的连接。需要将帐户密钥或 SAS 令牌链接到默认工作区 Blob 存储。

若要确保存储访问凭据链接到工作区以及关联的文件数据存储，请完成以下步骤：

1. 在 [Azure 门户](https://ms.portal.azure.com)中导航到工作区。
1. 在工作区“概述”页上选择存储链接。
1. 在存储页上，选择左侧菜单中的“访问密钥”。 
1. 复制密钥。
1. 导航到工作区的 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在工作室中，选择要为其提供身份验证凭据的文件数据存储。 
1. 选择“更新身份验证”。
1. 粘贴前面步骤中的密钥。 
1. 选择“保存”。  

### <a name="passing-data-as-input"></a>作为输入传递数据

**TypeError：FileNotFound:无此类文件或目录：** 如果文件不在提供的文件路径中，则会出现此错误。 需确保引用文件的方式与在计算目标上将数据集装载到的位置相一致。 为确保确定性状态，我们建议在将数据集装载到计算目标时使用抽象路径。 例如，在以下代码中，我们将数据集装载到计算目标文件系统的根目录 `/tmp` 下。 
    
```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

如果不包含前导正斜杠“/”，则需要为计算目标上的工作目录添加前缀（例如 `/mnt/batch/.../tmp/dataset`），以指示要将数据集装载到的位置。


## <a name="next-steps"></a>后续步骤

* 使用 TabularDataset [自动训练机器学习模型](how-to-configure-auto-train.md#data-source-and-format)。

* 使用 FileDataset [训练图像分类模型](https://aka.ms/filedataset-samplenotebook)。

* [通过管道使用数据集进行训练](./how-to-create-machine-learning-pipelines.md)。