---
title: 教程：训练示例 Jupyter Notebook
titleSuffix: Azure Machine Learning
description: 通过 Azure 机器学习在基于云的 Python Jupyter Notebook 中使用 scikit-learn 训练图像分类模型。 本教程是第一部分（共两部分）。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 04/26/2021
ms.custom: seodec18, devx-track-python, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 9f8a3fc284fc04b6e9808ca90eb05ba106fa55d3
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428558"
---
# <a name="tutorial-train-an-image-classification-model-with-an-example-jupyter-notebook"></a>教程：使用示例 Jupyter Notebook 训练图像分类模型 

在本教程中，你将在远程计算资源上训练一个机器学习模型。 你将在 Python Jupyter Notebook 中使用 Azure 机器学习的训练和部署工作流。  然后可以将 Notebook 用作模板，使用你自己的数据来定型机器学习。 本教程是由两个部分构成的系列教程的第一部分。  

本教程将 [MNIST](http://yann.lecun.com/exdb/mnist/) 数据集和 [scikit-learn](https://scikit-learn.org) 与 Azure 机器学习配合使用来训练简单的逻辑回归。 MNIST 是包含 70,000 张灰度图像的常用数据集。 每个图像是 28 x 28 像素的手写数字，代表一个从零到九的数字。 目标是创建多类分类器，以确定给定图像代表的数字。

了解如何执行以下操作：

> [!div class="checklist"]
> * 设置开发环境。
> * 访问和检查数据。
> * 在远程群集上训练一个简单的逻辑回归模型。
> * 查看定型结果，然后注册最佳模型。

你会在[本教程的第二部分](tutorial-deploy-models-with-aml.md)学习如何选择模型并对其进行部署。

如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

>[!NOTE]
> 本文中的代码已使用 [Azure 机器学习 SDK](/python/api/overview/azure/ml/intro) 版本 1.13.0 进行测试。

## <a name="prerequisites"></a>先决条件

* 完成[快速入门：Azure 机器学习入门](quickstart-create-resources.md)以执行以下操作：
    * 创建工作区。
    * 创建用于开发环境的基于云的计算实例。
    * 创建用于训练模型的基于云的计算群集。

## <a name="run-a-notebook-from-your-workspace"></a><a name="azure"></a>在工作区中运行笔记本

Azure 机器学习在你的工作区中提供了一个云笔记本服务器，实现了免安装的预配置体验。 如果你希望控制环境、包和依赖项，请使用[自己的环境](how-to-configure-environment.md#local)。

 按照此视频的说明或使用详细步骤从工作区克隆和运行教程笔记本。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a>克隆笔记本文件夹

在 Azure 机器学习工作室中完成以下试验设置并运行步骤。 此合并接口包括机器学习工具，所有技能级别的数据科学专业人员均可利用这些工具实现数据科学方案。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。

1. 选择创建的订阅和工作区。

1. 在左侧选择“笔记本”。

1. 在顶部选择“示例”选项卡。

1. 打开“Python”文件夹。

1. 打开包含版本号的文件夹。 此数字表示 Python SDK 的当前版本。

1. 选择“教程”文件夹右侧的“…”按钮，然后选择“克隆”  。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="显示“克隆”教程文件夹的屏幕截图。":::

1. 文件夹列表显示访问该工作区的每个用户。 选择要将“tutorials”文件夹克隆到其中的文件夹。

### <a name="open-the-cloned-notebook"></a><a name="open"></a>打开克隆的笔记本

1. 打开已克隆到“用户文件”部分的“tutorials”文件夹 。

    > [!IMPORTANT]
    > 可以查看“示例”文件夹中的笔记本，但无法从此文件夹运行笔记本。 若要运行笔记本，请确保在“用户文件”部分打开笔记本的克隆版本。
    
1. 选择 tutorials/image-classification-mnist-data 文件夹中的 img-classification-part1-training.ipynb 文件。 

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="显示“打开”教程文件夹的屏幕截图。":::

1. 在顶部栏上，选择用于运行笔记本的计算实例。


如果希望在自己的 [本地环境](how-to-configure-environment.md#local)中使用此教程及其附带的 **utils.py** 文件，也可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上找到它。 如果不使用计算实例，请运行 `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` 安装本教程的依赖项。 

> [!Important]
> 本文的其余部分包含的内容与在笔记本中看到的内容相同。  
>
> 如果要在运行代码时继续阅读，请立即切换到 Jupyter Notebook。 
> 若要在笔记本中运行单个代码单元，请单击代码单元，然后按 **Shift+Enter**。 或者，通过从顶部工具栏中选择“全部运行”来运行整个笔记本。

## <a name="set-up-your-development-environment"></a><a name="start"></a>设置开发环境

开发工作的所有设置都可以在 Python Notebook 中完成。 安装包括以下操作：

* 导入 Python 包。
* 连接到工作区，以便本地计算机能够与远程资源通信。
* 创建试验来跟踪所有运行。
* 创建要用于定型的远程计算目标。

### <a name="import-packages"></a>导入程序包

导入在此会话中所需的 Python 包。 此外显示 Azure 机器学习 SDK 版本：

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>连接到工作区

从现有工作区创建工作区对象。 `Workspace.from_config()` 读取文件 config.json 并将详细信息加载到一个名为 `ws` 的对象。  计算实例将此文件的副本保存在其根目录中。  如果在其他位置运行代码，则需要[创建文件](how-to-configure-environment.md#workspace)。

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> 首次运行以下代码时，系统可能会要求你向工作区进行身份验证。 按照屏幕上的说明进行操作。

### <a name="create-an-experiment"></a>创建试验

创建一个试验来跟踪工作区中的运行。 一个工作区可有多个试验：

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>创建或附加现有的计算目标

Azure 机器学习计算是一项托管服务，可让数据科学家在 Azure 虚拟机群集上训练机器学习模型。 示例包括带 GPU 支持的 VM。 在本教程中，你将创建 Azure 机器学习计算作为训练环境。 在本教程的后面部分，你将提交要在此 VM 上运行的 Python 代码。 

如果工作区中尚无计算群集，以下代码将创建计算群集。 它会设置一个群集，该群集在不使用时将缩减至 0，并且最多可以扩展至 4 个节点。

 **创建计算目标需要大约 5 分钟。** 如果计算资源已经在工作区中，则代码将使用它并跳过创建过程。  

> [!TIP]
> 如果在快速入门中创建了计算群集，请确保以下代码中的 `compute_name` 使用相同的名称。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

现在已有所需的包和计算资源，可以在云中定型模型。 

## <a name="explore-data"></a>浏览数据

对模型进行定型之前，需要了解用于定型的数据。 本部分介绍以下操作：

* 下载 MNIST 数据集。
* 显示一些示例图像。

### <a name="download-the-mnist-dataset"></a>下载 MNIST 数据集

使用 Azure 开放数据集获取原始 MNIST 数据文件。 [Azure 开放数据集](../open-datasets/overview-what-are-open-datasets.md)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 每个数据集都有相应的类（此例中为 `MNIST`），以便以不同的方式检索数据。

此代码将数据检索为 `FileDataset` 对象，该对象是 `Dataset` 的子类。 `FileDataset` 引用数据存储或公共 URL 中的任何格式的单个或多个文件。 该类可让你通过创建对数据源位置的引用来将文件下载或装载到计算。 此外，你将数据集注册到你的工作区，以便在训练期间轻松检索。

按照[操作方法](how-to-create-register-datasets.md)了解有关数据集及其在 SDK 中的用法的详细信息。

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>显示一些示例图像

将压缩文件加载到 `numpy` 数组。 然后，使用 `matplotlib` 从数据集随意绘制 30 张图像，并在上方附加标签。 此步骤需要 `utils.py` 文件中包含的 `load_data` 函数。 此文件包含在示例文件夹中。 确保它与此 Notebook 放在同一文件夹中。 `load_data` 函数直接将压缩文件解析为 numpy 数组。

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

随机图像示例显示：

![随机图像示例](./media/tutorial-train-models-with-aml/digits.png)

现在你已了解这些图像的外观和预期预测结果。

## <a name="train-on-a-remote-cluster"></a>在远程群集上定型

在此任务中，你将提交作业以在前面设置的远程训练群集上运行。  若要提交作业：
* 创建目录
* 创建定型脚本
* 创建脚本运行配置
* 提交作业

### <a name="create-a-directory"></a>创建目录

创建一个目录，将所需的代码从计算机发送到远程资源。

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>创建定型脚本

若要将作业提交到群集，首先创建定型脚本。 运行以下代码，以在刚创建的目录中创建名为 `train.py` 的定型脚本。

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

请注意该脚本获取数据和保存模型的方式：

- 定型脚本读取参数以查找包含数据的目录。 稍后提交作业时，请参考数据存储获取此参数：

  `parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')`

- 训练脚本将模型保存到一个名为 **outputs** 的目录中。 此目录中编写的所有内容都会自动上传到你的工作区。 稍后，你将在本教程从此目录访问模型。 `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

- 训练脚本需要使用文件 `utils.py` 来正确加载数据集。 以下代码将 `utils.py` 复制到 `script_folder` 中，以便可以与远程资源上的训练脚本一起访问该文件。

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>配置训练作业

创建一个 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 对象，以指定训练作业的配置详细信息，包括训练脚本、要使用的环境，以及要在其上运行的计算目标。 通过指定以下内容配置 ScriptRunConfig：

* 包含脚本的目录。 此目录中的所有文件都上传到群集节点以便执行。
* 计算目标。 在此示例中，将使用所创建的 Azure 机器学习计算群集。
* 定型脚本名称，train.py。
* 一个环境，其中包含运行脚本所需的库。
* 训练脚本中的必需参数。

在本教程中，此目标是 AmlCompute。 此脚本文件夹中的所有文件都上传到群集节点来运行。 --data_folder 设置为使用数据集。

首先，创建包含以下内容的环境：scikit-learn 库、azureml-dataset-runtime（访问数据集时需要）和 azureml-defaults（包含用于记录指标的依赖项）。 azureml-defaults 还包含稍后在本教程的第 2 部分中将模型部署为 Web 服务时所需的依赖项。

定义环境后，将其注册到工作区，以便在本教程的第 2 部分中重复使用它。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

然后，通过指定训练脚本、计算目标和环境来创建 ScriptRunConfig。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>将作业提交到群集

通过提交 ScriptRunConfig 对象来运行此试验：

```python
run = exp.submit(config=src)
run
```

由于调用是异步的，因此一旦作业启动，它就会返回“正在准备”或“正在运行”状态 。

## <a name="monitor-a-remote-run"></a>监视远程运行

总的来说，首次运行需要大约 10 分钟。 但对于后续运行，只要不更改脚本依赖项，将重复使用相同的映像。 因此容器启动时间要快得多。

等待时会发生以下情况：

- **映像创建**：将创建与 Azure ML 环境指定的 Python 环境匹配的 Docker 映像。 映像将上传到工作区。 创建和上传映像需要大约五分钟。

  此阶段针对每个 Python 环境发生一次，因为已缓存容器用于后续运行。 映像创建期间，日志将流式传输到运行历史记录。 可以使用这些日志监视映像创建进度。

- **缩放**：如果远程群集需要比当前可用节点更多的节点来执行运行，则会自动添加其他节点。 缩放通常需要大约五分钟。

- **正在运行**：在此阶段，必要的脚本和文件会发送到计算目标。 接着，装载或复制数据存储。 然后，运行 entry_script。 运行作业时，stdout 和 ./logs 目录会流式传输到运行历史记录 。 可以使用这些日志监视运行进度。

- **后期处理**：运行的 ./outputs 目录将复制到工作区中的运行历史记录，以便可以访问这些结果。

可以通过多种方式查看正在运行的作业的进度。 本教程使用 Jupyter 小组件和 `wait_for_completion` 方法。

### <a name="jupyter-widget"></a>Jupyter 小组件

使用 [Jupyter 小组件](/python/api/azureml-widgets/azureml.widgets)查看运行进度。 和运行提交一样，该小组件采用异步方式，并每隔 10 到 15 秒提供实时更新，直到完成作业：

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

训练结束时，该小组件将如下所示：

![Notebook 小组件](./media/tutorial-train-models-with-aml/widget.png)

如果需要取消运行，可以遵照[这些说明](./how-to-track-monitor-analyze-runs.md)。

### <a name="get-log-results-upon-completion"></a>完成时获取日志结果

模型定型和监视在后台发生。 在运行更多代码之前，请耐心等待，直到该模型完成定型。 使用 `wait_for_completion` 显示模型定型过程何时完成：

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>显示运行结果

现在你拥有一个在远程群集上定型的模型。 检索模型的准确性：

```python
print(run.get_metrics())
```

输出显示远程模型的准确度为 0.9204：

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

下一教程将详细介绍此模型。

## <a name="register-model"></a>注册模型

定型脚本的最后一步在运行作业的群集 VM 内名为 `outputs` 的目录中编写文件 `outputs/sklearn_mnist_model.pkl`。 `outputs` 是一个专门目录，此目录中的所有内容都会自动都上传到工作区。 此内容在工作区下试验的运行记录中显示。 因此，模型文件现还在工作区中提供。

可以看到与该运行关联的文件：

```python
print(run.get_file_names())
```

在工作区中注册模型，以便稍后你或其他协作者可以查询、检查和部署该模型：

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

此外可以只删除 Azure 机器学习计算群集。 但是，自动缩放已打开，并且群集最小值为零。 因此，未使用此特定资源时，便不会产生额外计算费用：

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>后续步骤

在本 Azure 机器学习教程中，已使用 Python 执行以下任务：

> [!div class="checklist"]
> * 设置开发环境。
> * 访问和检查数据。
> * 使用流行的 scikit-learn 机器学习库训练远程群集上的多个模型
> * 查看定型详细信息，然后注册最佳模型。

可以使用本系列教程的下一部分中的说明来部署此注册模型：

> [!div class="nextstepaction"]
> [教程 2 - 部署模型](tutorial-deploy-models-with-aml.md)
