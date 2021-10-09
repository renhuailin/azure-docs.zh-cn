---
title: 在 Azure Sentinel 上使用笔记本进行安全搜寻
description: 本文介绍如何使用笔记本和 Azure Sentinel 搜寻功能。
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/24/2021
ms.openlocfilehash: d57941c06f4917c8ff3b27f5e566fc9d5f95474b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272996"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 笔记本搜寻安全威胁

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel 的基础是数据存储；它结合了高性能查询和动态架构并可扩展到大规模数据卷。 Azure 门户和所有 Azure Sentinel 工具都使用一个公共 API 来访问此数据存储。

同一 API 也可用于 [Jupyter](https://jupyter.org/) 笔记本和 Python 等外部工具。 虽然可以在门户中执行许多常见任务，但 Jupyter 扩展了可对此数据执行的操作范围。 它将完整的可编程性与用于机器学习、可视化效果和数据分析的大量库集合组合在一起。 这些属性使 Jupyter 成为安全调查和搜寻的引人注目的工具。

例如，使用笔记本可以进行以下操作：

- 执行非内置于 Azure Sentinel 的分析（例如，一些 Python 机器学习功能）
- 创建非内置于 Azure Sentinel 的数据可视化效果（例如，自定义时间线和进程树）
- 集成 Azure Sentinel 外部的数据源（例如，本地数据集）。

我们已将 Jupyter 体验集成到 Azure 门户中，使你可以轻松地创建和运行笔记本来分析数据。 Kqlmagic 库提供了一种连接，使你可以从 Azure Sentinel 获取 [KQL](https://kusto.azurewebsites.net/docs/kusto/query/index.html) 查询并直接在笔记本中运行它们。

Microsoft 的某些安全分析师开发的几个笔记本打包在 Azure Sentinel 中：

- 其中一些笔记本专门用于特定方案，可以按原样使用。
- 其他笔记本用作示例，以说明可以复制或修改以便在自己的笔记本中使用的技术和功能。

还有一些笔记本也可以从 [Azure Sentinel GitHub 社区](https://github.com/Azure/Azure-Sentinel-Notebooks/)导入。


## <a name="notebook-components"></a>笔记本组件

笔记本包括两个组件：

- **基于浏览器的界面**，你可以在其中输入和运行查询和代码，执行结果也显示在此处。
- ***内核***，负责分析和执行代码本身。

Azure Sentinel 笔记本的内核在 Azure 虚拟机 (VM) 上运行。 如果笔记本包含复杂的机器学习模型，则可以利用几种许可选项来使用功能更强大的虚拟机。

Azure Sentinel 笔记本使用许多常见的 Python 库（例如，pandas、matplotlib、bokeh 等）  。 还提供了许多其他 Python 包供你选择，涵盖的领域包括：

- 可视化效果和图形
- 数据处理和分析
- 统计和数字计算
- 机器学习和深度学习

为了避免将复杂而重复的代码键入或粘贴到笔记本单元格中，大多数 Python 笔记本依赖于名为包的第三方库。 若要使用笔记本中的包，需要安装和导入包。 Azure ML 计算预先安装了最常见的包。 请确保导入包或包的相关部分，如模块、文件、函数或类。

Azure Sentinel 笔记本使用名为 [MSTICPy](https://github.com/Microsoft/msticpy/) 的 Python 包，它是用于数据检索、分析、扩充和可视化的网络安全工具集合。 

MSTICPy 工具专为帮助创建用于搜寻和调查的笔记本而设计，我们正在积极地致力于提供新功能和改进。 有关详细信息，请参阅：

- [MSTIC、Jupyter 和 Python 安全工具文档](https://msticpy.readthedocs.io/)
- [教程：开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy](notebook-get-started.md)
- [适用于 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy 的高级配置](notebooks-msticpy-advanced.md)

[Azure Sentinel GitHub 社区存储库](https://github.com/Azure/Azure-Sentinel-Notebooks/)是存储任何未来的 Azure Sentinel 笔记本的位置，这些笔记本由 Microsoft 生成或由社区提供。

## <a name="manage-access-to-azure-sentinel-notebooks"></a>管理对 Azure Sentinel 笔记本的访问

若要在 Azure Sentinel 中使用 Jupyter 笔记本，首先必须具有适当的权限，具体取决于用户角色。

虽然可以在 JupyterLab 或 Jupyter 经典中运行 Azure Sentinel 笔记本，但在 Azure Sentinel 中，笔记本在 [Azure 机器学习](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML) 平台上运行。 要运行 Azure Sentinel 笔记本，必须对 Azure Sentinel 工作区和 [Azure ML 工作区](../machine-learning/concept-workspace.md)均具有适当的访问权限。

|权限  |说明  |
|---------|---------|
|**Azure Sentinel 权限**     |   与其他 Azure Sentinel 资源一样，若要访问 Azure Sentinel Notebooks 边栏选项卡上的笔记本，则需要 Azure Sentinel 读取者、Azure Sentinel 响应者或 Azure Sentinel 参与者角色。 <br><br>有关详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。|
|**Azure 机器学习权限**     | Azure 机器学习工作区是一种 Azure 资源。 与其他 Azure 资源一样，创建新的 Azure 机器学习工作区时，它附带了默认角色。 可以将用户添加到工作区，并将他们分配给这些内置角色之一。 有关详细信息，请参阅 [Azure 机器学习默认角色](../machine-learning/how-to-assign-roles.md)和 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。 <br><br>   **重要说明**：在 Azure 中可将角色访问权限划分为多个级别。 例如，对工作区具有所有者访问权限的人可能没有对包含工作区的资源组的所有者访问权限。 有关详细信息，请参阅 [Azure RBAC 工作原理](../role-based-access-control/overview.md)。 <br><br>如果你是 Azure ML 工作区的所有者，可以添加和删除工作区的角色，并将角色分配给用户。 有关详细信息，请参阅：<br>    - [Azure 门户](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure 资源管理器模板](../role-based-access-control/role-assignments-template.md)<br> - [Azure 机器学习 CLI](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>如果内置角色不足，还可以创建自定义角色。 自定义角色可能具有该工作区中的读取、写入、删除和计算资源权限。 可以使角色在特定工作区级别、特定资源组级别或特定订阅级别可用。 有关详细信息，请参阅[创建自定义角色](../machine-learning/how-to-assign-roles.md#create-custom-role)。 |
|     |         |

## <a name="create-an-azure-ml-workspace-from-azure-sentinel"></a>从 Azure Sentinel 创建 Azure ML 工作区

此过程介绍如何从 Azure Sentinel 为 Azure Sentinel 笔记本创建 Azure ML 工作区。

**创建工作区**：

1. 在 Azure 门户中，前往“Azure Sentinel” > “威胁管理” > “笔记本”，然后选择“创建新的 AML 工作区”   。

1. 输入以下详细信息，然后选择“下一步”。

    |字段|说明|
    |--|--|
    |**订阅**|选择要使用的 Azure 订阅。|
    |**资源组**|使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。|
    |**工作区名称**|输入用于标识工作区的唯一名称。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。|
    |区域|选择离你的用户和数据资源最近的位置来创建工作区。|
    |**存储帐户**| 存储帐户用作工作区的默认数据存储。 可以创建新的 Azure 存储资源或选择订阅中的现有资源。|
    |**KeyVault**| 密钥保管库用于存储工作区所需的机密和其他敏感信息。 可以创建新的 Azure Key Vault 资源或选择订阅中的现有资源。|
    |**Application insights**| 工作区使用 Azure Application Insights 来存储有关已部署模型的监视信息。 可以创建新的 Azure Application Insights 资源或选择订阅中的现有资源。|
    |**容器注册表**| 容器注册表用于注册在训练和部署中使用的 docker 映像。 为了最大程度地降低成本，生成第一个映像后，才创建新的 Azure 容器注册表。 或者，可以选择立即创建资源或选择订阅中的现有资源，或者选择“无”（如果不想使用任何容器注册表）。|
    | | |

1. 在“网络”选项卡上，选择是使用公共终结点还是使用配置的专用终结点连接工作区。 如果 Azure Sentinel 工作区具有公共终结点，建议对 Azure ML 工作区使用公共终结点，以避免网络通信中的潜在问题。 完成操作后，选择“查看 + 创建”。

1. 在“查看 + 创建”选项卡中，查看信息以验证信息是否正确，然后选择“创建”，开始部署工作区 。 例如：

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="从 Azure Sentinel 查看 + 创建机器学习工作区。":::

    在云中创建工作区可能需要几分钟时间。 在此期间，工作区的“概述”页显示当前部署状态，并在部署完成后进行更新。

1. 部署完成后，可以返回到 Azure Sentinel“笔记本”，然后从新的 Azure ML 工作区启动笔记本。

    如果有多个笔记本，请确保启动笔记本时选择使用默认 AML 工作区。 例如：

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="选择默认 AML 工作区用于笔记本。":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>在 Azure ML 工作区中启动笔记本

创建 AML 工作区后，请在 Azure Sentinel 的 Azure ML 工作区中开始启动笔记本。

> [!NOTE]
> 可以将笔记本视为静态文档，例如在 GitHub 内置静态笔记本呈现器中。 但是，若要在笔记本中运行代码，你必须将笔记本连接到名为 Jupyter 内核的后端进程。 内核运行代码，并保存代码创建的所有变量和对象。 浏览器是此数据的查看器。
>
> 在 Azure ML 中，内核在名为 Azure ML 计算的虚拟机上运行。 计算实例可以支持同时运行多个笔记本。
>

从 Azure Sentinel 启动笔记本：

1. 在 Azure 门户中，导航到“Azure Sentinel” > “威胁管理” > “笔记本”，可在此处看到 Azure Sentinel 提供的笔记本  。

    > [!TIP]
    > 在“笔记本”页的顶部，选择“指南和反馈”，以在面板右侧显示更多的资源和指导 。

1. 选择笔记本以查看其说明、所需的数据类型和数据源。

    找到你想要使用的笔记本时，请选择“保存笔记本”，将其克隆到自己的工作区中。

    按需编辑名称。 如果工作区中已存在笔记本，可以选择覆盖现有笔记本或创建新笔记本。

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="保存笔记本以将其克隆到自己的工作区。":::

1. 保存笔记本后，“保存笔记本”按钮更改为“启动笔记本” 。 选择“启动笔记本”，在工作区中打开它。

    例如：

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="在 AML 工作区中启动笔记本。":::

1. 在页面顶部，选择“计算”实例用于笔记本服务器。

    如果没有计算实例，请[创建新的实例](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio)。 如果计算实例已停止，请确保启动它。 有关详细信息，请参阅[在 Azure 机器学习工作室中运行笔记本](../machine-learning/how-to-run-jupyter-notebooks.md)。

    只有本人可以查看和使用自己创建的计算实例。 用户文件与 VM 分开存储，并在工作区中的所有计算实例之间共享。

    > [!TIP]
    > 如果要创建新的计算实例以测试笔记本，请创建“常规用途”类别的实例。
    >
    > 内核还显示在 Azure ML 窗口右上角。 如果未选择所需的内核，请从下拉列表中选择其他版本。
    >

1. 创建并启动笔记本服务器后，可以开始运行笔记本单元格。 在每个单元格中，选择“运行”图标以运行笔记本代码。

    有关详细信息，请参阅[命令模式快捷方式](../machine-learning/how-to-run-jupyter-notebooks.md)

1. 如果笔记本挂起或者你希望从头开始，可以重启内核，并从头开始重新运行笔记本单元格。 选择“内核操作” > “重启内核”。 例如：

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="重启笔记本内核。":::

    > [!IMPORTANT]
    > 重启内核将擦除所有变量和其他状态。 重新启动后，需要重新运行任何初始化和身份验证单元格。
    >

## <a name="run-code-in-your-notebook"></a>将代码粘贴到笔记本中

在笔记本中：

- “Markdown”单元格包含文本，包括 HTML 和静态图像。
- “代码”单元格包含代码。 选择某一代码单元格后，通过选择该单元格左侧的“播放”图标或按 SHIFT+ENTER 来运行该单元格中的代码。

> [!IMPORTANT]
> 始终按顺序运行笔记本代码单元格。 跳过单元格可能会导致错误。
>

例如，在笔记本中运行以下代码单元格：

```python
# This is your first code cell. This cell contains basic Python code.

# You can run a code cell by selecting it and then selecting
# the Play button to the left of the cell, or by pressing SHIFT+ENTER.
# Code output displays below the code.

print("Congratulations, you just ran this code cell")

y = 2 + 2

print("2 + 2 =", y)

```

上面所示的示例代码生成以下输出：

```python
Congratulations, you just ran this code cell

2 + 2 = 4
```

笔记本代码单元格中的变量集在单元格格之间保持不变，这样可以将单元格链接在一起。 例如，下面的代码单元格使用上一个单元格中的 `y` 值：

```python
# Note that output from the last line of a cell is automatically
# sent to the output cell, without needing the print() function.

y + 2
```

输出为：

```output
6
```

## <a name="download-all-azure-sentinel-notebooks"></a>下载所有 Azure Sentinel 笔记本

本部分介绍如何使用 Git 从 Azure Sentinel 笔记本内部将 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel-Notebooks/)提供的所有笔记本直接下载到 Azure ML 工作区。

将 Azure Sentinel 笔记本存储在 Azure ML 工作区中，可以轻松更新它们。

1. 在 Azure Sentinel 笔记本中，将以下代码输入空单元格中，然后运行该单元格：

    ```python
    !git clone https://github.com/Azure/Azure-Sentinel-Notebooks.git azure-sentinel-nb
    ```

    GitHub 存储库内容的副本是在 Azure ML 工作区中用户文件夹的 azure-sentinel-nb 目录中创建的。

1. 将所需的笔记本从此文件夹复制到工作目录。

1. 若要使用 GitHub 中的最近更改来更新笔记本，请运行：

    ```python
    !cd azure-sentinel-nb && git pull
    ```


## <a name="troubleshooting"></a>疑难解答

通常，笔记本无缝创建或附加到内核，无需进行任何手动更改。 如果收到错误，或者笔记本似乎未运行，则可能需要检查内核的版本和状态。

如果笔记本遇到问题，请参阅 [Azure 机器学习故障排除](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting)。

### <a name="force-caching-for-user-accounts-and-credentials-between-notebook-runs"></a>在笔记本运行之间强制缓存用户帐户和凭据

默认情况下，即使对于同一会话，也不会在笔记本运行之间缓存用户帐户和凭据。

在会话期间强制缓存：

1. 使用 Azure CLI 进行身份验证。 在空笔记本单元格中，输入并运行以下代码：

    ```python
    !az login
    ```

    随即显示以下输出：

    ```python
    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the 9-digit device code to authenticate.
    ```

1. 选择并复制输出中的九个字符令牌，并选择 `devicelogin` URL 以跳到指示的页面。 

1. 将令牌粘贴到对话框中，并在出现提示时继续登录。

    成功登录后，将看到以下输出：

    ```python
    Subscription <subscription ID> 'Sample subscription' can be accessed from tenants <tenant ID>(default) and <tenant ID>. To select a specific tenant when accessing this subscription, use 'az login --tenant TENANT_ID'.

> [!NOTE]
> The following tenants don't contain accessible subscriptions. Use 'az login --allow-no-subscriptions' to have tenant level access.
>
> ```
> <tenant ID> 'foo'
><tenant ID> 'bar'
>[
> {
>    "cloudName": "AzureApp",
>    "homeTenantId": "<tenant ID>",
>    "id": "<ID>",
>    "isDefault": true,
>    "managedByTenants": [
>    ....
>```
>
### Error: *Runtime dependency of PyGObject is missing*

If the *Runtime dependency of PyGObject is missing* error appears when you load a query provider, try troubleshooting using the following steps:

1. Proceed to the cell with the following code and run it:

    ```python
    qry_prov = QueryProvider("AzureSentinel")
    ```

    将显示类似于下面的警告，指示缺少 Python 依赖项 (`pygobject`)：

    ```output
    Runtime dependency of PyGObject is missing.

    Depends on your Linux distribution, you can install it by running code similar to the following:
    sudo apt install python3-gi python3-gi-cairo gir1.2-secret-1

    If necessary, see PyGObject's documentation: https://pygobject.readthedocs.io/en/latest/getting_started.html

    Traceback (most recent call last):
      File "/anaconda/envs/azureml_py36/lib/python3.6/site-packages/msal_extensions/libsecret.py", line 21, in <module>
    import gi  # https://github.com/AzureAD/microsoft-authentication-extensions-for-python/wiki/Encryption-on-Linux
    ModuleNotFoundError: No module named 'gi'
    ```

1. 使用位于 Azure Sentinel 笔记本 GitHub 存储库中的 [aml-compute-setup.sh](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/HowTos/aml-compute-setup.sh) 脚本，在计算实例的所有笔记本和 Anaconda 环境中自动安装 `pygobject`。

> [!TIP]
> 还可以通过从笔记本运行以下代码来解决此警告：
>
> ```python
> !conda install --yes -c conda-forge pygobject
> ```
>


## <a name="next-steps"></a>后续步骤

在 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel-Notebooks)中共享的笔记本旨在用作开发自己的笔记本时可以使用的有用工具、插图和代码示例。

欢迎提供反馈、建议、功能请求、笔记本、bug 报告或对现有笔记本的改进和补充。 请前往 [Azure Sentinel GitHub 社区](https://github.com/Azure/Azure-Sentinel)创建问题或分支并上传贡献。

- 通过浏览一些笔记本模板（例如 [Azure Log Analytics 上的凭据扫描](https://www.youtube.com/watch?v=OWjXee8o04M)和“引导式调查 - 处理警报”）详细了解在威胁搜寻和调查中如何使用笔记本。

    在 Azure Sentinel >“笔记本” > “模板”选项卡中找到更多笔记本模板。

- 有关更多笔记本，请参阅 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel-Notebooks)：

    - [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) 目录中保存了示例笔记本以及可用于显示预期输出的数据。

    - [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) 目录包含描述设置默认 Python 版本、从笔记本创建 Azure Sentinel 书签等概念的笔记本。


有关详细信息，请参阅：

  - [教程：开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy](notebook-get-started.md)
- [教程：Azure Sentinel 笔记本 - 入门](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)（视频）
- [教程：在不离开 Azure ML 工作室的情况下编辑和运行 Jupyter 笔记本](https://www.youtube.com/watch?v=AAj-Fz0uCNk)（视频）
- [网络研讨会：Azure Sentinel 笔记本基础](https://www.youtube.com/watch?v=rewdNeX6H94)
- [主动搜寻威胁](hunting.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
- [Jupyter、msticpy 和 Microsoft Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
