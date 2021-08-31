---
title: 什么是 Azure 机器学习计算实例？
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习计算实例 - 完全托管式基于云的工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2021
ms.openlocfilehash: bba8329075ecb47d367fc04afa1f2df0b4fcf721
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742099"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什么是 Azure 机器学习计算实例？

Azure 机器学习计算实例是面向数据科学家的基于云的托管式工作站。

计算实例可让客户轻松地开始进行 Azure 机器学习开发，并为 IT 管理员提供管理和企业就绪功能。

可以使用计算实例作为在云中进行机器学习的完全配置和托管的开发环境。 还可以在开发和测试中将它们用作训练和推理的计算目标。

对于生产级模型训练，请使用具有多节点缩放功能的 [Azure 机器学习计算群集](how-to-create-attach-compute-cluster.md)。 对于生产级模型部署，请使用 [Azure Kubernetes 服务群集](how-to-deploy-azure-kubernetes-service.md)。

为了让计算实例 Jupyter 功能可以正常运行，请确保没有禁用 Web 套接字通信。 请确保网络允许到 *.instances.azureml.net 和 *.instances.azureml.ms 的 websocket 连接。

> [!IMPORTANT]
> 本文中标记了“（预览版）”的项目目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="why-use-a-compute-instance"></a>为何使用计算实例？

计算实例是一个完全托管的基于云的工作站，已针对机器学习开发环境进行优化。 它提供以下优势：

