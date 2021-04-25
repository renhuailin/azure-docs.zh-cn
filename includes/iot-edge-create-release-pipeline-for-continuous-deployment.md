---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803230"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>为持续部署创建发布管道

在本部分中，会创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

创建新管道，然后添加新阶段：

1. 在“管道”下的“发布”选项卡中，选择“+ 新建管道”  。 或者，如果已有发布管道，则选择“+ 新建”按钮并选择“+ 新建发布管道” 。  

    ![使用“+ 新建管道”按钮添加发布管道](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. 提示选择模板时，选择以“空作业”开头。

    ![从发布管道的空作业开始](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. 新版本管道初始化为一个阶段，称为“阶段 1”。 将阶段 1 重命名为“dev”，并将其视为适用于你的开发环境的持续部署管道。 通常情况下，持续部署管道具有多个阶段，包括开发、过渡和生产。可以使用不同的名称并根据 DevOps 实践创建更多阶段  。 重命名后关闭阶段详细信息窗口。

   还可选择顶部的“新建发布管道”文本来重命名发布管道。

4. 将发布链接到生成管道发布的生成项目。 在项目区域中，单击“添加”。

   ![单击界面的项目区域中的“添加”](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. 在“添加项目”页上，选择“生成”作为“源类型”  。 选择项目和创建的生成管道。 如果需要，可将“源别名”更改为更具说明性的名称。 然后选择“添加”。

   ![在“添加项目”页上，选择“添加”以创建项目](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. 打开项目触发器并选择切换以启用持续部署触发器。 现在，每次有新的生成时，都会创建新版本。

   ![打开项目触发器并切换以启用持续部署触发器](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. “dev”阶段预先配置了一个作业和零个任务。 从管道菜单中，选择“任务”，然后选择“dev”阶段 。 选择“代理作业”并将其“显示名称”更改为“QA”  。 可配置有关代理作业的详细信息，但部署任务对平台不敏感，因此可在所选的“代理池”中使用任何“代理规格” 。

   ![在“任务”选项卡下查看开发阶段的任务](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. 在 QA 作业中，选择加号 (+) 以添加两个任务。 搜索并添加“Azure IoT Edge”两次。

9. 选择第一个 **Azure IoT Edge** 任务，并使用以下值对其进行配置：

    | 参数 | 说明 |
    | --- | --- |
    | 显示名称 | “操作”字段更改时，显示名称会自动更新。 |
    | 操作 | 选择 `Generate deployment manifest`。 |
    | .template.json 文件 | 指定路径：`$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`。 路径从生成管道发布。 |
    | 默认平台 | 根据目标 IoT Edge 设备为模块选择相应的操作系统。 |
    | 输出路径| 放置路径 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`。 此路径是最终的 IoT Edge 部署清单文件。 |

    这些配置有助于替换 `deployment.template.json` 文件中的模块映像 URL。 “生成部署清单”也有助于将变量替换为在 `deployment.template.json` 文件中定义的具体值。 在 VS/VS Code 中，你是在 `.env` 文件中指定实际值。 在 Azure Pipelines 中，请在“发布管道变量”选项卡中设置值。转到“变量”选项卡，按如下所示配置名称和值 

    * **ACR_ADDRESS**：Azure 容器注册表的“登录服务器”值。 可以在 Azure 门户的容器注册表的“概述”页中检索登录服务器。
    * **ACR_PASSWORD**：你的 Azure 容器注册表密码。
    * **ACR_USER**：你的 Azure 容器注册表用户名。

    如果你在项目中有其他变量，可以在此选项卡中指定名称和值。“生成部署清单”只能识别 `${VARIABLE}` 样式的变量。 请确保在 `*.template.json` 文件中使用此样式。
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![在“变量”选项卡中配置发布管道的变量](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. 选择第二个 **Azure IoT Edge** 任务，并使用以下值对其进行配置：

    | 参数 | 说明 |
    | --- | --- |
    | 显示名称 | “操作”字段更改时，显示名称会自动更新。 |
    | 操作 | 选择 `Deploy to IoT Edge devices`。 |
    | 部署文件 | 放置路径 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`。 此路径指向 IoT Edge 部署清单文件。 |
    | Azure 订阅 | 选择包含 IoT 中心的订阅。|
    | IoT 中心名称 | 选择 IoT 中心。|
    | 选择单个/多个设备 | 选择是否要将发布管道部署到一个或多个设备。 如果部署到单个设备，请输入“IoT Edge设备 ID”。 如果要部署到多个设备，请指定设备“目标条件”。 目标条件是用于在 IoT 中心匹配一组 IoT Edge 设备的筛选器。 若想将设备标记用作条件，则需要使用 IoT 中心设备孪生更新对应的设备标记。 在高级设置中更新“IoT Edge 部署 ID”和“IoT Edge 部署优先级” 。 有关为多个设备创建部署的详细信息，请参阅[了解 IoT Edge 自动部署](../articles/iot-edge/module-deployment-monitoring.md)。 |
    | 设备 ID 或目标条件 | 根据前面的选择，指定要部署到多个设备的设备 ID 或[目标条件](../articles/iot-edge/module-deployment-monitoring.md#target-condition)。 |
    | 高级 | 对于 IoT Edge 部署 ID，请指定 `$(System.TeamProject)-$(Release.EnvironmentName)`。 此变量将项目和发布名称映射到你的 IoT Edge 部署 ID。 |
    

    如果任务涉及使用公有云不可见的专用 Docker 受信任注册表中的映像，你可将 SKIP_MODULE_IMAGE_VALIDATION 环境变量设置为 `true` 以跳过图像验证。 

    ![为开发阶段添加 Azure IoT Edge 任务](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. 选择“保存”，将更改保存到新发布管道。 从菜单中选择“管道”选项卡，返回管道视图。
