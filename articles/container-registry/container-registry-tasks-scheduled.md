---
title: 教程 - 计划 ACR 任务
description: 本教程介绍如何通过设置一个或多个计时器触发器按定义的计划运行 Azure 容器注册表任务
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030605"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>教程：按定义的计划运行 ACR 任务

本教程介绍如何按计划运行 [ACR 任务](container-registry-tasks-overview.md)。 通过设置一个或多个计时器触发器来计划任务。  计时器触发器可以单独使用，也可以与其他任务触发器结合使用。

本教程介绍如何计划任务及完成以下任务：

> [!div class="checklist"]
> * 创建具有计时器触发器的任务
> * 管理计时器触发器

任务计划适用于如下所述的方案：

* 运行容器工作负荷来执行计划性维护操作。 例如，运行容器化应用以从注册表中删除不需要的映像。
* 在工作日针对要在现场监视的生产映像运行一组测试。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>关于计划任务

* **使用 cron 表达式的触发器** - 任务的计时器触发器使用 cron 表达式。  该表达式是包含五个字段的字符串，这些字段指定要触发任务的分钟、小时、日期、月份和星期。 支持的最高频率为每分钟一次。

  例如，表达式 `"0 12 * * Mon-Fri"` 在每个工作日的中午（UTC 时间）触发任务。 请参阅本文稍后所述的[详细信息](#cron-expressions)。
* **多个计时器触发器** - 允许将多个计时器添加到g wh 任务，前提是计划不同。
    * 可在创建任务时指定多个计时器触发器，也可以在以后添加。
    * （可选）为触发器命名以便于管理，否则 ACR 任务将提供默认的触发器名称。
    * 如果多个计时器计划在某个时间发生重叠，ACR 任务将在每个计时器的计划时间触发任务。
* **其他任务触发器** - 在计时器触发的任务中，还可以基于 [源代码提交](container-registry-tutorial-build-task.md)或 [基础映像更新](container-registry-tutorial-base-image-update.md)启用触发器。 与其他 ACR 任务一样，你也可以 [手动运行][az-acr-task-run] 计划的任务。

## <a name="create-a-task-with-a-timer-trigger"></a>创建具有计时器触发器的任务

### <a name="task-command"></a>任务命令

首先，使用适用于你的环境的值填充以下 shell 环境变量。 此步骤并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果未填充环境变量，则必须在示例命令中出现的任何位置手动替换每个值。

[![嵌入式启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

使用 [az acr task create][az-acr-task-create] 命令创建任务时，可以选择性地添加计时器触发器。 添加 `--schedule` 参数并为计时器传递 cron 表达式。

作为一个简单的示例，以下任务触发了 `hello-world` 每天 21:00 UTC 从 Microsoft 容器注册表运行映像。 该任务无需源代码上下文即可运行。

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

运行 [az acr task show][az-acr-task-show] 命令查看该计时器触发器是否已配置。 默认情况下，还启用了基础映像更新触发器。

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>触发任务

使用 [az acr task run][az-acr-task-run] 手动触发任务，以确保正确设置该任务：

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

如果容器成功运行，则输出类似于以下内容。 该输出经过简化，只显示关键步骤

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

在计划的时间之后，运行 [az acr task list-runs][az-acr-task-list-runs] 命令验证计时器是否按预期触发了任务：

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

如果计时器成功，输出将如下所示：

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>管理计时器触发器

使用 [az acr task timer][az-acr-task-timer] 命令管理 ACR 任务的计时器触发器。

### <a name="add-or-update-a-timer-trigger"></a>添加或更新计时器触发器

创建任务后，可以选择性地使用 [az acr task timer add][az-acr-task-timer-add] 命令添加计时器触发器。 下面的示例将计时器触发器名称 *timer2* 添加到之前创建的 *timertask* 。 此计时器每日 10:30（UTC 时间）触发任务。

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

使用 [az acr task timer update][az-acr-task-timer-update] 命令更新现有触发器的计划或更改其状态。 例如，更新名为 *timer2* 的触发器，以在 11:30（UTC 时间）触发任务：

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>列出计时器触发器

[az acr task timer list][az-acr-task-timer-list] 命令可显示针对任务设置的计时器触发器：

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

示例输出：

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>删除计时器触发器

使用 [az acr task timer remove][az-acr-task-timer-remove] 命令从任务中删除计时器触发器。 下面的示例从 *timertask* 中删除 *timer2* 触发器：

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 表达式

ACR 任务使用 [NCronTab](https://github.com/atifaziz/NCrontab) 库来解释 cron 表达式。 ACR 任务支持的表达式包含空格分隔的五个必需字段：

`{minute} {hour} {day} {month} {day-of-week}`

cron 表达式使用的时区为协调世界时 (UTC)。 时间为 24 小时制。

> [!NOTE]
> ACR 任务不支持在 cron 表达式中使用 `{second}` 或 `{year}` 字段。 如果复制其他系统中使用的 cron 表达式，请确保删除这些字段（如果已使用）。

每个字段可以具有下列类型之一的值：

|类型  |示例  |何时触发  |
|---------|---------|---------|
|一个具体值 |<nobr>`"5 * * * *"`</nobr>|每小时的第 5 分钟|
|所有值 (`*`)|<nobr>`"* 5 * * *"`</nobr>|从 5:00 UTC 开始每隔一分钟（每天 60 次）|
|一个范围（`-` 运算符）|<nobr>`"0 1-3 * * *"`</nobr>|每天 1:00、2:00 和 3:00 UTC，触发 3 次|
|一组值（`,` 运算符）|<nobr>`"20,30,40 * * * *"`</nobr>|每小时的第 20 分钟、30 分钟和 40 分钟，触发 3 次|
|一个间隔值（`/` 运算符）|<nobr>`"*/10 * * * *"`</nobr>|每小时的第 10 分钟、20 分钟...，触发 6 次

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 示例

|示例|何时触发  |
|---------|---------|
|`"*/5 * * * *"`|每五分钟一次|
|`"0 * * * *"`|每小时一次（在每小时的开头）|
|`"0 */2 * * *"`|每两小时一次|
|`"0 9-17 * * *"`|从 9:00 到 17:00 UTC 每小时触发一次|
|`"30 9 * * *"`|每天 9:30 UTC|
|`"30 9 * * 1-5"`|每个工作日的 9:30 UTC|
|`"30 9 * Jan Mon"`|一月份的每个星期一 9:30 UTC|

## <a name="clean-up-resources"></a>清理资源

若要删除本系列教程中创建的所有资源（包括容器注册表、容器实例、密钥保管库和服务主体），请发出以下命令：

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建由计时器自动触发的 Azure 容器注册表任务。 

有关使用计划任务清理注册表中的存储库的示例，请参阅 [自动清除 Azure 容器注册表](container-registry-auto-purge.md)中的映像。

有关由源代码提交或基础映像更新触发的任务的示例，请参阅 [ACR 任务系列教程](container-registry-tutorial-quick-task.md)中的其他文章。



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
