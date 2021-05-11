---
title: 教程：使用自己的数据
titleSuffix: Azure Machine Learning
description: Azure 机器学习入门系列的第 4 部分介绍了如何在远程训练运行中使用你自己的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python, contperf-fy21q3
ms.openlocfilehash: e664b08f7ca487236e5e2780d183c19d342a915b
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888019"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>教程：使用你自己的数据（第 4 部分，共 4 部分）

本教程介绍了如何在 Azure 机器学习中上传并使用你自己的数据来训练机器学习模型。

本教程是由四部分组成的系列教程的第 4 部分，你可以在其中了解 Azure 机器学习基础知识，并在 Azure 中完成基于作业的机器学习任务。 本教程以你在[第 1 部分：设置](tutorial-1st-experiment-sdk-setup-local.md)、[第 2 部分：运行“Hello World!”](tutorial-1st-experiment-hello-world.md)和[第 3 部分：训练模型](tutorial-1st-experiment-sdk-train.md)中完成的工作为基础编写。

在[第 3 部分：训练模型](tutorial-1st-experiment-sdk-train.md)中，已通过 PyTorch API 中内置的 `torchvision.datasets.CIFAR10` 方法下载了数据。 但在许多情况下，你将会需要在远程训练运行中使用自己的数据。 本文介绍了可用于在 Azure 机器学习中使用你自己的数据的工作流。

本教程介绍以下操作：

> [!div class="checklist"]
> * 将训练脚本配置为使用本地目录中的数据。
> * 在本地测试训练脚本。
> * 将数据上传到 Azure。
> * 创建控制脚本。
> * 了解新的 Azure 机器学习概念（传递参数、数据集、数据存储）。
> * 提交并运行训练脚本。
> * 在云中查看代码输出。

## <a name="prerequisites"></a>先决条件

需要数据和在上一教程中创建的 pytorch 环境的更新版本。  请确保已完成以下步骤：

1. [创建训练脚本](tutorial-1st-experiment-sdk-train.md#create-training-scripts)
1. [创建新的 Python 环境](tutorial-1st-experiment-sdk-train.md#environment)
1. [本地测试](tutorial-1st-experiment-sdk-train.md#test-local)
1. [更新 Conda 环境文件](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)

## <a name="adjust-the-training-script"></a>调整训练脚本

现在，你的训练脚本 (tutorial/src/train.py) 已在 Azure 机器学习中运行，并且你可以监视模型性能。 让我们通过引入参数来将训练脚本参数化。 使用参数可轻松比较不同的超参数。

我们的训练脚本现在设置为在每次运行时下载 CIFAR10 数据集。 以下 Python 代码已调整为从某个目录中读取数据。

>[!NOTE] 
> 使用 `argparse` 将脚本参数化。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>了解代码更改

`train.py` 中的代码已使用 `argparse` 库来设置 `data_path`、`learning_rate` 和 `momentum`。

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

此外，`train.py` 脚本已经过改编，以将优化器更新为使用用户定义的参数：

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [我调整了训练脚本](?success=adjust-training-script#test-locally) [我遇到了一个问题](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> 在本地测试脚本

你的脚本现在接受数据路径作为参数。 若要开始，请在本地对其进行测试。 向教程目录结构添加一个名为 `data` 的文件夹。 目录结构应类似于：

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="目录结构中显示了 .azureml、data 和 src 子目录":::

1. 退出当前环境。

    ```bash
    conda deactivate

1. Now create and activate the new environment.  This will rebuild the pytorch-aml-env with the [updated environment file](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)


    ```bash
    conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
    ```

    ```bash
    conda activate pytorch-aml-env          # activate new conda environment
    ```

1. 最后，在本地运行已修改的训练脚本。

    ```bash
    python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
    ```

可以通过传入数据的本地路径来避免下载 CIFAR10 数据集。 对于“学习速率”和“动量”超参数，你也可以使用不同的值进行试验，而无需在训练脚本中对这些超参数进行硬编码 。

> [!div class="nextstepaction"]
> [我在本地测试脚本](?success=test-locally#upload) [我遇到了一个问题](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> 将数据上传到 Azure

若要在 Azure 机器学习中运行此脚本，需要使训练数据在 Azure 中可用。 Azure 机器学习工作区附带默认的数据存储。 这是一个 Azure Blob 存储帐户，你可以在其中存储训练数据。

>[!NOTE] 
> Azure 机器学习允许连接其他基于云的数据存储，用来存储你的数据。 有关详细信息，请参阅[数据存储文档](./concept-data.md)。  

在 `tutorial` 目录中，创建一个名为 `05-upload-data.py` 的新 Python 控制脚本：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

`target_path` 值指定 CIFAR10 数据将要上传到的数据存储的路径。

>[!TIP] 
> 在使用 Azure 机器学习上传数据时，可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)来上传临时文件。 如果需要 ETL 工具，可以使用 [Azure 数据工厂](../data-factory/introduction.md)将数据引入 Azure。

在已激活 tutorial1 conda 环境的窗口中，运行 Python 文件以上传数据。 （上传速度应该会很快，时间应短于 60 秒。）

```bash
python 05-upload-data.py
```

应该会看到以下标准输出：

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [我上传了数据](?success=upload-data#control-script) [我遇到了一个问题](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> 创建控制脚本

像之前的操作一样，新建一个名为“`06-run-pytorch-data.py`”的 Python 控制脚本：

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>了解代码更改

该控制脚本类似于[此系列第 3 部分](tutorial-1st-experiment-sdk-train.md)中的控制脚本，包含以下新行：

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [数据集](/python/api/azureml-core/azureml.core.dataset.dataset)用于引用上传到 Azure Blob 存储的数据。 数据集是基于数据的抽象层，旨在提高可靠性和可信任性。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 已修改，包含将传递到 `train.py` 中的参数列表。 `dataset.as_named_input('input').as_mount()` 参数表示指定的目录将会被装载到计算目标。
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [我创建了控制脚本](?success=control-script#submit-to-cloud) [我遇到了一个问题](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> 将该运行提交到 Azure 机器学习

现在，请重新提交该运行以使用新配置：

```bash
python 06-run-pytorch-data.py
```

此代码将会在 Azure 机器学习工作室中输出一个指向试验的 URL。 如果访问该链接，就可以看到代码在运行。

> [!div class="nextstepaction"]
> [我重新提交了运行](?success=submit-to-cloud#inspect-log) [我遇到了一个问题](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> 检查日志文件

在工作室中，转到试验运行（通过选择前面的 URL 输出），然后选择“输出 + 日志”。 选择 `70_driver_log.txt` 文件。 向下滚动日志文件，直到看到以下输出：

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

注意：

- Azure 机器学习已自动为你将 Blob 存储装载到计算群集。
- 控制脚本中使用的 ``dataset.as_named_input('input').as_mount()`` 将解析为装入点。

> [!div class="nextstepaction"]
> [我检查了日志文件](?success=inspect-log#clean-up-resources) [我遇到了一个问题](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们了解了如何通过使用 `Datastore` 将数据上传到 Azure。 数据存储充当工作区的云存储，为你提供了一个持久且灵活的数据保存位置。

你已了解如何修改训练脚本，以通过命令行接受数据路径。 通过使用 `Dataset`，可以将目录装载到远程运行。 

有了模型后，接下来请学习：

* 如何[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。
