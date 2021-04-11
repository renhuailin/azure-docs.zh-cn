---
title: 使用 Azure DevOps 为流分析作业创建 CI/CD 管道
description: 本文介绍如何在 Azure DevOps 中为 Azure 流分析作业设置持续集成和部署 (CI/CD) 管道
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 82a2c3047f851c9fbc273cd13e730572c38b6bcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640383"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>使用 Azure DevOps 为流分析作业创建 CI/CD 管道

本文介绍如何使用 Azure 流分析 CI/CD 工具创建 Azure DevOps [生成](/azure/devops/pipelines/get-started/pipelines-get-started)和[发布](/azure/devops/pipelines/release/define-multistage-release-process)管道。

## <a name="commit-your-stream-analytics-project"></a>提交流分析项目

在开始之前，请将完整的流分析项目作为源文件提交到 [Azure DevOps](/azure/devops/user-guide/source-control) 存储库。 可以在 Azure Pipelines 中引用此[示例存储库](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo)和[流分析项目源代码](https://dev.azure.com/ASA-CICD-sample/_git/azure-streamanalytics-cicd-demo)。

本文中的步骤使用流分析 Visual Studio Code 项目。 如果使用的是 Visual Studio 项目，请按照[使用 CI/CD 工具自动执行 Azure 流分析作业的生成、测试和部署](cicd-tools.md)中的步骤操作。

## <a name="create-a-build-pipeline"></a>创建生成管道

本部分介绍如何创建生成管道。 

1. 打开 Web 浏览器并导航 Azure DevOps 中的项目。  

2. 在左侧导航菜单中的“管道”下，选择“生成”。 然后，选择“新建管道”。

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="创建新的 Azure 管道":::

3. 选择“使用经典编辑器”在没有 YAML 的情况下创建一个管道。

4. 选择源类型、团队项目和存储库。 然后选择“继续”。

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="选择 Azure 流分析项目":::

5. 在“选择模板”页上，选择“空作业”。

## <a name="install-npm-package"></a>安装 npm 包

1. 在“任务”页上，选择“代理作业 1”旁的加号。 在任务搜索中输入 npm，并选择“npm”。

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="选择 npm 任务":::

2. 为任务指定“显示名称”。 将“命令”选项更改为“自定义”，然后在“命令和参数”中输入以下命令。 保留其余默认选项。

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="输入 npm 任务的配置":::

如果需要使用托管 Linux 代理，请执行以下步骤：
1.  选择“代理规范”
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="选择代理规范的屏幕截图。":::

2.  在“任务”页上，选择“代理作业 1”旁的加号。 在任务搜索中输入命令行，然后选择“命令行”。
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="搜索命令行任务的屏幕截图。":::

3.  为任务指定“显示名称”。 在“脚本”中输入以下命令。 保留其余默认选项。

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="为 cmd 任务输入脚本的屏幕截图。":::

## <a name="add-a-build-task"></a>添加生成任务

1. 在“变量”页上，在“管道变量”中选择“+ 添加”。 添加以下变量。 根据你的偏好设置以下值：

   |变量名称|值|
   |-|-|
   |projectRootPath|[YourProjectName]|
   |outputPath|输出|
   |deployPath|部署|

2. 在“任务”页上，选择“代理作业 1”旁的加号。 搜索“命令行”。

3. 为任务指定“显示名称”，然后输入以下脚本。 用存储库名称和项目名称修改脚本。

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   下图使用流分析 Visual Studio Code 项目作为示例。

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="为命令行任务 visual studio code 输入配置":::

## <a name="add-a-test-task"></a>添加测试任务

1. 在“变量”页上，在“管道变量”中选择“+ 添加”。 添加以下变量。 使用输出路径和存储库名称修改这些值。

   |变量名称|值|
   |-|-|
   |testPath|测试|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="添加管道变量":::

2. 在“任务”页上，选择“代理作业 1”旁的加号。 搜索“命令行”。

3. 为任务指定“显示名称”，然后输入以下脚本。 用项目文件名和测试配置文件的路径来修改脚本。 

   有关如何添加和配置测试用例的详细信息，请参阅[自动测试说明](cicd-tools.md#automated-test)。

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="输入命令行任务的配置":::

## <a name="add-a-copy-files-task"></a>添加复制文件任务

需要添加复制文件任务，以将测试摘要文件和 Azure 资源管理器模板文件复制到项目文件夹。 

1. 在“任务”页上，选择“代理作业 1”旁的 +。 搜索“复制文件”。 然后输入以下配置。 通过将 `**` 分配给“内容”，将复制测试结果的所有文件。

   |参数|输入|
   |-|-|
   |显示名称|将文件复制到：$(build.artifactstagingdirectory)|
   |源文件夹|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |目录| `**` |
   |目标文件夹| `$(build.artifactstagingdirectory)`|

2. 展开“控制选项”。 在“运行此任务”中选择“即使上一任务失败，除非已取消生成”。

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="输入复制任务的配置":::

## <a name="add-a-publish-build-artifacts-task"></a>添加发布生成项目任务

1. 在“任务”页上，选择“代理作业 1”旁的加号。 搜索“发布生成项目”，然后选择带有黑色箭头图标的选项。

2. 展开“控制选项”。 在“运行此任务”中选择“即使上一任务失败，除非已取消生成”。

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="输入发布任务的配置":::

## <a name="save-and-run"></a>“保存”和“运行”

完成添加 npm 包、命令行、复制文件和发布生成项目任务后，请选择“保存并排队”。 出现提示时，请输入保存注释，然后选择“保存并运行”。 可以从管道的“摘要”页下载测试结果。

## <a name="check-the-build-and-test-results"></a>检查生成和测试结果

可在“已发布”文件夹中找到测试摘要文件和 Azure 资源管理器模板文件。

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="检查生成和测试结果":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="检查项目":::

## <a name="release-with-azure-pipelines"></a>使用 Azure Pipelines 发布

本部分介绍如何创建发布管道。 

打开 Web 浏览器并导航到 Azure 流分析 Visual Studio Code 项目。

1. 在左侧导航菜单中的“管道”下，选择“发布”。 然后选择“新建管道”。

2. 选择“从空作业开始”。

3. 在“项目”框中，选择“+ 添加项目”。 在“源”下，选择创建的生成管道，然后选择“添加”。

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="输入生成管道项目":::

4. 将“阶段 1”的名称更改为“将作业部署到测试环境”。

5. 添加新阶段并将其命名为“将作业部署到生产环境”。

### <a name="add-deploy-tasks"></a>添加部署任务

1. 从“任务”下拉列表中，选择“将作业部署到测试环境”。

2. 选择“代理作业”旁的 +，搜索“ARM 模板部署”。 输入以下参数：

   |参数|值|
   |-|-|
   |显示名称| 部署 myASAProject|
   |Azure 订阅| 选择订阅。|
   |操作| *创建或更新资源组*|
   |资源组| 选择将包含流分析作业的测试资源组的名称。|
   |位置|选择测试资源组的位置。|
   |模板位置| 链接的项目|
   |模板| $(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |模板参数|$(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |重写模板参数|-<arm_template_parameter> "your value". 可以使用“变量”定义参数。|
   |部署模式|增量|

3. 从“任务”下拉列表中，选择“将作业部署到生产环境”。

4. 选择“代理作业”旁的 +，搜索“ARM 模板部署”。 输入以下参数：

   |参数|值|
   |-|-|
   |显示名称| 部署 myASAProject|
   |Azure 订阅| 选择订阅。|
   |操作| *创建或更新资源组*|
   |资源组| 选择将包含流分析作业的生产资源组的名称。|
   |位置|选择生产资源组的位置。|
   |模板位置| 链接的项目|
   |模板| $(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |模板参数|$(System.DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |重写模板参数|-<arm_template_parameter> "your value"|
   |部署模式|增量|

### <a name="create-a-release"></a>创建发布

若要创建发布，请在右上角选择“创建发布”。

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="使用 Azure Pipelines 创建发布":::

## <a name="next-steps"></a>后续步骤

* [Azure 流分析的持续集成和持续部署](cicd-overview.md)
* [使用 CI/CD 工具自动生成、测试和部署 Azure 流分析作业](cicd-tools.md)