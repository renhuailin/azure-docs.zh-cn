---
title: 开发适用于 Azure Functions 的 Python 辅助角色扩展
description: 了解如何创建和发布辅助角色扩展，以便将中间件行为注入到 Azure 中运行的 Python 函数。
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 6/1/2021
ms.custom: devx-track-python
ms.openlocfilehash: 592562caa57980500d9bbc19b015a98e5604bb7a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987833"
---
# <a name="develop-python-worker-extensions-for-azure-functions"></a>开发适用于 Azure Functions 的 Python 辅助角色扩展

Azure Functions 可让你将自定义行为集成为 Python 函数执行的一部分。 利用此功能可以创建可供客户在其自己的函数应用中轻松使用的业务逻辑。 有关详细信息，请参阅 [Python 开发人员参考](functions-reference-python.md#python-worker-extensions)。

本教程介绍以下操作： 
> [!div class="checklist"]
> * 创建适用于 Azure Functions 的应用程序级 Python 辅助角色扩展。 
> * 在应用中像客户那样使用该扩展。 
> * 打包并发布扩展以供使用。 

## <a name="prerequisites"></a>先决条件

在开始之前，必须满足以下要求：

* [Python 3.6.x or above](https://www.python.org/downloads/release/python-374/)。 若要查看 Azure Functions 支持的 Python 版本的完整列表，请参阅 [Python 开发人员指南](functions-reference-python.md#python-version)。

* [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3568 或更高版本。

* 已在某个[支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上安装 [Visual Studio Code](https://code.visualstudio.com/)。

## <a name="create-the-python-worker-extension"></a>创建 Python 辅助角色扩展

创建的扩展将在控制台日志和 HTTP 响应正文中报告 HTTP 触发器调用的运行时间。

### <a name="folder-structure"></a>文件夹结构

扩展项目的文件夹应采用如下所示的结构：

```
<python_worker_extension_root>/
 | - .venv/
 | - python_worker_extension_timer/
 | | - __init__.py
 | - setup.py
 | - readme.md
```

| 文件夹/文件 | 说明 |
| --- | --- |
| .venv/ | （可选）包含用于本地开发的 Python 虚拟环境。 |
| python_worker_extension/ | 包含 Python 辅助角色扩展的源代码。 此文件夹包含要发布到 PyPI 的主 Python 模块。 |
| setup.py | 包含 Python 辅助角色扩展包的元数据。 |
| readme.md | （可选）包含扩展的说明和用法。 此内容将作为说明显示在 PyPI 项目的主页中。 |

### <a name="configure-project-metadata"></a>配置项目元数据

首先创建 `setup.py`，以提供有关包的最基本信息。 为了确保将扩展正确分发并集成到客户的函数应用中，请确认 `'azure-functions >= 1.7.0, < 2.0.0'` 位于 `install_requires` 节中。

在以下模板中，应根据需要更改 `author`、`author_email`、`install_requires`、`license`、`packages` 和 `url` 字段。

:::code language="python" source="~/azure-functions-python-worker-extension/setup.py":::

接下来，在应用程序级范围实现扩展代码。

### <a name="implement-the-timer-extension"></a>实现计时器扩展

在 `python_worker_extension_timer/__init__.py` 中添加以下代码以实现应用程序级扩展：

:::code language="python" source="~/azure-functions-python-worker-extension/python_worker_extension_timer/__init__.py":::

此代码继承自 [AppExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py)，因此扩展将应用于应用中的每个函数。 还可以通过从 [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py) 继承，在函数级范围实现该扩展。

`init` 方法是导入扩展类时由辅助角色调用的类方法。 可在此处对扩展执行初始化操作。 在这种情况下，将初始化一个哈希映射，以记录每个函数的调用开始时间。

`configure` 方法是面向客户的方法。 在自述文件中，可以告知客户何时需要调用 `Extension.configure()`。 自述文件还应该阐述扩展的功能、可能的配置及扩展的用法。 在此示例中，客户可以选择是否在 `HttpResponse` 中报告运行时间。

`pre_invocation_app_level` 方法在函数运行之前由 Python 辅助角色调用。 它提供函数中的信息，例如函数上下文和参数。 在此示例中，扩展将记录一条消息，并基于某个调用的 invocation_id 记录该调用的开始时间。

同样，`post_invocation_app_level` 也是在函数执行后调用的。 此示例根据开始时间和当前时间计算运行时间。 它还会覆盖 HTTP 响应的返回值。

## <a name="consume-your-extension-locally"></a>在本地使用扩展

创建扩展后，可以在应用项目中使用该扩展，以验证它是否按预期方式工作。 

### <a name="create-an-http-trigger-function"></a>创建 HTTP 触发器函数

1. 为应用项目创建一个新文件夹，然后导航到该文件夹。 

1. 在相应的 shell（例如 Bash）中，运行以下命令以初始化项目：

    ```bash
    func init --python
    ```

1. 使用以下命令创建允许匿名访问的新 HTTP 触发器函数：

    ```bash
    func new -t HttpTrigger -n HttpTrigger -a anonymous
    ```

### <a name="activate-a-virtual-environment"></a>激活虚拟环境

1. 按下面所示创建基于 OS 的 Python 虚拟环境：

    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    python3 -m venv .venv
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    py -m venv .venv
    ``` 
    ---

1. 按下面所示激活基于 OS 的 Python 虚拟环境：
    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    source .venv/bin/activate
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    .venv\Scripts\Activate.ps1
    ``` 
    ---

### <a name="configure-the-extension"></a>配置扩展

1. 使用以下命令安装函数应用项目的远程包： 
    
    ```bash
    pip install -r requirements.txt
    ```

1. 按下面所示在可编辑模式下从本地文件路径安装扩展：

    ```bash
    pip install -e <PYTHON_WORKER_EXTENSION_ROOT>
    ```

    在此示例中，请将 `<PYTHON_WORKER_EXTENSION_ROOT>` 替换为扩展项目的文件位置。   
    当客户使用你的扩展时，他们会改为将你的扩展包位置添加到 requirements.txt 文件，如以下示例所示：

    # <a name="pypi"></a>[PyPI](#tab/pypi)
    ```python
    # requirements.txt
    python_worker_extension_timer==1.0.0
    ``` 
    # <a name="github"></a>[GitHub](#tab/github)
    
    ```python
    # requirements.txt
    git+https://github.com/Azure-Samples/python-worker-extension-timer@main
    ```
    ---

1. 打开 local.settings.json 项目文件，将以下字段添加到 `Values`：

    ```json
    "PYTHON_ENABLE_WORKER_EXTENSIONS": "1" 
    ```

    在 Azure 中运行时，请改为将 `PYTHON_ENABLE_WORKER_EXTENSIONS=1` 添加到[函数应用中的应用设置](functions-how-to-use-azure-function-app-settings.md#settings)。

1. 在 \_\_init.py\_\_ 中的 `main` 函数前面添加以下两行代码：

    ```python
    from python_worker_extension_timer import TimerExtension
    TimerExtension.configure(append_to_http_response=True)
    ```

    此代码导入 `TimerExtension` 模块并设置 `append_to_http_response` 配置值。

### <a name="verify-the-extension"></a>验证扩展

1. 在应用项目的根文件夹中，使用 `func host start --verbose` 启动函数主机。 在输出中，应会看到类似于 `https://localhost:7071/api/HttpTrigger` 的函数本地终结点。

1. 在浏览器中，向 `https://localhost:7071/api/HttpTrigger` 发送 GET 请求。 应会看到如下所示的响应，其中追加了请求的 TimeElapsed 数据。 

    <pre>
    This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response. (TimeElapsed: 0.0009996891021728516 sec)
    </pre>

## <a name="publish-your-extension"></a>发布扩展

创建并验证扩展后，仍然需要完成以下剩余发布任务：

> [!div class="checklist"]
> + 选择许可证。
> + 创建 readme.md 和其他文档。 
> + 将扩展库发布到 Python 包注册表或版本控制系统 (VCS)。

# <a name="pypi"></a>[PyPI](#tab/pypi)

若要将扩展发布到 PyPI，请执行以下操作：

1. 运行以下命令，以在默认 Python 环境或虚拟环境中安装 `twine` 和 `wheel`：

    ```bash
    pip install twine wheel
    ```

1. 从扩展存储库中删除旧的 `dist/` 文件夹。

1. 运行以下命令在 `dist/` 中生成新包：

    ```bash
    python setup.py sdist bdist_wheel
    ```

1. 运行以下命令将该包上传到 PyPI：

    ```bash
    twine upload dist/*
    ```

    在上传过程中，可能需要提供 PyPI 帐户凭据。

执行这些步骤后，客户可以通过在其 requirements.txt 中包含你的包名称来使用你的扩展。

有关详细信息，请参阅[官方的 Python 打包教程](https://packaging.python.org/tutorials/packaging-projects/)。

# <a name="github"></a>[GitHub](#tab/github)

还可以将扩展源代码连同 setup.py 文件一起发布到 GitHub 存储库，如[此示例存储库](https://github.com/Azure-Samples/python-worker-extension-timer)中所示。 

有关 pip 中的 VCS 支持的详细信息，请参阅[官方的 pip VCS 支持文档](https://pip.pypa.io/en/stable/cli/pip_install/#vcs-support)。

---

## <a name="examples"></a>示例

+ 可以在 [python_worker_extension_timer](https://github.com/Azure-Samples/python-worker-extension-timer) 示例存储库中查看本文中已完成的示例扩展项目。 

+ OpenCensus 集成是一个开源项目，它使用扩展接口在 Azure Functions Python 应用中集成遥测跟踪。 请参阅 [opencensus-python-extensions-azure](https://github.com/census-ecosystem/opencensus-python-extensions-azure/tree/main/extensions/functions) 存储库来查看此 Python 辅助角色扩展的实现。

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Python 开发的详细信息，请参阅以下资源：

* [Azure Functions Python 开发人员指南](functions-reference-python.md)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
