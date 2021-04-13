---
title: 使用 Azure DevOps 持续部署（预览版）
titleSuffix: Azure Cognitive Services
description: 本文介绍如何为自定义命令应用程序设置持续部署。 你可以创建脚本来支持持续部署工作流。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98927603"
---
# <a name="continuous-deployment-with-azure-devops"></a>使用 Azure DevOps 持续部署

本文介绍如何为自定义命令应用程序设置持续部署。 将向你提供支持 CI/CD 工作流的脚本。

## <a name="prerequisite"></a>先决条件
> [!div class = "checklist"]
> * 用于开发的自定义命令应用程序 (DEV)
> * 用于生产的自定义命令应用程序 (PROD)
> * 注册 [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>导出/导入/发布

脚本托管在[认知服务语音助理 - 自定义命令](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)。 将 bash 目录中的脚本克隆到存储库中。 确保保持同一路径。

### <a name="set-up-a-pipeline"></a>设置管道 

1. 转到“Azure DevOps - Pipelines”，然后单击“New Pipeline”
1. 在“Connect”部分中，选择这些脚本所在的存储库的位置
1. 在“Select”部分中，选择你的存储库
1. 在“Configure”部分中，选择“Starter pipeline”
1. 接下来，你将获得一个带有 YAML 文件的编辑器，请用此脚本替换“steps”部分。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. 请注意，这些脚本假定你使用的是区域 `westus2`，如果不是这样，请相应地更新任务的参数

    > [!div class="mx-imgBorder"]
    > ![突出显示参数中的区域值的屏幕截图。](media/custom-commands/cicd-new-pipeline-yaml.png)

1. 在“Save and run”按钮中，打开下拉菜单，然后单击“Save”

### <a name="hook-up-the-pipeline-with-your-application"></a>将管道挂接到应用程序

1. 导航到管道的主页。
1. 在右上角下拉列表中，选择“Edit pipeline”。 将显示 YAML 编辑器。 
1. 在“Run”按钮旁的右上角，选择“Variables”。 单击“New variable”。
1. 添加这些变量：
    
    | 变量 | 说明 |
    | ------- | --------------- | ----------- |
    | SourceAppId | 开发应用程序的 ID |
    | TargetAppId | PROD 应用程序的 ID |
    | SubscriptionKey | 用于这两个应用程序的订阅密钥 |
    | 环境 | 应用程序环境（即 en-us） |

    > [!div class="mx-imgBorder"]
    > ![“发送活动”有效负载](media/custom-commands/cicd-edit-pipeline-variables.png)

1. 单击“Run”，然后单击正在运行的“作业”。 

    应会看到正在运行的任务列表，包括：“Export source app”、“Import to target app”和“Train and Publish target app”

## <a name="deploy-from-source-code"></a>从源代码进行部署

如果要将应用程序的定义保留在存储库中，我们会提供源代码中用于部署的脚本。 由于脚本在 bash 中，因此，如果你使用的是 Windows，则需要安装 [Linux 子系统](/windows/wsl/install-win10)。

脚本托管在[认知服务语音助理 - 自定义命令](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)。 将 bash 目录中的脚本克隆到存储库中。 确保保持同一路径。

### <a name="prepare-your-repository"></a>准备存储库

1. 为应用程序创建一个目录，本示例将创建一个名为“apps”的目录。
1. 更新以下 bash 脚本的参数，并运行。 它会将应用程序的对话框模型导入文件 myapp.json
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | 参数 | 说明 |
    | ------- | --------------- | ----------- |
    | region | 应用程序的区域，即 westus2。 |
    | subscriptionkey | 语音资源的订阅密钥。 |
    | appid | 要导出的自定义命令的应用程序 ID。 |

1. 将这些更改推送到存储库。

### <a name="set-up-a-pipeline"></a>设置管道 

1. 转到“Azure DevOps - Pipelines”，然后单击“New Pipeline”
1. 在“Connect”部分中，选择这些脚本所在的存储库的位置
1. 在“Select”部分中，选择你的存储库
1. 在“Configure”部分中，选择“Starter pipeline”
1. 接下来，你将获得一个带有 YAML 文件的编辑器，请用此脚本替换“steps”部分。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > 这些脚本假定你使用的是区域 westus2，如果不是这样，请相应地更新任务的参数

1. 在“Save and run”按钮中，打开下拉菜单，然后单击“Save”

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>将管道挂接到目标应用程序

1. 导航到管道的主页。
1. 在右上角下拉列表中，选择“Edit pipeline”。 将显示 YAML 编辑器。 
1. 在“Run”按钮旁的右上角，选择“Variables”。 单击“New variable”。
1. 添加这些变量：

    | 变量 | 说明 |
    | ------- | --------------- | ----------- |
    | TargetAppId | PROD 应用程序的 ID |
    | SubscriptionKey | 用于这两个应用程序的订阅密钥 |
    | 环境 | 应用程序环境（即 en-us） |

1. 单击“Run”，然后单击正在运行的“作业”。
    应会看到正在运行的任务列表，包括：“Import app”和“Train and Publish app”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看 GitHub 上的示例](https://aka.ms/speech/cc-samples)
