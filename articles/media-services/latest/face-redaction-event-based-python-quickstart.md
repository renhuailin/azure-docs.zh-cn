---
title: 基于事件的人脸编修
description: 本快速入门演示如何在 Azure 上部署基于事件的解决方案，在此解决方案中，将使用 Azure 媒体服务中的作业来转换传入的视频。
services: media-services
author: harmke
manager: ervandeh
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 5/21/2021
ms.author: inhenkel
ms.openlocfilehash: 198009abab7e1c5f663a454eb23debc55c81d7f3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730794"
---
# <a name="event-based-face-redaction"></a>基于事件的人脸编修

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>简介
 
在某些情况下或用例中，Azure 媒体服务应在视频进入数据存储时处理或分析视频。 例如，团队想要分析某个地点或工厂的视频，以确认现场人员是否遵循安全规定（例如，佩戴头盔）。 对于这一用例，现场 Edge 设备可以在检测到运动时捕获视频，然后发送到 Azure。 为了遵守隐私标准，在团队分析视频中捕获到的人脸之前，应对人脸进行编修。 若要尽快与团队共享扩充的视频，在视频进入 Azure 之后应立即完成人脸编修步骤。 本快速入门演示如何在基于事件的方案中，在 Azure 中使用 Azure 媒体服务。 将使用 Azure 媒体服务中的作业来转换上传到存储帐户的视频。 这一作业使用媒体服务 v3 API。

将使用的特定转换称为[人脸编修器](./analyze-face-redaction-concept.md)。 这是 Azure 媒体分析预设，可用于通过模糊处理所选个人的人脸来修改视频。

完成本快速入门后即可对视频中的人脸进行编修：

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/output-redacted.gif" alt-text="示例输出":::

## <a name="solution-overview"></a>解决方案概述

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/architecture.png" alt-text="解决方案的体系结构概述":::

本快速入门演示如何部署上文的解决方案概述中提到的解决方案。 本解决方案以一个存储帐户 (Azure Data Lake Storage Gen2) 开始，并且连接了一个事件侦听器（事件网格），当新的 .mp4 文件上传到存储帐户时便会触发 Azure Functions。 Azure Functions 将在 Azure 媒体服务将作业提交给预先配置的转换。 生成的编修视频将存储在 Blob 存储帐户中。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 创建一个资源组以用于此快速入门。

## <a name="get-the-sample-and-understand-its-deployment"></a>获取示例并了解其部署

