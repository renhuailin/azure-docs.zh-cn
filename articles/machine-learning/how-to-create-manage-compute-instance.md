---
title: 创建和管理计算实例
titleSuffix: Azure Machine Learning
description: 了解如何创建和管理 Azure 机器学习计算实例。 用作开发环境，或用作其用途为开发/测试的计算目标。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli, references_regions
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 09/22/2021
ms.openlocfilehash: 4897b557626be5071a21d2cc1a6a8194eaed8994
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154273"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>创建和管理 Azure 机器学习计算实例

了解如何在 Azure 机器学习工作区中创建和管理[计算实例](concept-compute-instance.md)。

可将计算实例用作云中的完全配置、完全托管的开发环境。 对于开发和测试，还可将该实例用作[训练计算目标](concept-compute-target.md#train)或用于[推理目标](concept-compute-target.md#deploy)。   计算实例可以并行运行多个作业，它有一个作业队列。 作为开发环境，不能与工作区中的其他用户共享计算实例。

在本文中，学习如何：

* [创建](#create)计算实例
* [管理](#manage)（启动、停止、重启、删除）计算实例
* [创建计划](#schedule)以自动启动和停止计算实例（预览）
* [使用安装脚本](#setup-script)自定义和配置计算实例

计算实例可以在[虚拟网络环境](how-to-secure-training-vnet.md)中安全地运行作业，无需企业打开 SSH 端口。 作业在容器化环境中执行，并将模型依赖项打包到 Docker 容器中。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 或 [Azure 机器学习 Visual Studio Code 扩展](how-to-setup-vs-code.md)。

## <a name="create"></a>创建

> [!IMPORTANT]
> 下面标记了“（预览版）”的项当前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

**时间估计**：大约 5 分钟。

对于工作区而言，创建计算实例是一次性过程。 可将此计算重复用作开发工作站，或者用作训练的计算目标。 可将多个计算实例附加到工作区。 

对于每个区域每个虚拟机 (VM) 系列配额和创建计算实例时应用的区域总配额，专用内核数一致，且该数量与 Azure 机器学习训练计算群集配额共享。 停止计算实例不会释放配额，因此无法确保你能够重启计算实例。 创建计算实例后，不能更改其虚拟机大小。

<a name="create-instance"></a> 以下示例演示如何创建计算实例：

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [ComputeInstance 类](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

有关详细信息，请查看 [az ml computetarget create computeinstance](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_computeinstance) 参考文档。

# <a name="studio"></a>[工作室](#tab/azure-studio)

1. 导航到 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在“管理”下，选择“计算” 。
1. 在顶部选择“计算实例”。
1. 如果没有计算实例，请在页面中间选择“创建”。
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="创建计算目标":::

1. 如果看到计算资源的列表，请选择列表上方的“+ 新建”。

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="选择“新建”":::
1. 填写表单：

    |字段  |说明  |
    |---------|---------|
    |计算名称     |  <ul><li>名称是必须提供的，且长度必须介于 3 到 24 个字符之间。</li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li></ul>     |
    |虚拟机类型 |  选择“CPU”或“GPU”。 此类型在创建后无法更改     |
    |虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |

1. 除非要为计算实例配置高级设置，否则请选择“创建”。
1. <a name="advanced-settings"></a> 如果要执行以下操作，请选择“下一步: 高级设置”：

    * 启用 SSH 访问。  按照下面[详细的 SSH 访问说明](#enable-ssh)操作。
    * 启用虚拟网络。 指定 **资源组**、**虚拟网络** 和 **子网**，以在 Azure 虚拟网络 (vnet) 中创建计算实例。 还可以选择“无公共 IP”（预览）以防止创建了需要专用链接工作区的公共 IP 地址。 还必须满足虚拟网络设置的这些[网络要求](./how-to-secure-training-vnet.md)。 
    * 将计算机分配给其他用户。 有关分配给其他用户的详细信息，请参阅[代表他人创建](#on-behalf)。
    * 使用安装脚本进行预配（预览）- 若要详细了解如何创建和使用安装脚本，请参阅[使用脚本自定义计算实例](#setup-script)。
    * 添加计划（预览）。 计划计算实例自动启动和/或关闭的时间。 请参阅下面的[计划详细信息](#schedule)。


---

还可使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)创建计算实例。

## <a name="enable-ssh-access"></a><a name="enable-ssh"></a> 启用 SSH 访问

默认情况下会禁用 SSH 访问。  SSH 访问在创建后便不可更改。 如果计划使用 [VS Code Remote](how-to-set-up-vs-code-remote.md) 以交互式方式进行调试，请确保启用访问权限。  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

创建并运行计算实例后，请参阅[使用 SSH 访问进行连接](how-to-create-attach-compute-studio.md#ssh-access)。

## <a name="create-on-behalf-of-preview"></a><a name="on-behalf"></a> 代表他人创建（预览版）

作为管理员，你可代表数据科学家创建计算实例，并通过以下方式将实例分配给他们：

* 工作室，通过使用[高级设置](?tabs=azure-studio#advanced-settings)

* [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)。  若要详细了解如何查找此模板中所需的 TenantID 和 ObjectID，请参阅[查找身份验证配置的标识对象 ID](../healthcare-apis/azure-api-for-fhir/find-identity-object-ids.md)。  也可在 Azure Active Directory 门户中找到这些值。

* REST API

你为其创建计算实例的数据科学家需要拥有针对以下项的 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 权限：

* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

数据科学家可启动、停止和重启计算实例。 他们可将计算实例用于：
* Jupyter
* JupyterLab
* RStudio
* 集成式笔记本

## <a name="schedule-automatic-start-and-stop-preview"></a><a name="schedule"></a> 计划自动启动和停止（预览）

为自动关闭和自动启动定义多个计划。 例如，创建两个计划，一个从周一至周四上午 9 点开始，下午 6 点停止，另一个从周五上午 9 点开始，下午 4 点停止。  每个计算实例总共可以创建四个计划。

还可以为[代表他人创建](#on-behalf)计算实例定义计划。 可以创建计划以创建状态为“已停止”的计算实例。 当用户代表其他用户创建计算实例时，这特别有用。

### <a name="create-a-schedule-in-studio"></a>在工作室中创建计划

1. [填写表单](?tabs=azure-studio#create-instance)。
1. 在表单的第二页上，打开“显示高级设置”。
1. 选择“添加计划”以添加新计划。

    :::image type="content" source="media/how-to-create-attach-studio/create-schedule.png" alt-text="屏幕截图：在“高级设置”中添加计划。":::

1. 选择“启动计算实例”或“停止计算实例” 。
1. 选择“时区”。
1. 选择“启动时间”或“关闭时间” 。
1. 选择此计划处于活动状态的天数。

    :::image type="content" source="media/how-to-create-attach-studio/stop-compute-schedule.png" alt-text="屏幕截图：计划要关闭的计算实例。":::

1. 如果要创建其他计划，请再次选择“添加计划”。

创建计算实例后，可以从计算实例详细信息部分查看、编辑或添加新计划。
请注意，时区标签不考虑夏令时。 例如，(UTC+01:00) 阿姆斯特丹、柏林、伯尔尼、罗马、斯德哥尔摩、维也纳在夏令时期间实际上是 UTC+02:00。

### <a name="create-a-schedule-with-a-resource-manager-template"></a>使用资源管理器模板创建计划

可以使用资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)来计划计算实例的自动启动和停止。

在资源管理器模板中，添加：

```
"schedules": "[parameters('schedules')]"
```

然后，使用 cron 或 LogicApps 表达式在参数文件中定义启动或停止实例的计划：
 
```json
        "schedules": {
        "value": {
        "computeStartStop": [
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 18 * * *"
            },
            "action": "Stop",
            "status": "Enabled"
          },
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 8 * * *"
            },
            "action": "Start",
            "status": "Enabled"
          },
          { 
            "triggerType": "Recurrence", 
            "recurrence": { 
              "frequency": "Day", 
              "interval": 1, 
              "timeZone": "UTC", 
              "schedule": { 
                "hours": [17], 
                "minutes": [0]
              } 
            }, 
            "action": "Stop", 
            "status": "Enabled" 
          } 
        ]
      }
    }
```

* “action”的值可以是“Start”或“Stop”。
* 对于触发类型 `Recurrence`，使用与逻辑应用相同的语法，及此[定期架构](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。
* 对于触发类型 `cron`，使用标准 cron 语法：  

    ```cron
    // Crontab expression format: 
    // 
    // * * * * * 
    // - - - - - 
    // | | | | | 
    // | | | | +----- day of week (0 - 6) (Sunday=0) 
    // | | | +------- month (1 - 12) 
    // | | +--------- day of month (1 - 31) 
    // | +----------- hour (0 - 23) 
    // +------------- min (0 - 59) 
    // 
    // Star (*) in the value field above means all legal values as in 
    // braces for that column. The value column can have a * or a list 
    // of elements separated by commas. An element is either a number in 
    // the ranges shown above or two numbers in the range separated by a 
    // hyphen (meaning an inclusive range). 
    ```
### <a name="azure-policy-support-to-default-a-schedule"></a>Azure Policy 支持将某个计划设为默认计划
使用 Azure Policy 为订阅中的每个计算实例强制执行已存在的关闭计划；如果不存在任何计划，则强制执行默认设置的某个计划。
下面是一个示例策略，可将一个在太平洋时间晚上 10:10 进行的关闭计划设为默认计划。
```json
{
    "mode": "All",
    "policyRule": {
     "if": {
      "allOf": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/computeType",
        "equals": "ComputeInstance"
       },
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "exists": "false"
       }
      ]
     },
     "then": {
      "effect": "append",
      "details": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "value": {
         "computeStartStop": [
          {
           "triggerType": "Cron",
           "cron": {
            "startTime": "2021-03-10T21:21:07",
            "timeZone": "Pacific Standard Time",
            "expression": "0 22 * * *"
           },
           "action": "Stop",
           "status": "Enabled"
          }
         ]
        }
       }
      ]
     }
    }
}    
```

## <a name="customize-the-compute-instance-with-a-script-preview"></a><a name="setup-script"></a> 使用脚本自定义计算实例（预览版）

使用安装脚本实现自动方式，以便在预配时自定义并配置计算实例。 作为管理员，你可以编写一个自定义脚本，用于按照要求预配工作区中的所有计算实例。

可以在安装脚本中执行的操作的一些示例：

* 安装包、工具和软件
* 装载数据
* 创建自定义 conda 环境和 Jupyter 内核
* 克隆 Git 存储库并设置 Git 配置
* 设置网络代理
* 设置环境变量。
* 安装 JupyterLab 扩展

### <a name="create-the-setup-script"></a>创建安装脚本

安装脚本是以 rootuser 身份运行的 shell 脚本。  创建脚本或将其上传到笔记本文件：

1. 登录到[工作室](https://ml.azure.com)并选择你的工作区。
2. 在左侧选择“笔记本”
3. 使用“添加文件”工具创建或上传安装 shell 脚本。  请确保脚本文件名以“sh”结尾。  创建新文件时，还需要将“文件类型”更改为“bash(.sh)”。

:::image type="content" source="media/how-to-create-manage-compute-instance/create-or-upload-file.png" alt-text="在工作室中创建安装脚本或将其上传到笔记本文件":::

脚本运行时，脚本的当前工作目录是它上传到的目录。 例如，如果脚本上传到了 Users>admin，则脚本运行时，该脚本在计算实例和当前工作目录中的位置为 /home/azureuser/cloudfiles/code/Users/admin。这样，你便可以在脚本中使用相对路径。

脚本参数可以在脚本中引用为 $1、$2 等。

如果脚本执行了特定于 azureuser 的某项操作（例如安装 Conda 环境或 Jupyter 内核），则必须将它放入 sudo -u azureuser 块中，如下所示

:::code language="bash" source="~/azureml-examples-main/setup-ci/install-pip-package.sh":::

命令 sudo -u azureuser 将当前工作目录更改为 /home/azureuser 。 此外，无法访问此块中的脚本参数。

有关其他示例脚本，请参阅 [azureml-examples](https://github.com/Azure/azureml-examples/tree/main/setup-ci)。

也可以在脚本中使用以下环境变量：

1. CI_RESOURCE_GROUP
2. CI_WORKSPACE
3. CI_NAME
4. CI_LOCAL_UBUNTU_USER. 这会指向 azureuser

可以将安装脚本与 Azure Policy 结合使用，以在每次创建计算实例时强制执行或不执行安装脚本。 安装脚本超时的默认值为 15 分钟。 这可以通过 Studio UI 或通过 ARM 模板使用 DURATION 参数进行更改。
DURATION 是一个带有可选后缀的浮点数：“s”代表秒（默认设置），“m”代表分钟，“h”代表小时，“d”代表天。

### <a name="use-the-script-in-the-studio"></a>在工作室中使用脚本

存储脚本后，在计算实例的创建过程中指定该脚本：

1. 登录到[工作室](https://ml.azure.com/)并选择你的工作区。
1. 在左侧选择“计算”。
1. 选择“+ 新建”以创建新的计算实例。
1. [填写表单](?tabs=azure-studio#create-instance)。
1. 在表单的第二页上，打开“显示高级设置”。
1. 启用“使用安装脚本进行预配”。
1. 浏览到所保存的 shell 脚本。  或从计算机上传脚本。
1. 根据需要添加命令参数。

:::image type="content" source="media/how-to-create-manage-compute-instance/setup-script.png" alt-text="在工作室中使用安装脚本预配计算实例。":::

如果工作区存储已附加到虚拟网络，则除非从虚拟网络内部访问工作室，否则你可能无法访问安装脚本文件。

### <a name="use-script-in-a-resource-manager-template"></a>在资源管理器模板中使用脚本

在资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)中，添加 `setupScripts` 以在预配计算实例时调用安装脚本。 例如：

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"workspaceStorage",
        "scriptData":"[parameters('creationScript.location')]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```
上面的 scriptData 指定了笔记本文件共享中创建脚本的位置，例如 Users/admin/testscript.sh。scriptArguments 是可选的，用于指定创建脚本的参数  。

可以改为为资源管理器模板提供内联脚本。  Shell 命令可以引用上传到笔记本文件共享中的任何依赖项。  使用内联字符串时，脚本的工作目录为 /mnt/batch/tasks/shared/LS_root/mounts/clusters/ciname/code/Users。

例如，为 `scriptData` 指定 base64 编码的命令字符串：

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"inline",
        "scriptData":"[base64(parameters('inlineCommand'))]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```

### <a name="setup-script-logs"></a>安装脚本日志

安装脚本执行的日志显示在计算实例详细信息页的日志文件夹中。 日志将存储回 Logs\<compute instance name> 文件夹下的笔记本文件共享中。 特定计算实例的脚本文件和命令参数显示在详细信息页中。


## <a name="manage"></a>管理

启动、停止、重启和删除计算实例。 计算实例不会自动纵向缩减，因此请确保停止该资源以免产生费用。 停止计算实例会将其解除分配。 然后在需要时重启。 虽然停止计算实例将停止计算时间的计费，但仍会对磁盘、公共 IP 和标准负载均衡器计费。 

你可以为计算实例[创建计划](#schedule)，以基于时间和星期几自动启动和停止。

> [!TIP]
> 计算实例具有 120GB 的 OS 磁盘。 如果磁盘空间不足，则在停止或重启计算实例之前，[使用终端](how-to-access-terminal.md)可至少清空 1-2 GB 空间。 请勿通过从终端分发 sudo shutdown 来停止计算实例。

# <a name="python"></a>[Python](#tab/python)

在下例中，计算实例的名称均为“实例”

* 获取状态

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* 停止

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* 开始

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* 重启

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Delete

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在下例中，计算实例的名称均为“实例”

* 停止

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget stop computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)。

* 开始

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget start computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)。

* 重启

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget restart computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)。

* Delete

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    有关详细信息，请参阅 [az ml computetarget delete computeinstance](/cli/azure/ml(v1)/computetarget#az_ml_computetarget_delete)。

# <a name="studio"></a>[工作室](#tab/azure-studio)
<a name="schedule"></a>

在 Azure 机器学习工作室中的工作区内选择“计算”，然后在顶部选择“计算实例”。 

![管理计算实例](./media/concept-compute-instance/manage-compute-instance.png)

可执行以下操作：

* 新建计算实例
* 刷新“计算实例”选项卡。
* 启动、停止和重启计算实例。  只要实例在运行，你就需要为其付费。 不使用计算实例时，请将其停止，以便降低成本。 停止计算实例会将其解除分配。 然后在需要时重启。 还可以计划计算实例的启动和停止时间。
* 删除计算实例。
* 筛选计算实例列表，以仅显示已创建的实例。

对于你创建的（或为你创建的）工作区中的每个计算实例，你可以：

* 访问计算实例上的 Jupyter、JupyterLab、RStudio。
* 通过 SSH 连接到计算实例。 默认已禁用 SSH 访问，但可以在创建计算实例时启用。 SSH 访问是通过公钥/私钥机制实现的。 选项卡中将提供 IP 地址、用户名和端口号等 SSH 连接详细信息。 在虚拟网络部署中，禁用 SSH 会阻止从公共 Internet 进行 SSH 访问，仍可使用计算实例节点的专用 IP 地址和端口 22 从虚拟网络内部进行 SSH 访问。
* 选择计算名称以：
    * 查看有关特定计算实例的详细信息，如 IP 地址和区域。
    * 创建或修改用于启动和停止计算实例的计划（预览）。  向下滚动到页面底部以编辑计划。

---

使用 [Azure RBAC](../role-based-access-control/overview.md) 可以对工作区中的哪些用户能够创建、删除、启动、停止、重启计算实例进行控制。 充当工作区参与者和所有者角色的所有用户可以在整个工作区中创建、删除、启动、停止和重启计算实例。 但是，只有特定计算实例的创建者或分配的用户（如果该计算实例是以其名义创建的）可在该计算实例上访问 Jupyter、JupyterLab 和 RStudio。 计算实例专用于具有 root 用户访问权限的单个用户，并且可通过 Jupyter/JupyterLab/RStudio 进行终端访问。 计算实例将具有单用户登录名，所有操作都将使用该用户的标识进行试验运行的 Azure RBAC 控制和权限划分。 SSH 访问是通过公钥/私钥机制控制的。

可以通过 Azure RBAC 来控制这些操作：
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

若要创建计算实例，需要以下操作的权限：
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


## <a name="next-steps"></a>后续步骤

* [访问计算实例终端](how-to-access-terminal.md)
* [创建和管理文件](how-to-manage-files.md)
* [提交训练运行](how-to-set-up-training-targets.md)
