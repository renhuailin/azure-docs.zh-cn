---
title: 对自动化 ML 试验进行故障排除
titleSuffix: Azure Machine Learning
description: 了解如何在自动化机器学习试验中排除故障和解决问题。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.date: 03/08/2021
ms.topic: troubleshooting
ms.custom: devx-track-python, automl, references_regions
ms.openlocfilehash: 3634cce207462695fdb7fadfecfb2f3f520d621f
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428096"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>对 Python 中的自动化 ML 试验进行故障排除

在本指南中，了解如何使用 [Azure 机器学习 SDK](/python/api/overview/azure/ml/intro) 识别和解决自动化机器学习试验中的已知问题。

## <a name="version-dependencies"></a>版本依赖项

`AutoML` 与更新的包版本的依赖项中断了兼容性。 SDK 1.13.0 版本之后，由于以前的 `AutoML` 包中固定的旧版本与今天固定的较新版本之间不兼容，因此模型不会加载到旧的 SDK 中。

可能出现以下错误：

* 找不到模块错误，例如，

  `No module named 'sklearn.decomposition._truncated_svd`

* 导入错误，例如，

  `ImportError: cannot import name 'RollingOriginValidator'`,
* 属性错误，例如，

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

解决方法取决于 `AutoML` SDK 训练版本：

* 如果 `AutoML` SDK 训练版本高于 1.13.0，则需要 `pandas == 0.25.1` 和 `scikit-learn==0.22.1`。

    * 如果版本不匹配，请使用以下命令将 scikit-learn 和/或 pandas 升级到正确的版本，

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* 如果 `AutoML` SDK 训练版本低于或等于 1.12.0，则需要 `pandas == 0.23.4` 和 `sckit-learn==0.20.3`。
  * 如果版本不匹配，请使用以下命令将 scikit-learn 和/或 pandas 降级到正确的版本，
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>设置

从 1.0.76 版本开始的 `AutoML` 包更改需要在更新到新版本之前卸载之前的版本。

* **`ImportError: cannot import name AutoMLConfig`**

    如果从 v1.0.76 之前的 SDK 版本升级到 v1.0.76 或更高版本后遇到该错误，请先运行 `pip uninstall azureml-train automl` 再运行 `pip install azureml-train-automl` 来解决该错误。 automl_setup.cmd 脚本会自动执行此操作。

