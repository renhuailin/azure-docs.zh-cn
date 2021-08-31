---
title: 预生成 Docker 映像的 Python 扩展性
titleSuffix: Azure Machine Learning
description: 使用 Python 包扩展性解决方案扩展预生成的 Docker 映像
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 08139501886f580033aae46bdabd55ed300c5059
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710012"
---
# <a name="python-package-extensibility-for-prebuilt-docker-images-preview"></a>预生成 Docker 映像的 Python 包扩展性（预览版）

[用于模型推理的预生成 Docker 映像](concept-prebuilt-docker-images-inference.md)包含常用机器学习框架的包。 可使用两种方法来添加 Python 包，而无需重新生成 Docker 映像：

* [动态安装](#dynamic)：当 Docker 容器启动时，此方法使用一个 [requirements](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) 文件自动还原 Python 包。

    若要进行快速原型制作，请考虑使用此方法。 映像启动时，将使用 `requirements.txt` 文件还原包。 此方法会增加映像启动时间，你必须等待更长的时间，然后部署才能处理请求。

* [预安装的 Python 包](#preinstalled)：提供包含预安装的 Python 包的目录。 在部署过程中，此目录将装载到容器中，供入口脚本 (`score.py`) 使用。

    此方法可用于生产部署。 由于包含包的目录已装载到映像中，因此即使部署无法访问公共 Internet，也可以使用该目录。 例如，在已部署到受保护 Azure 虚拟网络的情况下。

> [!IMPORTANT]
> 将预生成的 Docker 映像与 Azure 机器学习配合使用的功能目前以预览版提供。 预览功能按原样提供，不保证支持或服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关创建工作区的教程，请参阅 [Azure 机器学习入门](quickstart-create-resources.md)。
* 熟悉 Azure 机器学习[环境](how-to-use-environments.md)的用法。
* 熟悉在 Azure 机器学习中的[何处以及如何部署模型](how-to-deploy-and-where.md)。

<a id="dynamic"></a>

## <a name="dynamic-installation"></a>动态安装

当映像启动时，此方法使用一个 [requirements](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) 文件自动还原 Python 包。

若要通过 requirements.txt 扩展预生成的 Docker 容器映像，请执行以下步骤：

1. 连同 `score.py` 脚本一起创建 `requirements.txt` 文件。
2. 将全部所需的包添加到 `requirements.txt` 文件。
3. 将 Azure 机器学习[环境](how-to-use-environments.md)中的 `AZUREML_EXTRA_REQUIREMENTS_TXT` 环境变量设置为 `requirements.txt` 文件的位置。

部署后，系统会自动为评分脚本还原包。

> [!TIP]
> 即使是在原型制作时，我们也建议在 `requirements.txt` 中固定每个包版本。
> 例如，使用 `scipy == 1.2.3`，而不仅仅是 `scipy` 或甚至 `scipy > 1.2.3`。
> 如果你不固定确切的版本，而 `scipy` 发布了新版本，则可能会损坏评分脚本，导致在部署和缩放期间失败。

以下示例演示如何设置 `AZUREML_EXTRA_REQUIRMENTS_TXT` 环境变量：

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies 

myenv = Environment(name="my_azureml_env")
myenv.docker.enabled = True
myenv.docker.base_image = <MCR-path>
myenv.python.user_managed_dependencies = True

myenv.environment_variables = {
    "AZUREML_EXTRA_REQUIREMENTS_TXT": "requirements.txt"
}
```

下图是动态安装过程的视觉表示形式：

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/dynamic-install-python-extend.svg" alt-text="动态安装过程示意图":::

<a id="preinstalled"></a>

## <a name="pre-installed-python-packages"></a>预安装的 Python 包

此方法将你提供的目录装载到映像中。 然后，此目录中的 Python 包可供入口脚本 (`score.py`) 使用。

若要通过预安装的 python 包扩展预生成的 Docker 容器映像，请执行以下步骤：

> [!IMPORTANT]
> 必须使用与 Python 3.7 兼容的包。 所有当前映像已固定到 Python 3.7。

1. 使用 [virtualenv](https://virtualenv.pypa.io/) 创建虚拟环境。

1. 安装依赖项。 例如，如果在 `requirements.txt` 中包含了依赖项列表，则可以使用该列表通过 `pip install -r requirements.txt` 进行安装，或者只是使用 `pip install` 安装单个依赖项。

1. 指定 `AZUREML_EXTRA_PYTHON_LIB_PATH` 环境变量时，请确保指向正确的站点包目录，该目录因环境名称和 Python 版本而异。 以下代码演示如何为名为 `myenv` 的虚拟环境和 Python 3.7 设置路径：


    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies 

    myenv = Environment(name='my_azureml_env')
    myenv.docker.enabled = True
    myenv.docker.base_image = <MCR-path>
    myenv.python.user_managed_dependencies = True

    myenv.environment_variables = {
        "AZUREML_EXTRA_PYTHON_LIB_PATH": "myenv/lib/python3.7/site-packages"
    }
    ```

下图是预安装包过程的视觉表示形式：

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/pre-install-python-extend.svg" alt-text="使用预安装包的过程示意图":::

### <a name="common-problems"></a>常见问题

仅当 `myenv` 站点包目录包含所有依赖项时，装载解决方案才起作用。 如果本地环境使用安装在其他位置的依赖项，则这些依赖项不会在映像中提供。

下面是可能导致此问题的一些因素：

* `virtualenv` 默认创建一个隔离的环境。 激活虚拟环境后，无法使用全局依赖项。
* 如果某个 `PYTHONPATH` 环境变量指向全局依赖项，它可能会干扰虚拟环境。 激活环境后，运行 `pip list` 和 `pip freeze` 以确保环境中没有不需要的依赖项。
* Conda 和 `virtualenv` 环境可能会相互干扰。 确保不要同时使用 [Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) 环境和 `virtualenv`。

## <a name="limitations"></a>限制

### <a name="modelpackage"></a>Model.package()

* [Model.package()](/python/api/azureml-core/azureml.core.model(class)) 方法可让你创建 Docker 映像形式的模型包，或者在 Dockerfile 生成上下文中创建模型包。 对预生成的推理 Docker 映像使用 Model.package() 会触发中间映像生成，这会将非 root 用户更改为 root 用户。

* 建议使用我们的 Python 包扩展性解决方案。 如果需要其他依赖项（如 `apt` 包），请创建自己的[从推理映像扩展的 Dockerfile](how-to-extend-prebuilt-docker-image-inference.md#buildmodel)。

## <a name="frequently-asked-questions"></a>常见问题

* 在 requirements.txt 扩展性方法中，文件名是否必须为 `requirements.txt`？

        
    ```python
    myenv.environment_variables = {
        "AZUREML_EXTRA_REQUIREMENTS_TXT": "name of your pip requirements file goes here"
    }
    ```

* 你们是否可以总结一下 `requirements.txt` 方法与装载方法相比较的信息？

    一开始请使用 requirements.txt 方法进行原型制作。
    经过一定次数的迭代后，如果你确信哪些包（和版本）能够成功完成模型部署，请切换到“装载解决方案”。
        
    下面是详细的比较。

    | 比较项 | Requirements.txt（动态安装） | 包装载 |
    | ----- | ----- | ------ |
    | 解决方案  | 创建一个 `requirements.txt` 以用于在容器启动时安装指定的包。 | 创建具有所有依赖项的本地 Python 环境。 在运行时将此目录装载到容器中。 |
    | 包安装           | 无需额外安装（假设已安装 pip）                                                                                                          | 虚拟环境或 Conda 环境安装。                                                                                   |
    | 虚拟环境设置              | 无需额外设置虚拟环境，因为用户可以按需使用 pip freeze 拉取当前本地用户环境来创建 `requirements.txt`。 | 需要设置干净的虚拟环境，这可能需要执行额外的步骤，具体取决于当前的用户本地环境。                        |
    | [调试](how-to-inference-server-http.md)                 | 易于设置和调试服务器，因为依赖项已明确列出。 | 在调试服务器时，不干净的虚拟环境可能导致问题。 例如，可能无法明确知道错误是来源于环境还是用户代码。 |
    | 横向扩展期间的一致性 | 不一致，因为此方法依赖于外部 PyPi 包以及用户是否固定了其依赖项。 这些外部下载可能会导致异常行为。                                 | 仅依赖于用户环境，因此不会出现一致性问题。                                                                             |

* 为何在容器中找不到我的 `requirements.txt` 和已装载的依赖项目录？

    在本地验证是否正确设置了环境变量。 接下来，验证指定的路径是否拼写正确，以及这些路径是否存在。
    检查是否在[推理配置](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)构造函数中正确设置了源目录。

* 是否可以在预生成的推理 Docker 映像中替代 Python 包依赖项？

    是的。 如果你要使用已在推理映像中安装的其他 Python 包版本，我们的扩展性解决方案将遵循你的版本。 请确保这两个版本之间没有冲突。

## <a name="best-practices"></a>最佳实践

* 请参阅[加载已注册的模型](how-to-deploy-advanced-entry-script.md#load-registered-models)文档。注册模型目录时，请不要在该目录中包含你的评分脚本、已装载的依赖项目录或 `requirements.txt`。


* 有关如何加载已注册模型或本地模型的详细信息，请参阅[在何处以及如何部署](how-to-deploy-and-where.md?tabs=azcli#define-a-dummy-entry-script)。

## <a name="bug-fixes"></a>Bug 修复

### <a name="2021-07-26"></a>2021-07-26

* `AZUREML_EXTRA_REQUIREMENTS_TXT` 和 `AZUREML_EXTRA_PYTHON_LIB_PATH` 现在总是相对于评分脚本的目录。
例如，如果 requirements.txt 和评分脚本都位于 my_folder 中，则需要将 `AZUREML_EXTRA_REQUIREMENTS_TXT` 设置为 requirements.txt。 不再将 `AZUREML_EXTRA_REQUIREMENTS_TXT` 设置为 my_folder/requirements.txt。

## <a name="next-steps"></a>后续步骤

若要详细了解如何部署模型，请参阅[如何部署模型](how-to-deploy-and-where.md)。

若要了解如何排查预生成 Docker 映像部署的问题，请参阅[如何排查预生成 Docker 映像部署的问题](how-to-troubleshoot-prebuilt-docker-image-inference.md)。
