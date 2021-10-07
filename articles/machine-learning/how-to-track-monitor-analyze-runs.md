---
title: 跟踪、监视和分析运行
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 Python SDK 启动、监视和跟踪机器学习试验运行。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 04/19/2021
ms.topic: how-to
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 96f66914c22ddb107ab10ec49965e0f5ce8c6e4a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424343"
---
# <a name="start-monitor-and-track-run-history"></a>启动、监视和跟踪运行历史记录

[适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)、[机器学习 CLI](reference-azure-machine-learning-cli.md) 和 [Azure 机器学习工作室](https://ml.azure.com)提供多种方法用于监视、组织和跟踪训练运行与试验运行。 ML 运行历史记录是可解释和可重复的 ML 开发过程的重要部分。

本文演示如何完成以下任务：

* 监视运行性能。
* 添加运行显示名称。 
* 创建自定义视图。 
* 添加运行说明。 
* 标记和查找运行。
* 对运行历史记录运行搜索。 
* 取消运行或使其失败。
* 创建子运行。
* 通过电子邮件通知监视运行状态。
 

> [!TIP]
> 如果要了解如何监视 Azure 机器学习服务及关联的 Azure 服务，请参阅[如何监视 Azure 机器学习](monitor-azure-machine-learning.md)。
> 如果要了解如何监视部署为 Web 服务的模型，请参阅[收集模型数据](how-to-enable-data-collection.md)和[使用 Application Insights 进行监视](how-to-enable-app-insights.md)。

## <a name="prerequisites"></a>先决条件

需要准备好以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* 适用于 Python 的 Azure 机器学习 SDK（1.0.21 或更高版本）。 若要安装或更新到最新版本的 SDK，请参阅[安装或更新 SDK](/python/api/overview/azure/ml/install)。

    若要检查 Azure 机器学习 SDK 的版本，请使用以下代码：

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) 和 [Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。


## <a name="monitor-run-performance"></a>监视运行性能

* 启动运行及其日志记录过程

    # <a name="python"></a>[Python](#tab/python)
    
    1. 通过从 [azureml.core](/python/api/azureml-core/azureml.core) 包导入 [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace)、[Experiment](/python/api/azureml-core/azureml.core.experiment.experiment)、[Run](/python/api/azureml-core/azureml.core.run%28class%29) 和 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 类来设置试验。
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. 使用 [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) 方法启动运行及其日志记录过程。
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    若要启动试验的运行，请使用以下步骤：
    
    1. 在 shell 中或命令提示符下，使用 Azure CLI 对 Azure 订阅进行身份验证：
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. 将工作区配置附加到包含训练脚本的文件夹。 请将 `myworkspace` 替换为你的 Azure 机器学习工作区。 请将 `myresourcegroup` 替换为包含你的工作区的 Azure 资源组：
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        此命令创建包含示例 runconfig 和 conda 环境文件的 `.azureml` 子目录。 此子目录还包含用来与 Azure 机器学习工作区通信的 `config.json` 文件。
    
        有关详细信息，请参阅 [az ml folder attach](/cli/azure/ml(v1)/folder#az_ml_folder_attach)。
    
    2. 若要启动运行，请使用以下命令。 使用此命令时，请针对 -c 参数指定 runconfig 文件的名称（如果查看的是文件系统，此名称为 \*.runconfig 前面的文本）。
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach` 命令创建了一个 `.azureml` 子目录，其中包含两个示例 runconfig 文件。
        >
        > 如果你的某个 Python 脚本以编程方式创建运行配置对象，则你可以使用 [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) 将此对象另存为 runconfig 文件。
        >
        > 有关更多示例 runconfig 文件，请参阅 [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/)。
    
        有关详细信息，请参阅 [az ml run submit-script](/cli/azure/ml(v1)/run#az_ml_run_submit-script)。

    # <a name="studio"></a>[工作室](#tab/azure-studio)

    有关在 Azure 机器学习设计器中训练模型的示例，请参阅[教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)。

    ---

* 监视运行的状态

    # <a name="python"></a>[Python](#tab/python)
    
    * 使用 [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) 方法获取运行的状态。
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * 若要获取运行 ID、执行时间和有关运行的其他详细信息，请使用 [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) 方法。
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * 成功完成运行后，使用 [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) 方法将其标记为已完成。
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * 如果使用 Python 的 `with...as` 设计模式，则当运行超出范围时，该运行会自动将自身标记为已完成。 无需手动将它标记为已完成。
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * 若要查看试验的运行列表，请使用以下命令。 请将 `experiment` 替换为你的试验名称。
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        此命令返回一个 JSON 文档，其中列出了有关此试验的运行的信息。
    
        有关详细信息，请参阅 [az ml experiment list](/cli/azure/ml(v1)/experiment#az_ml_experiment_list)。
    
    * 若要查看有关特定运行的信息，请使用以下命令。 请将 `runid` 替换为运行的 ID：
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        此命令返回一个 JSON 文档，其中列出了有关运行的信息。
    
        有关详细信息，请参阅 [az ml run show](/cli/azure/ml(v1)/run#az_ml_run_show)。
    
    
    # <a name="studio"></a>[工作室](#tab/azure-studio)
    
    ---    
   
## <a name="run-display-name"></a>运行显示名称 
运行显示名称是可为运行提供的可自定义可选名称。 若要编辑运行显示名称，请执行以下操作：

1. 导航到运行列表。 

2. 选择运行以在运行详细信息页面中编辑显示名称。

3. 选择“编辑”按钮以编辑运行显示名称。 

:::image type="content" source="media/how-to-track-monitor-analyze-runs/display-name.gif" alt-text="屏幕截图：编辑显示名称":::

## <a name="custom-view"></a>自定义视图 
    
若要查看工作室中的运行，请执行以下操作： 
    
1. 导航到“试验”选项卡。
    
1. 选择“所有试验”以查看试验中的所有运行，或选择“所有运行”以查看工作区中已提交的所有运行 。
    
在“所有运行”页面中，可以按标记、试验、计算目标等筛选运行列表，以便更好地组织和确定工作范围。  
    
1. 通过选择要比较的运行、添加图表或应用筛选来对页面进行自定义。 这些更改可以保存为“自定义视图”，以便你轻松返回到你的工作内容。 具有工作区权限的用户可以编辑或查看自定义视图。 此外，还可以通过选择“共享视图”，与团队成员共享自定义视图以增强协作。   

1. 要查看运行日志，请选择特定的运行，在“输出 + 日志”选项卡中，可以找到运行的诊断和错误日志。

:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views-2.gif" alt-text="屏幕截图：创建自定义视图":::
    

## <a name="run-description"></a>运行说明 

可以将运行说明添加到运行中，以便为运行提供更多上下文和信息。 还可以从运行列表中搜索这些说明，并将运行说明作为列添加到运行列表中。 

导航到运行的“运行详细信息”页，然后选择“编辑”或“铅笔”图标以添加、编辑或删除运行说明。 要保留对运行列表的更改，请将所做的更改保存到现有的自定义视图或新的自定义视图。 允许对图像进行嵌入和深层链接的运行说明支持 Markdown 格式，如下所示。

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description-2.gif" alt-text="屏幕截图：创建运行说明"::: 

## <a name="tag-and-find-runs"></a>标记和查找运行

在 Azure 机器学习中，可以使用属性与标记来帮助组织运行，以及查询运行以获取重要信息。

* 添加属性和标记

    # <a name="python"></a>[Python](#tab/python)
    
    若要将可搜索的元数据添加到运行，请使用 [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) 方法。 例如，以下代码将 `"author"` 属性添加到运行：
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    属性是不可变的，因此它们将创建一条永久记录用于审核目的。 以下代码示例会导致出错，因为我们已在前面的代码中添加了 `"azureml-user"` 作为 `"author"` 属性值：
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    与属性不同，标记是可变的。 若要为试验的使用者添加可搜索且有意义的信息，请使用 [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) 方法。
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    还可以添加简单的字符串标记。 当这些标记作为键出现在标记字典中时，它们的值为 `None`。
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > 使用 CLI 只能添加或更新标记。
    
    若要添加或更新标记，请使用以下命令：
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    有关详细信息，请参阅 [az ml run update](/cli/azure/ml(v1)/run#az_ml_run_update)。
    
    # <a name="studio"></a>[工作室](#tab/azure-studio)
    
    可以添加、编辑或删除工作室中的运行标记。 导航到运行的“运行详细信息”页，然后选择“编辑”或“铅笔”图标以添加、编辑或删除运行标记。 还可以从运行列表页面搜索和筛选这些标记。
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="屏幕截图：添加、编辑或删除运行标记":::
    
    ---

* 查询属性和标记

    可以查询试验中的运行，以返回与特定属性和标记匹配的运行列表。

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI 支持 [JMESPath](http://jmespath.org) 查询，可以使用这些查询基于属性和标记来筛选运行。 若要在 Azure CLI 中使用 JMESPath 查询，请使用 `--query` 参数指定该查询。 以下示例演示了一些使用属性和标记的查询：
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    有关查询 Azure CLI 结果的详细信息，请参阅[查询 Azure CLI 命令输出](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)。
    
    # <a name="studio"></a>[工作室](#tab/azure-studio)
    
    若要搜索特定运行，请导航到“所有运行”列表。 可以使用以下两个选项：
    
    1. 使用“添加筛选器”按钮，选择标记上的筛选器按分配给运行的标记筛选运行。 <br><br>
    OR
    
    1. 使用搜索栏通过搜索运行的元数据（如标记、说明、试验名称和提交者名称）快速查找运行。 
    
## <a name="cancel-or-fail-runs"></a>取消运行或使其失败

如果发现错误，或者完成运行花费的时间太长，可以取消该运行。

# <a name="python"></a>[Python](#tab/python)

若要使用 SDK 取消运行，请使用 [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) 方法：

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

如果运行已完成但包含错误（例如，使用了错误的训练脚本），可以使用 [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) 方法将其标记为失败。

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 CLI 取消运行，请使用以下命令。 请将 `runid` 替换为运行的 ID

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

有关详细信息，请参阅 [az ml run cancel](/cli/azure/ml(v1)/run#az_ml_run_cancel)。

# <a name="studio"></a>[工作室](#tab/azure-studio)

若要在工作室中取消某个运行，请执行以下步骤：

1. 转到“试验”或“管道”部分中正在运行的管道。 

1. 选择要取消的管道运行编号。

1. 在工具栏中，选择“取消”。

---

## <a name="create-child-runs"></a>创建子运行

创建子运行可将相关的运行组合到一起，例如，以完成不同的超参数优化迭代。

> [!NOTE]
> 只能使用 SDK 创建子运行。

此代码示例使用 `hello_with_children.py` 脚本，通过 [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) 方法从已提交的运行内部创建包含五个子运行的批：

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 当子运行超出范围时，会自动标记为已完成。

若要高效地创建许多子运行，请使用 [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) 方法。 由于每次创建操作都会造成网络调用，因此，创建一批运行比逐个创建更为高效。

### <a name="submit-child-runs"></a>提交子运行

也可以从父运行提交子运行。 通过此操作可创建父运行和子运行的层次结构。 你无法创建没有父运行的子运行：即使父运行只启动子运行而不执行任何操作，仍需要创建层次结构。 所有运行的状态都是独立的：即使一个或多个子运行已取消或失败，父运行也可以处于 `"Completed"` 成功状态。  

你可能会希望子运行使用与父运行不同的运行配置。 例如，对父运行使用常规的基于 CPU 的配置，而对子运行使用基于 GPU 的配置。 另一种常见的需求是向每个子运行传递不同的参数和数据。 若要自定义子运行，请为该子运行创建一个 `ScriptRunConfig` 对象。 

> [!IMPORTANT]
> 若要从远程计算上的父运行提交子运行，必须先登录到父运行代码中的工作区。 默认情况下，远程运行中的运行上下文对象没有用于提交子运行的凭据。 使用服务主体或托管标识凭据进行登录。 有关身份验证的详细信息，请参阅[设置身份验证](how-to-setup-authentication.md)。

以下代码：

- 从工作区 `ws` 中检索名为 `"gpu-cluster"` 的计算资源
- 循环访问要传递给子 `ScriptRunConfig` 对象的不同参数值
- 使用自定义计算资源和参数创建并提交新的子运行
- 阻止至所有子运行完成为止

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

若要高效创建多个具有相同配置、参数和输入内容的子运行，可使用 [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) 方法。 由于每次创建操作都会造成网络调用，因此，创建一批运行比逐个创建更为高效。

在子运行内部，可以查看父运行 ID：

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>查询子运行

若要查询特定父级的子运行，请使用 [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) 方法。 使用 ``recursive = True`` 参数可以查询子级和孙级的嵌套树。

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>记录到父运行或根运行

你可以使用 `Run.parent` 字段访问启动当前子运行的运行。 使用 `Run.parent` 的一个常见用例是将日志结果合并到一个位置。 子运行以异步方式执行，而且只能保证父运行等待其子运行完成，无法保证它们顺序一致且保持同步。

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>通过电子邮件通知监视运行状态

1. 在 [Azure 门户](https://ms.portal.azure.com/)的左侧导航栏中，选择“监视”选项卡。 

1. 选择“诊断设置”，然后选择“+ 添加诊断设置。

    ![电子邮件通知诊断设置屏幕截图](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. 在“诊断设置”的 
    1. “类别详细信息”下，选择“AmlRunStatusChangedEvent”。 
    1. 在“目标详细信息”中，选择“发送到 Log Analytics 工作区”，并指定“订阅”和“Log Analytics 工作区”。 

    > [!NOTE]
    > Azure Log Analytics 工作区是一种不同于 Azure 机器学习服务工作区的 Azure 资源类型。 如果该列表中没有选项，则可以[创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 
    
    ![保存电子邮件通知的位置](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. 在“日志”选项卡中，添加“新的警报规则”。 

    ![新建警报规则](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. 请参阅[如何使用 Azure Monitor 创建和管理日志警报](../azure-monitor/alerts/alerts-log.md)。

## <a name="example-notebooks"></a>示例笔记本

以下笔记本演示了本文中的概念：

* 若要详细了解日志记录 API，请参阅[日志记录 API 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)。

* 有关使用 Azure 机器学习 SDK 管理运行的详细信息，请参阅[管理运行笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何记录试验的指标，请参阅[在训练运行期间记录指标](how-to-log-view-metrics.md)。
* 若要了解如何监视 Azure 机器学习中的资源和日志，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。