* **automl_setup 失败**

  * 在 Windows 上，从 Anaconda 提示符运行 automl_setup。 [安装 Miniconda](https://docs.conda.io/en/latest/miniconda.html)

  * 确保已安装 conda 64 位 4.4.10 版本或更高版本。 可以使用命令 `conda info` 检查该位。 对于 Windows，`platform` 应为 `win-64`，对于 Mac，应为 `osx-64`。 若要检查版本，请使用命令 `conda -V`。 如果安装了以前的版本，可以使用以下命令对其进行更新：`conda update conda`。 若要检查 32 位，请运行 

  * 确保已安装 conda。 

  * Linux - `gcc: error trying to exec 'cc1plus'`

    1. 如果遇到 `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` 错误，请为 Linux 分发版安装 GCC 构建工具。 例如，在 Ubuntu 上，使用命令 `sudo apt-get install build-essential`。

    1. 将新名称作为第一个参数传递给 automl_setup 以创建新的 conda 环境。 使用 `conda env list` 查看现有的 conda 环境，并使用 `conda env remove -n <environmentname>` 删除它们。

* **automl_setup_linux.sh 失败**：如果 automl_setup_linus.sh 在 Ubuntu Linux 上失败，并出现错误：`unable to execute 'gcc': No such file or directory`

  1. 确保已启用出站端口 53 和 80。 在 Azure 虚拟机上，可选择 VM 并单击“网络”，从 Azure 门户执行此操作。
  1. 运行命令 `sudo apt-get update`
  1. 运行命令 `sudo apt-get install build-essential --fix-missing`
  1. 再次运行 `automl_setup_linux.sh`

* **configuration.ipynb 失败**：

  * 对于本地 conda，请首先确保 `automl_setup` 已成功运行。
  * 确保 subscription_id 是正确的。 依次选择“所有服务”和“订阅”，在 Azure 门户中查找 subscription_id。 字符“<”和“>”不应包含在 subscription_id 值中。 例如，`subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 的格式有效。
  * 确保参与者或所有者有权访问订阅。
  * 检查该区域是否为受支持的区域之一：`eastus2`、`eastus`、`westcentralus`、`southeastasia`、`westeurope`、`australiaeast`、`westus2`、`southcentralus`。
  * 确保使用 Azure 门户访问该区域。
  
* **workspace.from_config 失败**：

  如果调用 `ws = Workspace.from_config()` 失败：

  1. 确保 configuration.ipynb 笔记本已成功运行。
  1. 如果正在从不在运行 `configuration.ipynb` 的文件夹下的文件夹中运行笔记本，则将文件夹 aml_config 及其包含的文件 config.json 复制到新文件夹中。 Workspace.from_config 读取笔记本文件夹或其父文件夹的 config.json。
  1. 如果正在使用新的订阅、资源组、工作区或区域，请确保再次运行 `configuration.ipynb` 笔记本。 仅当指定订阅下的指定资源组中已存在工作区时，直接更改 config.json 才会生效。
  1. 如果要更改区域，请更改工作区、资源组或订阅。 即使指定的区域不同，`Workspace.create` 也不会创建或更新工作区（如果已存在）。

## <a name="tensorflow"></a>TensorFlow

从 SDK 1.5.0 版开始，自动化机器学习默认不安装 TensorFlow 模型。 若要安装 TensorFlow 并将其用于自动化 ML 试验，请通过 `CondaDependencies` 安装 `tensorflow==1.12.0`。

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy 失败

* **`import numpy` 在 Windows 中失败**：在某些 Windows 环境中，最新的 Python 3.6.8 版本加载 numpy 时会出现错误。 如果出现此问题，请尝试使用 Python 3.6.7 版本。

* **`import numpy` 失败**：在自动化 ML conda 环境中检查 TensorFlow 版本。 支持的版本为 <1.13 的版本。 如果版本不低于 1.13，请从环境中卸载 TensorFlow。

可以按下面的方式检查 TensorFlow 的版本并卸载：

  1. 启动命令 shell，激活安装了自动化 ML 包的 conda 环境。
  1. 输入 `pip freeze` 并查找 `tensorflow`，如果找到，则列出的版本应 <1.13
  1. 如果列出的版本不受支持，请在命令行界面中使用 `pip uninstall tensorflow`，并输入 y 进行确认。

## `jwt.exceptions.DecodeError`

确切的错误消息：`jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`。

对于不高于 1.17.0 的 SDK 版本，安装可能会导致 PyJWT 的版本不受支持。 检查自动 ml conda 环境中的 PyJWT 版本是否是受支持的版本。 即 PyJWT 版本低于 2.0.0。

可以按下面的方式检查 PyJWT 的版本：

1. 启动命令 shell，并激活安装了自动化 ML 包的 conda 环境。

1. 输入 `pip freeze` 并查找 `PyJWT`，如果找到，则列出的版本应低于 2.0.0

如果列出的版本不是受支持的版本：

1. 请考虑升级到 AutoML SDK 的最新版本：`pip install -U azureml-sdk[automl]`

1. 如果这不可行，请从环境中卸载 PyJWT，并安装正确的版本，如下所示：

    1. 在命令行界面中输入 `pip uninstall PyJWT`，然后输入 `y` 进行确认。
    1. 使用 `pip install 'PyJWT<2.0.0'` 进行安装。
  

## <a name="data-access"></a>数据访问
 
若要运行自动化 ML，需要确保连接到 AzureFile 存储的文件数据存储具有适当的身份验证凭据。 否则，将显示以下消息。 了解如何[更新数据访问身份验证凭据](how-to-train-with-datasets.md#azurefile-storage)。

错误消息：`Could not create a connection to the AzureFileService due to missing credentials. Either an Account Key or SAS token needs to be linked the default workspace blob store.`

## <a name="databricks"></a>Databricks
请参阅[如何使用 Databricks 配置自动化 ML 试验](how-to-configure-databricks-automl-environment.md#troubleshooting)。


## <a name="forecasting-r2-score-is-always-zero"></a>预测 R2 评分始终为零

 如果提供的训练数据的时间序列包含的值与上一个 `n_cv_splits` + `forecasting_horizon` 数据点相同，则会出现此问题。

如果该模式在你的时序中是预期的，可将主要指标切换为“规范化均方根误差”。

## <a name="failed-deployment"></a>部署失败

 对于版本早于 1.18.0 的 SDK，为部署创建的基本映像可能会失败，并出现以下错误：`ImportError: cannot import name cached_property from werkzeug`。

  以下步骤可解决此问题：

  1. 下载模型包
  1. 将包解压缩
  1. 使用解压缩资产进行部署

## <a name="azure-functions-application"></a>Azure Functions 应用程序
  
  自动化 ML 当前不支持 Azure Functions 应用程序。 

## <a name="sample-notebook-failures"></a>示例笔记本失败

 如果示例笔记本失败，并出现属性、方法或库不存在的错误：

* 确保在 Jupyter Notebook 中选择了正确的内核。 内核显示在笔记本页面的右上方。 默认值为 azure_automl。 内核作为笔记本的一部分进行保存。 如果切换到新的 conda 环境，则需要在笔记本中选择新内核。

  * 对于 Azure Notebooks，它应为 Python 3.6。
  * 对于本地 conda 环境，它应为在 automl_setup 中指定的 conda 环境名称。

* 确保笔记本适用于正在使用的 SDK 版本，
  * 通过在 Jupyter Notebook 单元中执行 `azureml.core.VERSION` 来检查 SDK 版本。
  * 可以通过以下步骤从 GitHub 下载示例笔记本的早期版本：
    1. 选择 `Branch` 按钮
    1. 导航到“扩展”选项卡
    1. 选择版本

## <a name="next-steps"></a>后续步骤

+ 详细了解[如何使用自动化机器学习训练回归模型](tutorial-auto-train-models.md)或[如何使用自动化机器学习对远程资源进行训练](concept-automated-ml.md#local-remote)。

+ 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
