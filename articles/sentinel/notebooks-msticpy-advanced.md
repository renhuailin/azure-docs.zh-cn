---
title: 适用于 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy 的高级配置 | Microsoft Docs
description: 了解使用 Azure Sentinel 时可用于 Jupyter Notebook（包含 MSTICPy）的高级配置。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 09/12/2021
ms.openlocfilehash: b3a33bc1f72af20a28bffbd96df73b95d31e3cca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594349"
---
# <a name="advanced-configurations-for-jupyter-notebooks-and-msticpy-in-azure-sentinel"></a>适用于 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy 的高级配置

本文介绍了在 Azure Sentinel 中使用 Jupyter Notebook 和 MSTICPy 时会用到的高级配置。

有关详细信息，请参阅[使用 Jupyter Notebook 搜寻安全威胁](notebooks.md)和[教程：开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy](notebook-get-started.md)。

## <a name="prerequisites"></a>先决条件

本文是[教程：开始使用 Azure Sentinel 中的 Jupyter Notebook 和 MSTICPy](notebook-get-started.md) 的延续内容。 建议先按此教程执行操作，然后继续进行以下所述的高级过程。

## <a name="specify-authentication-parameters-for-azure-and-azure-sentinel-apis"></a>为 Azure 和 Azure Sentinel API 指定身份验证参数

本过程介绍如何在 **msticpyconfig.yaml** 文件中为 Azure Sentinel 和其他 Azure API 资源配置身份验证参数。

若要在 MSTICPy 设置编辑器中添加 Azure 身份验证和 Azure Sentinel API 设置：

1. 转到下一个单元格，使用并运行以下代码：

    ```python
    mpedit.set_tab("Data Providers")
    mpedit
    ```

1. 在“数据提供程序”选项卡中，选择“AzureCLI” > “添加”。

