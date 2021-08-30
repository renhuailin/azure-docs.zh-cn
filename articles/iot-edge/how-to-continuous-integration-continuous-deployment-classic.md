---
title: 持续集成和持续部署到 Azure IoT Edge 设备（经典编辑器）- Azure IoT Edge
description: 使用经典编辑器设置持续集成和持续部署 - Azure IoT Edge 和 Azure DevOps、Azure Pipelines
author: kgremban
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 757ae7f71e5b03a5dc38b6e5438cdf22a0083965
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740682"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>持续集成和持续部署到 Azure IoT Edge 设备（经典编辑器）

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

借助 Azure Pipelines 中的内置 Azure IoT Edge 任务，可以轻松地在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示了如何在经典编辑器中使用 Azure Pipelines 的持续集成和持续部署功能，快速高效地生成和测试应用程序并将其部署到 Azure IoT Edge。 也可[使用 YAML](how-to-continuous-integration-continuous-deployment.md)。

![示意图 - 用于开发和生产的 CI 和 CD 分支](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

本文介绍如何使用 Azure Pipelines 的内置 [Azure IoT Edge 任务](/azure/devops/pipelines/tasks/build/azure-iot-edge)为 IoT Edge 解决方案创建生成和发布管道。 添加到管道中的每个 Azure IoT Edge 任务都可执行以下四个操作之一：

 | 操作 | 说明 |
 | --- | --- |
 | 生成模块映像 | 使用 IoT Edge 解决方案代码生成容器映像。|
 | 推送模块映像 | 将模块映像推送到指定的容器注册表。 |
 | 生成部署清单 | 使用 deployment.template.json 文件和变量来生成最终的 IoT Edge 部署清单文件。 |
 | 部署到 IoT Edge 设备 | 创建目标为一个或多个 IoT Edge 设备的 IoT Edge 部署。 |

除非另行指定，否则本文中的过程不会探讨通过任务参数提供的所有功能。 有关更多信息，请参见以下资源：

* [任务版本](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **高级** - 根据情况指定不想生成的模块。
* [控制选项](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [环境变量](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [输出变量](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>先决条件

* Azure Repos 存储库。 如果没有存储库，可[在项目中创建一个新的 Git 存储库](/azure/devops/repos/git/create-new-repo)。 在本文中，我们创建了名为“IoTEdgeRepo”的存储库。
* 提交 IoT Edge 解决方案并将其推送到存储库。 如果要为测试本文创建新的示例解决方案，请按照[在 Visual Studio Code 中开发和调试模块](how-to-vs-code-develop-module.md)或[在 Visual Studio 中开发和调试 C# 模块](./how-to-visual-studio-develop-module.md)中的步骤进行操作。 在本文中，我们在名为“IoTEdgeSolution”的存储库中创建了一个解决方案，其中包含名为“filtermodule”的模块的代码 。

   对于本文，你只需要 Visual Studio Code 或 Visual Studio 中的 IoT Edge 模板创建的解决方案文件夹。 在继续操作之前，无需生成、推送、部署或调试此代码。 你将在 Azure Pipelines 中设置这些流程。

  了解解决方案中 deployment.template.js 文件的路径，该路径在几个步骤中使用。 如果不熟悉部署模板的角色，请参阅[了解如何部署模块和建立路由](module-composition.md)。

  >[!TIP]
  >如果要创建新解决方案，请首先在本地克隆存储库。 然后，在创建解决方案时，可以选择直接在存储库文件夹中创建它。 可以轻松从中提交和推送新文件。

* 容器注册表，你可以在其中推送模块图像。 可使用 [Azure 容器注册表](../container-registry/index.yml)或第三方注册表。
* 一个活动的具有至少两个 IoT Edge 设备的 Azure [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)，用于测试单独的测试和生产部署阶段。 可按照快速入门文章在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上创建 IoT Edge 设备

## <a name="create-a-build-pipeline-for-continuous-integration"></a>创建用于持续集成的生成管道

在本部分中，将创建新的生成管道。 配置管道使其自动运行，并在向 IoT Edge 解决方案签入任何更改时发布生成日志。

1. 登录 Azure DevOps 组织 (`https://dev.azure.com/{your organization}`)，打开包含 IoT Edge 解决方案存储库的项目。

    ![打开 DevOps 项目](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. 在项目的左窗格菜单中，选择“管道”。 在页面正中选择“创建管道”。 或者，如果已有生成管道，则选择右上方的“新建管道”按钮。

    ![创建新的生成管道](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. 在“代码位置”页的底部，选择“使用经典编辑器”。 如果要使用 YAML 创建项目的生成管道，请参阅 [YAML 指南](how-to-continuous-integration-continuous-deployment.md)。

    ![选择“使用经典编辑器”](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. 按照提示创建管道。

   1. 提供新生成管道的源信息。 选择“Azure Repos Git”作为源，然后选择 IoT Edge 解决方案代码所在的项目、存储库和分支。 然后选择“继续”。

      ![选择管道源](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. 选择“空作业”而不是模板。

      ![从生成管道的空作业开始](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. 创建管道后，将转到管道编辑器。 可在此处更改管道的名称、代理池和代理规范。

   可选择一个 Microsoft 托管池或由你自己管理的自承载池。

   在管道描述中，根据目标平台选择正确的代理规范：

   * 若要在用于 Linux 容器的 amd64 平台中生成模块，请选择“ubuntu-16.04”

   * 如果想在平台 amd64 中为 Windows 1809 容器生成模块，则需要[在 Windows 上设置自托管代理](/azure/devops/pipelines/agents/v2-windows)。

   * 如果想在平台 arm32v7 或 arm64 中为 Linux 容器生成模块，则需要[在 Linux 上设置自托管代理](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)。

    ![配置生成代理规范](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. 管道预先配置了名为“代理作业 1”的作业。 选择加号 (+)，向作业添加四个任务：“Azure IoT Edge”两次，“复制文件”一次，“发布生成项目”一次。    搜索每个任务并将鼠标悬停在任务的名称上以查看“添加”按钮。

   ![添加 Azure IoT Edge 任务](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   添加所有四个任务后，代理作业如下所示：

   ![生成管道中的四个任务](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. 选择第一个“Azure IoT Edge”任务进行编辑。 此任务通过指定的目标平台生成解决方案中的所有模块。 使用以下值编辑任务：

   | 参数 | 说明 |
   | --- | --- |
   | 显示名称 | “操作”字段更改时，显示名称会自动更新。 |
   | 操作 | 选择“生成模块映像”。 |
   | .template.json 文件 | 选择省略号 (...) 并导航到包含 IoT Edge 解决方案的存储库中的“deployment.template.json”文件。 |
   | 默认平台 | 根据目标 IoT Edge 设备为模块选择相应的操作系统。 |
   | 输出变量 | 提供一个将与文件路径（在其中生成 deployment.json 文件）关联的引用名称，例如 **edge**。 |

   有关此任务及其参数的详细信息，请参阅 [Azure IoT Edge 任务](/azure/devops/pipelines/tasks/build/azure-iot-edge)。

   这些配置使用 `module.json` 文件中定义的映像存储库和标记来命名和标记模块映像。 “生成模块映像”也有助于将变量替换为在 `module.json` 文件中定义的具体值。 在 Visual Studio 或 Visual Studio Code 中，你是在 `.env` 文件中指定实际值。 在 Azure Pipelines 中，请在“管道变量”选项卡中设置值。在管道编辑器菜单上选择“变量”选项卡并配置名称和值，如下所示：

   * **ACR_ADDRESS**：Azure 容器注册表的“登录服务器”值。 可以在 Azure 门户的容器注册表的概述页中检索登录服务器值。

   如果你在项目中有其他变量，可以在此选项卡上指定名称和值。“生成模块映像”仅识别 `${VARIABLE}` 格式的变量。 请确保在 `**/module.json` 文件中使用此格式。

8. 选择第二个“Azure IoT Edge”任务进行编辑。 此任务会把所有模块图像推送到所选容器注册表。

   | 参数 | 说明 |
   | --- | --- |
   | 显示名称 | “操作”字段更改时，显示名称会自动更新。 |
   | 操作 | 选择“推送模块映像”。 |
   | 容器注册表类型 | 使用默认类型：`Azure Container Registry`。 |
   | Azure 订阅 | 选择订阅。 |
   | Azure 容器注册表 | 选择用于存储模块图像的容器注册表类型。 窗体会根据选择的注册表类型进行更改。 如果选择“Azure 容器注册表”，请使用下拉列表选择 Azure 订阅和容器注册表的名称。 如果选择“通用容器注册表”，请选择“新建”以创建注册表服务连接 。 |
   | .template.json 文件 | 选择省略号 (...) 并导航到包含 IoT Edge 解决方案的存储库中的“deployment.template.json”文件。 |
   | 默认平台 | 根据目标 IoT Edge 设备为模块选择相应的操作系统。 |
   | 向部署清单添加注册表凭据 | 指定 true 以添加用于将 Docker 映像推送到部署清单的注册表凭据。 |

   有关此任务及其参数的详细信息，请参阅 [Azure IoT Edge 任务](/azure/devops/pipelines/tasks/build/azure-iot-edge)。

   如果有多个用于托管模块映像的容器注册表，则需重复此任务，选择其他容器注册表，并使用“高级”设置中的“绕过模块”来绕过不适用于此特定注册表的映像。 

9. 选择“复制文件”任务进行编辑。 使用此任务将文件复制到项目暂存目录。

   | 参数 | 说明 |
   | --- | --- |
   | 显示名称 | 使用默认名称或自定义 |
   | 源文件夹 | 包含要复制的文件的文件夹。 |
   | 目录 | 添加两行：`deployment.template.json` 和 `**/module.json`。 这两个文件充当生成 IoT Edge 部署清单所需的输入。 |
   | 目标文件夹 | 指定变量 `$(Build.ArtifactStagingDirectory)`。 请参阅[生成变量](/azure/devops/pipelines/build/variables#build-variables)，了解相关说明。 |

   有关此任务及其参数的详细信息，请参阅[复制文件任务](/azure/devops/pipelines/tasks/utility/copy-files?tabs=classic)。

10. 选择“发布生成项目”任务进行编辑。 提供到任务的项目暂存目录路径，使该路径可以发布到发布管道。

    | 参数 | 说明 |
    | --- | --- |
    | 显示名称 | 使用默认名称或自定义。 |
    | 要发布的路径 | 指定变量 `$(Build.ArtifactStagingDirectory)`。 请参阅[生成变量](/azure/devops/pipelines/build/variables#build-variables)，了解详细信息。 |
    | 项目名称 | 使用默认名称：**drop** |
    | 项目发布位置 | 使用默认位置：**Azure Pipelines** |

    有关此任务及其参数的详细信息，请参阅[发布生成工件任务](/azure/devops/pipelines/tasks/utility/publish-build-artifacts)。

11. 打开“触发器”标签，然后选中“启用持续集成”框 。 确保包含代码的分支已包括在内。

    ![开启“持续集成”触发器](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. 从“保存并排队”下拉列表中选择“保存”。 

此管道现配置为在将新代码推送到存储库时自动运行。 发布管道项目的最后一项任务是触发发布管道。 继续下一部分以生成发布管道。

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Azure DevOps 中的 Azure IoT Edge 任务尚不支持分层部署。
>
>但是，可以使用 [Azure DevOps 中的 Azure CLI 任务](/azure/devops/pipelines/tasks/deploy/azure-cli)将部署创建为分层部署。 对于“内联脚本”值，你可以使用 [az iot edge deployment create 命令](/cli/azure/iot/edge/deployment)：
>
>```azurecli-interactive
>az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>后续步骤

* [适用于 IoT Edge 的 Azure DevOps 入门版](how-to-devops-starter.md)中的 IoT Edge DevOps 示例
* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，详细了解创建、更新或删除部署的步骤。