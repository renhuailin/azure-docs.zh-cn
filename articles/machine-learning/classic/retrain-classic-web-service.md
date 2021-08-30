---
title: 机器学习工作室（经典）：重新训练经典 Web 服务 - Azure
description: 了解如何重新训练模型和更新经典 Web 服务以在机器学习工作室（经典）中使用新训练的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a7dba27f5c4ef0f63b270111692c35080b2ec378
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688815"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>重新训练和部署经典工作室（经典）Web 服务

适用对象：![适用于](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![X 表示否定。](../../../includes/media/aml-applies-to-skus/no.png)[不适用于。](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

重新训练机器学习模型是确保它们保持准确并基于最相关的数据的一种方法。 本文介绍如何重新训练经典工作室（经典）Web 服务。 有关如何重新训练新的工作室（经典）Web 服务的指南，请[查看此操作指南文章](retrain-machine-learning-model.md)。

## <a name="prerequisites"></a>先决条件

本文假设你已经进行了重新训练试验和预测性试验。 这些步骤在[重新训练和部署机器学习模型](./retrain-machine-learning-model.md)中进行了解释。 但是，你不会将机器学习模型部署为新的 Web 服务，而是将预测试验部署为经典 Web 服务。
     
## <a name="add-a-new-endpoint"></a>添加新的终结点

已部署的预测 Web 服务包含与原始训练和评分实验训练模型保持同步的默认评分终结点。 若要使用新的训练模型更新 Web 服务，必须创建新的评分终结点。

以下两种方法可将新终结点添加到 Web 服务：

* 以编程方式
* 使用 Azure Web 服务门户

> [!NOTE]
> 确保将终结点添加到预测 Web 服务，而不是训练 Web 服务。 如果已正确部署训练和预测 Web 服务，应该看到已列出两个单独的 Web 服务。 预测 Web 服务应以“[predictive exp.]”结尾。
>

### <a name="programmatically-add-an-endpoint"></a>以编程方式添加终结点

可以使用此 [GitHub 存储库](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)中提供的示例代码添加评分终结点。

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>使用 Azure Web 服务门户添加终结点

1. 在机器学习工作室（经典）的左侧导航栏中，单击“Web 服务”。
1. 在 Web 服务仪表板的底部，单击“管理终结点预览”。
1. 单击“添加”。
1. 键入新终结点的名称及说明。 选择日志记录级别以及是否启用示例数据。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](web-services-logging.md)。

## <a name="update-the-added-endpoints-trained-model"></a>更新已添加终结点的训练模型

### <a name="retrieve-patch-url"></a>检索修补程序 URL

请按照以下步骤使用 Web 门户获取正确的修补程序 URL：

1. 登录到 [Azure 机器学习 Web 服务](https://services.azureml.net/)门户。
1. 单击“Web 服务”或顶部的“经典 Web 服务”。
1. 单击你正在使用的计分 Web 服务（如果未修改 Web 服务的默认名称，则它将以“[Scoring Exp.]”结尾）。
1. 单击“+新建”。
1. 添加终结点后，单击终结点名称。
1. 在“修补程序”URL 下面，单击“API 帮助”打开修补帮助页。

> [!NOTE]
> 如果已向训练 Web 服务而非预测 Web 服务添加终结点，在单击“更新资源”链接时会收到以下错误：“抱歉，此功能不受支持或在此上下文中不可用”。 此 Web 服务没有可更新的资源。 我们对于所造成的不便深表歉意，正在努力改进此工作流。
>

修补程序帮助页包含你必须使用的修补程序 URL，并提供可用于调用它的示例代码。

![修补程序 URL。](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>更新终结点

现在可以使用训练模型更新之前创建的评分终结点。

以下示例代码演示了如何使用 *BaseLocation*、*RelativeLocation*、*SasBlobToken* 和 PATCH URL 更新终结点。

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

可以从终结点仪表板获取用于调用的 *apiKey* 和 *endpointUrl*。

*Resources* 中的 *Name* 参数的值应与预测实验中已保存的训练模型的资源名称相匹配。 若要获取资源名称，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中，单击“机器学习”。
1. 在“名称”下，单击你的工作区，并单击“Web 服务”。
1. 在“名称”下，单击“人口普查模型 [预测实验]”。
1. 单击已添加的新终结点。
1. 在终结点仪表板上，单击“更新资源”。
1. 在 Web 服务的更新资源 API 文档页上，可以在“可更新资源”下找到“资源名称”。

如果在完成更新终结点之前 SAS 令牌到期，必须使用作业 ID 执行 GET 操作，才能获取全新令牌。

当代码成功运行时，新终结点应当在大约 30 秒内开始使用重新训练的模型。

## <a name="next-steps"></a>后续步骤

若要了解有关如何管理 Web 服务或跟踪多个实验运行的详细信息，请参阅以下文章：

* [探索 Web 服务门户](manage-new-webservice.md)
* [管理试验迭代](manage-experiment-iterations.md)