1. 选择要使用的身份验证方法：

    - 尽管可以使用 [Azure 默认值](notebook-get-started.md#specify-the-azure-cloud-and-azure-authentication-methods)以外的方法集，但这种用法并非典型配置。
    - 除非要使用“env”（环境变量）身份验证，否则请将“clientId”、“tenantiId”和“clientSecret”字段保留为空。
    - MSTICPy 还支持使用客户端应用 ID 和机密进行身份验证，但不建议你这样做。 在此类情况下，请直接在“数据提供程序”选项卡中定义“clientId”、“tenantId”和“clientSecret”字段。

1. 选择“保存文件”以保存更改。

## <a name="define-autoloading-query-providers"></a>定义自动加载的查询提供程序

定义运行 `nbinit.init_notebook` 函数时 MSTICPy 要自动加载的任何所需的查询提供程序。

当频繁创作新的笔记本时，自动加载查询提供程序可以确保在加载其他组件（例如 Pivot 函数和 Notebooklets）之前加载所需的提供程序，从而节省时间。

若要添加自动加载的查询提供程序：

1. 转到下一个单元格，使用并运行以下代码：

    ```python
    mpedit.set_tab("Autoload QueryProvs")
    mpedit
    ```

1. 在“自动加载 QueryProv”选项卡中：

   - 对于 Azure Sentinel 提供程序，同时指定要连接到的提供程序名称和工作区名称。
   - 对于其他查询提供程序，仅指定提供程序名称。

   每个提供程序还具有以下可选值：

   - 自动连接：默认情况下，此选项定义为“True”，MSTICPy 会尝试在加载提供程序后立即对其进行身份验证。 MSTICPy 假设你已在设置中为提供程序配置凭据。

   - 别名：当 MSTICPy 加载提供程序时，它会为提供程序分配 Python 变量名。 默认情况下，Azure Sentinel 提供程序的变量名为 qryworkspace_name，其他提供程序的变量名为 qryprovider_name。

        例如，如果为 ContosoSOC 工作区加载查询提供程序，则将在笔记本环境中创建名称为 `qry_ContosoSOC` 的查询提供程序。 如果要使用更短或更容易键入和记住的内容，请添加别名。 提供程序变量名称将为 `qry_<alias>`，其中 `<alias>` 将替换为所提供的别名。

        采用此机制加载的提供程序还会添加到 MSTICPy 的 `current_providers` 属性中，例如，以下代码中将会使用该属性：

        ```python
        import msticpy
        msticpy.current_providers
        ```

1. 选择“保存设置”以保存更改。

## <a name="define-autoloaded-msticpy-components"></a>定义自动加载的 MSTICPy 组件

本过程介绍如何定义运行 `nbinit.init_notebook` 函数时 MSTICPy 自动加载的其他组件。

支持的组件按以下顺序包括：

1. TILookup：[TI 提供程序库](notebook-get-started.md#add-threat-intelligence-provider-settings)
1. GeoIP：想要使用的 [GeoIP 提供程序](notebook-get-started.md#add-geoip-provider-settings)
1. AzureData：用于查询有关 [Azure 资源](#specify-authentication-parameters-for-azure-and-azure-sentinel-apis)的详细信息的模块
1. AzureSentinelAPI：用于查询 [Azure Sentinel API](#specify-authentication-parameters-for-azure-and-azure-sentinel-apis) 的模块
1. Notebooklets：[msticnb 包](https://msticnb.readthedocs.io/en/latest/)中的 Notebooklets
1. Pivot：Pivot 函数

> [!NOTE]
> 组件按此顺序进行加载，因为 Pivot 组件需要加载查询和其他提供程序才能查找其附加到实体的 Pivot 函数。 有关详细信息，请参阅 [MSTICPy 文档](https://msticpy.readthedocs.io/en/latest/data_analysis/PivotFunctions.html)。
>

若要定义自动加载的 MSTICPy 组件：

1. 转到下一个单元格，使用并运行以下代码：

   ```python
   mpedit.set_tab("Autoload Components")
   mpedit
   ```

1. 在“自动加载组件”选项卡中，定义任何所需参数值。 例如：

   - GeoIpLookup。  输入要使用的 GeoIP 提供程序的名称，“GeoLiteLookup”或“IPStack”。  有关详细信息，请参阅[添加 GeoIP 提供程序设置](notebook-get-started.md#add-geoip-provider-settings)。

   - AzureData 和 AzureSentinelAPI 组件。  定义以下值：

      - auth_methods：替代 AzureCLI 的默认设置，并使用所选的方法进行连接。
      - Auto-connect：设置为 false，以在不连接的情况下加载。

      有关详细信息，请参阅[为 Azure 和 Azure Sentinel API 指定身份验证参数](#specify-authentication-parameters-for-azure-and-azure-sentinel-apis)。

   - Notebooklets。 Notebooklets 组件具有单个参数块：AzureSentinel。

      使用此语法指定 Azure Sentinel 工作区：`workspace:\<workspace name>`。 工作区名称必须是“Azure Sentinel”选项卡中定义的工作区之一。

      如果要添加更多要发送到 `notebooklets init` 函数的参数，请将这些参数指定为以换行符分隔的键值对。 例如：

      ```python
      workspace:<workspace name>
      providers=["LocalData","geolitelookup"]
      ```

      有关详细信息，请参阅 [MSTICNB (MSTIC Notebooklets) 文档](https://msticnb.readthedocs.io/en/latest/msticnb.html#msticnb.data_providers.init)。

    某些组件（例如 TILookup 和 Pivot）不需要任何参数。

1. 选择“保存设置”以保存更改。


## <a name="switch-between-python-36-and-38-kernels"></a>在 Python 3.6 和 3.8 内核之间切换

如果要在 Python 3.65 和 3.8 内核之间切换，你可能会发现 MSTICPy 和其他包没有按预期方式安装。

当 `!pip install pkg` 命令在第一个环境中正确安装，但未在第二个环境中正确安装时，就可能出现这种情况。 这会导致第二个环境无法导入或使用包。

建议不要使用 `!pip install...` 在 Azure ML 笔记本中安装包。 改用以下选项之一：

- 在笔记本中使用 %pip line magic。 运行：

  ```python

  %pip install --upgrade msticpy
  ```

- 从终端安装：

  1. 在 Azure ML 笔记本中打开终端，并运行以下命令：

     ``` bash
     conda activate azureml_py38
     pip install --upgrade msticpy
     ```

  1. 关闭终端并重启内核。


## <a name="set-an-environment-variable-for-your-msticpyconfigyaml-file"></a>为 msticpyconfig.yaml 文件设置环境变量

如果要在 Azure ML 中运行，且 msticpyconfig.yaml 文件位于用户文件夹的根目录中，则 MSTICPy 将自动查找这些设置。 但是，如果要在其他环境中运行笔记本，请按照本部分中的说明设置一个指向配置文件位置的环境变量。

通过在环境变量中定义 msticpyconfig.yaml 文件的路径，可以将文件存储在已知位置，并确保始终加载相同的设置。

如果要将不同的设置用于不同的笔记本，请使用多个配置文件和多个环境变量。

1. 确定 msticpyconfig.yaml 文件的位置，例如，位于 ~/.msticpyconfig.yaml 或 %userprofile%/msticpyconfig.yaml 中。

    Azure ML 用户：如果将配置文件存储在 Azure ML 用户文件夹中，则 MSTICPy `init_notebook` 函数（在初始化单元格中运行）将自动查找并使用该文件，而无需设置 MSTICPYCONFIG 环境变量。

    但是，如果此文件中还存储了机密，则建议将配置文件存储在计算本地驱动器中。 计算内部存储仅创建计算的人员才可访问，而共享存储则是有权访问 Azure ML 工作区的所有人都可访问。

    有关详细信息，请参阅[什么是 Azure 机器学习计算实例？](/azure/machine-learning/concept-compute-instance)。

1. 如果需要，请将 msticpyconfig.yaml 文件复制到所选位置。

1. 将 MSTICPYCONFIG 环境变量设置为指向该位置。

使用以下过程之一来定义 MSTICPYCONFIG 环境变量。
# <a name="windows"></a>[Windows](#tab/windows)

例如，若要在 Windows 系统上设置 MSTICPYCONFIG 环境变量：

1. 根据需要将 msticpyconfig.yaml 文件移动到计算实例。
1. 打开“系统属性”对话框的“高级”选项卡。
1. 选择“环境变量...”以打开“环境变量”对话框。
1. 在“系统变量”区域中，选择“新建...”，并按如下所示定义值：

    - 变量名称：定义为 `MSTICPYCONFIG`
    - 变量值：输入 msticpyconfig.yaml 文件的路径

# <a name="linux"></a>[Linux](#tab/linux)

本过程介绍如何更新 .bashrc 文件，以在 Linux 系统上设置 MSTICPYCONFIG 环境变量。

1. 根据需要将 msticpyconfig.yaml 文件移动到计算实例。

1. 打开 Azure ML 终端，例如从 Azure Sentinel“笔记本”页打开。

1. 验证是否可以访问 msticpyconfig.yaml 文件。

   在 Azure ML 终端中，当前目录应为装载在计算 Linux 系统中的 Azure ML 文件存储主目录。 提示与以下示例类似：

   ```python
   azureuser@alicecontoso-azml7:~/cloudfiles/code/Users/alicecontoso$
   ```

   通过输入 `ls`，列出主目录中的所有文件，包括 msticpyconfig.yaml 文件。

1. 若要将 msticpyconfig.yaml 文件移动到计算文件存储，请输入：

   ```python
   mv msticpyconfig.yaml ~
   ```

1. 使用以下过程之一编辑环境变量的 .bashrc 文件：

    |命令  |步骤  |
    |---------|---------|
    |vim     |     1.运行：`vim ~/.bashrc` <br>2.按 SHIFT+G  >  End，转到文件末尾。 3. 输入“a”并按 Enter，创建新行。 <br>4.添加环境变量，然后按 ESC 返回到命令模式。 <br>5.输入 :wq，保存文件。    |
    |Nano     |           1.运行：`nano ~/.bashrc`<br>        1.按 ALT+/ 或 OPTION+/，转到文件末尾。<br>        1.添加环境变量，然后保存文件。 按 CTRL+X，然后按 Y。      |
    |     |         |

    添加以下环境变量之一：

    - 如果移动了 msticpyconfig.yaml 文件，请运行 `export MSTICPYCONFIG=~/msticpyconfig.yaml`。
    - 如果未移动 msticpyconfig.yaml 文件，请运行 `export MSTICPYCONFIG=~/cloudfiles/code/Users/<YOURNAME>/msticpyconfig.yaml`。


# <a name="azure-ml-options"></a>[Azure ML 选项](#tab/azure-ml)

如果需要将 msticpyconfig.yaml 文件存储在 Azure ML 用户文件夹以外的其他位置，请使用以下选项之一：

- 位于用户文件夹根目录的 nbuser_settings.py 文件。  虽然此过程比编辑 **kernel.json** 文件更简单且干扰性更低，但仅在开始运行笔记本代码时运行 `init_notebook` 函数，才能使用此过程。 虽然这是默认行为，但如果在未先运行 `init_notebook` 的情况下运行笔记本代码，则 MSTICPy 可能找不到配置文件。

    1. 在 Azure ML 终端中，在使用用户名的用户文件夹根目录下创建 nbuser_settings.py 文件。
    1. 在 nbuser_settings.py 文件中，添加以下行：
        ```python
          import os
          os.environ["MSTICPYCONFIG"] = "~/msticpyconfig.yaml"
        ```

    此文件由 `init_notebook` 函数自动导入，用于为当前笔记本设置 `MSTICPYCONFIG` 环境变量。

- Python 内核的 kernel.json 文件。 如果计划手动运行笔记本且在开始时可能不调用 `init_notebook` 函数，则使用此过程。

    提供 Python 3.6 和 Python 3.8 内核。 如果同时使用这两个内核，请编辑这两个文件。

    - Python 3.8 位置：/usr/local/share/jupyter/kernels/python38-azureml/kernel.json
    - Python 3.6 位置：/usr/local/share/jupyter/kernels/python3-azureml/kernel.json

    若要在 kernel.json 文件中设置环境变量：

    1. 创建 kernel.json 文件的副本，并在编辑器中打开原始文件。 可能需要使用 `sudo` 覆盖 kernel.json 文件，文件内容类似于以下示例：

         ```python
         {
             "argv": [
             "/anaconda/envs/azureml_py38/bin/python",
             "-m",
             "ipykernel_launcher",
             "-f",
             "{connection_file}"
             ],
             "display_name": "Python 3.8 - AzureML",
             "language": "python"
         }
         ```

    1. 在 `"language"` 项的后面添加以下此行：`"env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }`

        确保在 `"language": "python"` 行的末尾添加逗号。 例如：

         ```python
         {
             "argv": [
             "/anaconda/envs/azureml_py38/bin/python",
             "-m",
             "ipykernel_launcher",
             "-f",
             "{connection_file}"
             ],
             "display_name": "Python 3.8 - AzureML",
             "language": "python",
             "env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }
         }
         ```

---

> [!NOTE]
> 对于 Linux 和 Windows 选项，需要重启 Jupyter 服务器，使之选取定义的环境变量。
>

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

|使用者  |更多参考  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy 包配置](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy 设置编辑器](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [配置笔记本环境](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb)。<br>    - [MPSettingsEditor 笔记本](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb)。 <br><br>注意：Azure-Sentinel-Notebooks GitHub 存储库还包含 msticpyconfig.yaml 模板文件，其中包含注释掉的部分，可能会对你了解设置有所帮助。      |
|**Azure Sentinel 和 Jupyter Notebook**     |      - [Jupyter Notebook 简介](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy 文档](https://msticpy.readthedocs.io/)<br>    - [Azure Sentinel Notebook 文档](notebooks.md)<br>    - [Infosec Jupyter 书籍](https://infosecjupyterbook.com/introduction.html)<br>    - [Linux 主机资源管理器 Notebook 演练](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [为什么使用 Jupyter 进行安全调查](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [使用 Azure Sentinel 和 Notebook 进行安全调查](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas 文档](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Bokeh 文档](https://docs.bokeh.org/en/latest/)       |
|     |         |