创建 [Python 示例存储库](https://github.com/Azure-Samples/media-services-v3-python)的分叉。 在本快速入门中，我们将使用 FaceRedactorEventBased 示例。

此示例的部署包括三个单独的步骤：部署 Azure 服务以设置整个解决方案，部署用于在上传新文件时将作业提交到 Azure 媒体服务的函数应用以及配置 Eventgrid 触发器。 我们已创建一个 GitHub Actions 工作流以执行这些步骤。 因此，可以通过将所需的变量添加到 GitHub 环境来部署此解决方案，这意味着不需要本地开发工具。

## <a name="create-a-service-principal"></a>创建服务主体

在 GitHub Actions 工作流能够运行之前，必须创建一个服务主体，此服务主体需在资源组具备“参与者”和“存储 Blob 数据读者”角色。  此服务主体将是代表 GitHub Actions 预配和配置所有 Azure 服务的应用程序。 部署解决方案为需要处理的视频生成 SAS 令牌之后，也要使用服务主体。

若要创建服务主体并赋予其资源组所需的角色，请在以下 bash 命令中填写变量，然后在 Cloud Shell 中运行此命令：
```bash
# Replace <subscription-id>, <name-of-resource-group> and <name-of-app> with the corresponding values. 
# Make sure to use a unique name for the app name parameter.

app_name="<name-of-app>"
resource_group="<name-of-resource-group>"
subscription_id="<subscription-id>"

az ad sp create-for-rbac --name $app_name --role contributor \
                     --scopes /subscriptions/$subscription_id/resourceGroups/$resource_group \
                     --sdk-auth

object_id=$(az ad sp list --display-name $app_name --query [0].objectId -o tsv)

az role assignment create --assignee $object_id --role "Storage Blob Data Reader" \
                      --scope /subscriptions/$subscription_id/resourceGroups/$resource_group
```
  
此命令应输出下面这样的 JSON 对象：
   
```json
{
  "clientId": "<GUID>",
  "clientSecret": "<GUID>",
  "subscriptionId": "<GUID>",
  "tenantId": "<GUID>",
  (...)
}
```
请确保复制输出，以便在下一步中使用。
 
## <a name="add-service-principal-details-to-github-secrets"></a>将服务主体详细信息添加到 GitHub 机密中 

服务主体详细信息应存储为 [GitHub 机密](https://docs.github.com/en/actions/reference/encrypted-secrets)，以便 GitHub Actions 可以在 Azure 中部署和配置必要的服务。 转到“存储库设置”-> 分叉存储库的机密，然后单击“创建新机密”。 创建以下机密：
 - 创建“AZURE_CREDENTIALS”，然后粘贴上一步的输出（完整 json）。 在 GitHub Actions 工作流中，此机密用于创建与 Azure 的连接。 
 - 创建“CLIENT_ID”并粘贴上一步中“clientId”的值。
 - 创建“CLIENT_SECRET”并粘贴上一步中“clientSecret”的值。
 - 创建“TENANT_ID”并粘贴上一步中“tenantId”的值。
 
## <a name="create-the-env-file"></a>创建 .env 文件

从 VideoAnalytics/FaceRedactorEventBased 文件夹中分叉存储库中的 sample.env 文件中复制内容。 然后单击“添加文件”->“创建新文件”以创建自己的 .env 文件。 命名 .env 文件并填写变量。 完成后，单击“提交新文件”。 我们现在已准备好部署解决方案，但首先将检查我们将使用的代码文件。

## <a name="examine-the-code-for-provisioning-the-azure-resources"></a>检查用于预配 Azure 资源的代码

下面的 bash 脚本将预配此解决方案中使用的 Azure 服务。 bash 脚本使用 Azure CLI 并执行以下操作：
- 将环境变量加载到局部变量中。
- 定义 ADLSgen2、Azure 存储帐户、Azure 媒体服务、Azure Functions 应用以及事件网格系统主题和订阅的名称。
- 预配定义的 Azure 服务。

[!code-bash[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureServicesProvisioning/deploy_resources.azcli)]

## <a name="examine-azure-function-code"></a>检查 Azure Functions 代码

成功预配 Azure 资源后，我们便已准备好将 Python 代码部署到 Azure Functions。 /azure-function/EventGrid_AMSJob/__init__.py 文件包含以下逻辑：只要文件进入 Azure Data Lake Gen2 文件系统便会触发 AMS 作业。 此脚本执行以下步骤：
- 导入依赖项和库。
- 使用函数绑定器侦听 Azure 事件网格。
- 从事件架构获取并定义变量。
- 为 AMS 作业创建输入/输出资产。
- 使用 DataLakeService 客户端连接到 Azure Data Lake Gen2，然后生成 SAS 令牌以用作 AMS 作业输入的身份验证。
- 配置并创建作业。

[!code-python[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureFunction/EventGrid_AMSJob/__init__.py)]

## <a name="examine-the-code-for-configuring-the-azure-resources"></a>检查用于配置 Azure 资源的代码 

以下 bash 脚本用于在预配资源后配置资源。 部署函数代码后，部署解决方案的最后一步是执行此脚本。 此脚本可执行以下步骤：
- 为函数应用配置应用设置。
- 创建 Azure 事件网格系统主题。
- 创建事件订阅，以便在 ADLSg2 Raw 文件夹中创建 Blob 时触发 Azure Functions。
- 使用 REST API 调用创建 Azure 媒体服务转换。 将在 Azure Functions 中调用此转换。

> [!NOTE]
> 目前，Azure 媒体服务 v3 Python SDK 和 Azure CLI 都不支持创建 FaceRedaction 转换。 因此，我们使用 Rest API 方法来创建转换作业。

[!code-bash[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureServicesProvisioning/configure_resources.azcli)]
 
## <a name="enable-github-actions-pipeline"></a>启用 GitHub Actions 管道
 此存储库中的工作流文件包含执行此解决方案部署的步骤。 若要启动工作流，需要为你自己的存储库启用工作流。 若要启用工作流，请转到存储库的“操作”选项卡，然后选择“我了解我的工作流，继续并启用”。
:::image type="content" source="./media/face-redaction-event-based-python-quickstart/activate-workflow.png" alt-text="启用工作流":::

启用 GitHub Actions 后，可在以下位置找到工作负载文件：[.github/workflows/main.yml](https://github.com/Azure-Samples/media-services-v3-python/blob/main/.github/workflows/main.yml)。  除了触发器之外，还有一个生成作业，其中有几个步骤。 包括以下步骤：
- Env：此处定义了多个环境变量，引用我们之前添加的 GitHub 机密。
- 读取环境文件：读取环境文件以用于生成作业。
- 使用 Pip 解析项目依赖项：Azure Functions 中所需的库将加载到 GitHub Actions 环境中
- Azure 登录：此步骤使用 GitHub 机密以通过服务主体详细信息登录 Azure CLI。
- 使用 Azure CLI 脚本文件部署 Azure 资源：运行用于预配 Azure 资源的部署脚本
- 部署 Azure Functions 代码：此步骤将封装 Azure Functions 并部署在“./azure-function”目录中。 成功部署 Azure Functions 后，函数应该会显示在 Azure 门户中，名称为“EventGrid_AMSJob”：

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/azurefunction.png" alt-text="Azure 门户中可见的 Azure 函数":::

- 使用 Azure CLI 脚本文件配置 Azure 资源：如果一切正确，最后一步是配置部署的 Azure 服务以激活事件侦听器。

启用工作流后，选择“部署 Azure 媒体服务 FaceRedaction 解决方案”工作流，然后选择“运行工作流”。 现在，将使用前面步骤中添加的变量来部署解决方案。 等待几分钟，并验证其是否成功运行。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/run-workflow.png" alt-text="运行工作流":::

## <a name="test-your-solution"></a>测试解决方案
在 Azure 门户中转到 ADLS Gen2 的存储资源管理器。 将一个视频上传到 Raw 容器。 如果要查找测试视频，可以从[该网站](https://www.pexels.com/search/videos/group/)下载一个。 有关将视频上传到 ADLS Gen2 存储帐户的指导，请参见下图：

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/upload-test-data.png" alt-text="正在上传视频":::

在 Azure 媒体服务实例中验证是否创建了作业，方法是：访问 Azure 媒体服务帐户并从菜单中选择“转换 + 作业”。 然后选择人脸编修器转换。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-transform.png" alt-text="AMS 转换":::

此页应显示 Azure Functions 触发的作业。 作业可以已经完成，也可能仍在处理。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-job.png" alt-text="AMS 作业":::

通过选择作业，你将看到有关特定作业的一些详细信息。 如果选择“输出资产名称”，然后使用存储容器的链接（链接到存储容器），则在作业完成时可以看到已处理的视频。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-output.png" alt-text="AMS 输出":::

## <a name="clean-up-resources"></a>清理资源

完成此快速入门后，请删除资源组中创建的资源。 此外，还可以删除分叉存储库。

## <a name="next-steps"></a>后续步骤

如果要修改此示例，那么你可能希望在本地运行代码。 对于本地开发，Sample.env 文件中的变量已经足够用，因为当用户帐户登录到本地安装的 Azure CLI 时，不需要服务主体。 有关在本地使用 Azure Functions 的指导，请参阅[以下文档](../../azure-functions/create-first-function-vs-code-python.md)。