|主要优点|描述|
|----|----|
|工作效率|可以在 Azure 机器学习工作室中使用集成的笔记本及以下工具来构建和部署模型：<br/>-  Jupyter<br/>-  JupyterLab<br/>- VS Code（预览版）<br/>-  RStudio（预览版）<br/>计算实例与 Azure 机器学习工作区和工作室完全集成。 你可以与工作区中的其他数据科学家共享笔记本和数据。<br/> 你还可以在计算实例中使用 [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630)。
|无需自行管理且安全|减少安全保护工作，增强企业的安全要求合规性。 计算实例提供可靠的管理策略和安全网络配置，例如：<br/><br/>- 通过资源管理器模板或 Azure 机器学习 SDK 自动预配<br/>- [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [虚拟网络支持](./how-to-secure-training-vnet.md#compute-instance)<br/>- 用于启用/禁用 SSH 访问的 SSH 策略<br/>已启用 TLS 1.2 |
|已针对 ML 进行了预配置|使用预配置的最新 ML 包、深度学习框架和 GPU 驱动程序完成设置任务，可节省时间。|
|完全可自定义|支持多种 Azure VM 类型，包括 GPU 和持久性低级自定义，例如，安装相应的包和驱动程序可以轻而易举地实现高级方案。 |

* 计算实例也是类似于计算群集的安全训练计算目标，但它是单节点。
* 你可以自行[创建计算实例](how-to-create-manage-compute-instance.md?tabs=python#create)，也可以由管理员[代表你创建计算实例](how-to-create-manage-compute-instance.md?tabs=python#on-behalf)。
* 你还可以[使用设置脚本（预览版）](how-to-create-manage-compute-instance.md#setup-script)，根据需要以自动化方式自定义并配置计算实例。
* 若要节省成本，[创建计划](how-to-create-manage-compute-instance.md#schedule)以自动启动和停止计算实例（预览版）。

## <a name="tools-and-environments"></a><a name="contents"></a>工具和环境

使用 Azure 机器学习计算实例可以在工作区中的完全集成式笔记本体验中创作、训练和部署模型。

使用计算实例作为远程服务器，无需 SSH 即可在 [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) 中运行 Jupyter 笔记本。 也可以通过[远程 SSH 扩展](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/)启用 VS Code 集成。

可以[安装包](how-to-access-terminal.md#install-packages)，然后在计算实例中[添加内核](how-to-access-terminal.md#add-new-kernels)。

计算实例上已安装以下工具和环境：

|常规工具和环境|详细信息|
|----|:----:|
|驱动程序|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 库||
|Azure CLI ||
|Azure 机器学习示例 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf||

|**R** 工具和环境|详细信息|
|----|:----:|
|RStudio Server 开源版（预览版）||
|R 内核||

|**PYTHON** 工具和环境|详细信息|
|----|----|
|Anaconda Python||
|Jupyter 和扩展||
|Jupyterlab 和扩展||
[适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)</br>（来自 PyPI）|包括大多数 azureml 额外包。  若要查看完整列表，请[打开计算实例上的终端窗口](how-to-access-terminal.md)并运行 <br/> `conda list -n azureml_py36 azureml*` |
|其他 PyPI 包|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 包|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度学习包|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 包|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure 机器学习 Python 示例||

Python 包都安装在“Python 3.8 - AzureML”环境中。 计算实例使用 Ubuntu 18.04 作为基础 OS。

## <a name="accessing-files"></a>访问文件

笔记本和 R 脚本存储在 Azure 文件共享中工作区的默认存储帐户内。  这些文件位于“用户文件”目录下。 通过此存储可以轻松地在计算实例之间共享笔记本。 停止或删除计算实例时，存储帐户还会安全保存笔记本。

工作区的 Azure 文件共享帐户作为驱动器装载到计算实例上。 此驱动器是 Jupyter、Jupyter Labs 和 RStudio 的默认工作目录。 这意味着，在 Jupyter、JupyterLab 或 RStudio 中创建的笔记本和其他文件会自动存储在文件共享上，并可在其他计算实例中使用。

可以从同一工作区中的所有计算实例访问文件共享中的文件。 对计算实例上的这些文件所做的任何更改将可靠地保存回到文件共享。

还可以将最新 Azure 机器学习示例克隆到工作区文件共享中“用户文件”目录下的文件夹内。

与写入到计算实例本地磁盘本身相比，在网络驱动器上写入小文件可能速度更慢。  若要写入许多小文件，请尝试直接在计算实例上使用某个目录，例如 `/tmp` 目录。 请注意，无法从其他计算实例访问这些文件。

请勿在笔记本文件共享上存储训练数据。 你可以使用计算实例上的 `/tmp` 目录来保存临时数据。  但是，请勿在计算实例的 OS 磁盘上写入非常大的数据文件。 计算实例上的 OS 磁盘容量为 128 GB。 也可以在 /mnt 上装载的临时磁盘上存储临时训练数据。 临时磁盘大小可以根据所选 VM 大小来配置。如果选择了较大的 VM，则可以存储更大量的数据。 还可以装载[数据存储和数据集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)。 你安装的所有软件包将保存在计算实例的 OS 磁盘上。 请注意，OS 磁盘当前不支持客户管理的密钥加密。 计算实例的 OS 磁盘使用 Microsoft 管理的密钥进行加密。 

### <a name="create-a-compute-instance"></a><a name="create"></a>创建计算实例

作为管理员，你可以[为工作区中的其他人创建计算实例（预览版）](how-to-create-manage-compute-instance.md#on-behalf)。

还可以[使用设置脚本（预览版）](how-to-create-manage-compute-instance.md#setup-script)，以自动化方式自定义并配置计算实例。

若要为自己创建计算实例，请使用 Azure 机器学习工作室中你的工作区，在准备好运行某个笔记本时，从“计算”部分或在“笔记本”部分[新建计算实例](how-to-create-manage-compute-instance.md?tabs=azure-studio#create) 。

也可以通过以下方式创建实例
* 直接从[集成式笔记本体验](tutorial-train-models-with-aml.md#azure)
* 在 Azure 门户中配置
* 通过 Azure 资源管理器模板。 有关示例模板，请参阅[创建 Azure 机器学习计算实例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)。
* 使用 [Azure 机器学习 SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* 从 [Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md#computeinstance)

对于每个区域每个虚拟机 (VM) 系列配额和创建计算实例时应用的区域总配额，专用内核数一致，且该数量与 Azure 机器学习训练计算群集配额共享。 停止计算实例不会释放配额，因此无法确保你能够重启计算实例。 请不要以执行 sudo 关闭的方式通过 OS 终端停止计算实例。

计算实例附带 P10 OS 磁盘。 临时磁盘类型取决于所选的 VM 大小。 目前无法更改 OS 磁盘类型。


## <a name="compute-target"></a>计算目标

计算实例可用作类似于 Azure 机器学习计算训练群集的[训练计算目标](concept-compute-target.md#train)。

计算实例：
* 具有作业队列。
* 在虚拟网络环境中安全地运行作业，无需企业打开 SSH 端口。 作业在容器化环境中执行，并将模型依赖项打包到 Docker 容器中。
* 可以并行运行多个小型作业（预览版）。  每个核心可以并行运行两个作业，而剩余的作业将排队。
* 支持单节点多 GPU 分布式训练作业

可以使用计算实例作为测试/调试方案的本地推理部署目标。

> [!TIP]
> 计算实例具有 120GB 的 OS 磁盘。 如果磁盘空间不足并且进入不可用状态，请采用删除文件/文件夹的方式通过计算实例终端在 OS 磁盘（在 / 上装载的）上清除至少 5 GB 磁盘空间，然后执行 `sudo reboot`。 若要访问该终端，请转到计算列表页或计算实例详细信息页，然后单击“终端”链接。 你可通过在终端上运行 `df -h` 来检查可用的磁盘空间。 在执行 `sudo reboot` 之前，请清除至少 5 GB 空间。 在清除完 5 GB 磁盘空间之前，请不要通过工作室停止或重启计算实例。

## <a name="next-steps"></a>后续步骤

* [创建和管理计算实例](how-to-create-manage-compute-instance.md)
* [教程：训练自己的首个 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何将计算实例与集成的笔记本配合使用。
