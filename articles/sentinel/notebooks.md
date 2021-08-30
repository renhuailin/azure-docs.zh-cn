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
ms.openlocfilehash: 7b1202648db3aed73ea040fb60610879a6816dd9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737568"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>使用 Jupyter Notebook 搜寻安全威胁

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel 的基础是数据存储；它结合了高性能查询和动态架构并可扩展到大规模数据卷。 Azure 门户和所有 Azure Sentinel 工具都使用一个公共 API 来访问此数据存储。 同一 API 也可用于 [Jupyter](https://jupyter.org/) 笔记本和 Python 等外部工具。 虽然可以在门户中执行许多常见任务，但 Jupyter 扩展了可对此数据执行的操作范围。 它将完整的可编程性与用于机器学习、可视化效果和数据分析的大量库集合组合在一起。 这些属性使 Jupyter 成为安全调查和搜寻的引人注目的工具。

例如，使用笔记本可以进行以下操作：

- 执行非内置于 Azure Sentinel 的分析（例如，一些 Python 机器学习功能）
- 创建非内置于 Azure Sentinel 的数据可视化效果（例如，自定义时间线和进程树）
- 集成 Azure Sentinel 外部的数据源（例如，本地数据集）。

我们已将 Jupyter 体验集成到 Azure 门户中，使你可以轻松地创建和运行笔记本来分析数据。 Kqlmagic 库提供了一种连接，使你可以从 Azure Sentinel 获取查询并直接在笔记本中运行它们。 查询使用 [Kusto 查询语言](https://kusto.azurewebsites.net/docs/kusto/query/index.html)。 Microsoft 的某些安全分析师开发的几个笔记本打包在 Azure Sentinel 中。 其中一些笔记本专门用于特定方案，可以按原样使用。 其他笔记本用作示例，以说明可以复制或修改以便在自己的笔记本中使用的技术和功能。 其他笔记本还可从 Azure Sentinel GitHub 社区导入。

集成的 Jupyter 体验使用 [Azure Notebooks](https://notebooks.azure.com/) 存储、共享和执行笔记本。 如果你的计算机上或其他 JupyterHub 环境（例如，Azure Databricks）有 Python 环境和 Jupyter，也可以以本地方式运行这些笔记本。


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

我们还在 [msticpy](https://github.com/Microsoft/msticpy/) 包中发布了一些开放源代码 Jupyter 安全工具。 此包可用于许多随附的笔记本。 Msticpy 工具专为帮助创建用于搜寻和调查的笔记本而设计，我们正在积极地致力于提供新功能和改进。 有关详细信息，请参阅 [MSTIC Jupyter 和 Python 安全工具文档](https://msticpy.readthedocs.io/)。

[Azure Sentinel GitHub 社区存储库](https://github.com/Azure/Azure-Sentinel)是存储任何未来的 Azure Sentinel 笔记本的位置，这些笔记本由 Microsoft 生成或由社区提供。


## <a name="manage-access-to-azure-sentinel-notebooks"></a>管理对 Azure Sentinel 笔记本的访问

若要使用笔记本，首先必须具有适当的权限，具体取决于用户角色。

在 [Azure 机器学习](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML) 平台上运行 Azure Sentinel 笔记本时，必须对 Azure Sentinel 工作区和 [Azure ML 工作区](../machine-learning/concept-workspace.md)均具有适当的访问权限。

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

**启动笔记本**：

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

1. 创建并启动笔记本服务器后，可以开始运行笔记本单元格。 在每个单元格中，选择“运行”图标以运行笔记本代码。

    有关详细信息，请参阅[命令模式快捷方式](../machine-learning/how-to-run-jupyter-notebooks.md)

1. 如果需要重启笔记本内核并从头重新运行笔记本单元格，请选择“内核操作” > “重启内核” 。 例如：

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="重启笔记本内核。":::

## <a name="troubleshooting"></a>故障排除

如果笔记本遇到问题，请参阅 [Azure 机器学习故障排除](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting)。


## <a name="next-steps"></a>后续步骤

- 通过浏览一些笔记本模板（例如 [Azure Log Analytics 上的凭据扫描](https://www.youtube.com/watch?v=OWjXee8o04M)和“引导式调查 - 处理警报”）详细了解在威胁搜寻和调查中如何使用笔记本 。 

    在 Azure Sentinel >“笔记本” > “模板”选项卡中找到笔记本模板 。

- 有关详细信息，请参阅 [Azure Sentinel Github 存储库](https://github.com/Azure/Azure-Sentinel-Notebooks)：

    - [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) 目录中保存了示例笔记本以及可用于显示预期输出的数据。

    - [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) 目录包含描述设置默认 Python 版本、从笔记本创建 Azure Sentinel 书签等概念的笔记本。

    有关详细信息，请参阅 [Azure ML 笔记本和 Azure Sentinel 入门](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb)指南。

> [!NOTE]
> 在 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel-Notebooks)中共享的笔记本旨在用作开发自己的笔记本时可以使用的有用工具、插图和代码示例。
>
> 欢迎提供反馈、建议、功能请求、笔记本、bug 报告或对现有笔记本的改进和补充。 请前往 [Azure Sentinel GitHub 社区](https://github.com/Azure/Azure-Sentinel)创建问题或分支并上传贡献。
>

有关详细信息，请参阅：

- [网络研讨会：Azure Sentinel 笔记本基础](https://www.youtube.com/watch?v=rewdNeX6H94)
- [教程：Azure Sentinel 笔记本 - 入门](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)
- [主动搜寻威胁](hunting.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
- [Jupyter、msticpy 和 Microsoft Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
- [教程：在不离开 Azure ML 工作室的情况下编辑和运行 Jupyter 笔记本](https://www.youtube.com/watch?v=AAj-Fz0uCNk)
