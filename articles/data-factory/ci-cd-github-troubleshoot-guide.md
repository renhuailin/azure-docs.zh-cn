---
title: 在 ADF 中排查 CI-CD、Azure DevOps 和 GitHub 问题
description: 使用不同的方法对 ADF 中的 CI CD 问题进行故障排除。
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 2b6f97f0966cb2c92dbd88c4a70188282ed3ed27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802027"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>在 ADF 中排查 CI-CD、Azure DevOps 和 GitHub 问题 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍 Azure 数据工厂中针对持续集成-持续部署 (CI-CD)、Azure DevOps 和 GitHub 问题的常见故障排除方法。

如果在使用源代码管理或 DevOps 技术方面有疑问或问题，下面是一些可能有用的文章：

- 请参阅 [ADF 中的源代码管理](source-control.md)，了解如何在 ADF 中实行源代码管理。 
- 请参阅 [ADF 中的 CI-CD](continuous-integration-deployment.md)，详细了解如何在 ADF 中实行 DevOps CI-CD。
 
## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>由于租户不同，连接到 Git 存储库失败

#### <a name="issue"></a>问题
    
有时会遇到 HTTP 状态 401 等身份验证问题。 特别是在多个租户具有来宾帐户的时候，情况可能变得更加复杂。

#### <a name="cause"></a>原因

我们看到的是，从原始租户那里获得令牌，但 ADF 位于来宾租户并且尝试使用该令牌来访问来宾租户中的 DevOps。 这不属于预期行为。

#### <a name="recommendation"></a>建议

应改为使用从来宾租户颁发的令牌。 例如，需要将同一 Azure Active Directory 分配为来宾租户和 DevOps，因此它可以正确设置令牌行为并使用正确的租户。

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>参数文件中的模板参数无效

#### <a name="issue"></a>问题

如果我们删除开发分支中的触发器，而该触发器已在具有相同配置（如频率和间隔）的测试或生产分支中可用，那么发布管道部署将成功，并且相应的触发器将在各自环境中删除。 但是，如果测试/生产环境中使用的配置（如频率和间隔）不同，并且在开发环境中删除相同的触发器，那么部署将失败并出现错误。

#### <a name="cause"></a>原因

CI/CD 管道失败并出现以下错误：

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>建议

之所以发生此错误，是因为我们经常删除的触发器已进行参数化，于是这些参数将无法在 ARM 模板中使用（因为该触发器不再存在）。 由于参数不再位于 ARM 模板，因此需要更新 DevOps 管道中的重写参数。 否则，每次更改 ARM 模板中的参数时，必须更新 DevOps 管道中的重写参数（在部署任务中）。

### <a name="updating-property-type-is-not-supported"></a>不支持更新属性类型

#### <a name="issue"></a>问题

CI/CD 发布管道失败，出现以下错误：

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>原因

这是因为目标工厂中名称相同的集成运行时的类型不同。 部署时集成运行时的类型需要相同。

#### <a name="recommendation"></a>建议

- 请参阅下面的 CI/CD 最佳做法：

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- 集成运行时不经常更改，并且在 CI/CD 中的所有阶段类似，因此数据工厂需要集成运行时在 CI/CD 的所有阶段都具有相同的名称和类型。 如果名称、类型和属性不同，请确保匹配源和目标集成运行时配置，然后部署发布管道。
- 若要在所有阶段中共享集成运行时，请考虑使用三元工厂，这只是为了包含共享的集成运行时。 可以在所有环境中将此共享工厂用作链接的集成运行时类型。

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>由于无效的引用，文档创建或更新失败

#### <a name="issue"></a>问题

尝试将更改发布到数据工厂时，收到以下错误消息：

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>原因

已取消 Git 配置，并且在选择了“重要资源”标志的情况下再次对其进行设置，这将数据工厂设置为“同步”。 这意味着没有要发布的更改。

#### <a name="resolution"></a>解决方法

取消 Git 配置并再次进行设置，并确保不选中“导入现有资源”复选框。

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>数据工厂从一个资源组移动到另一个资源组失败

#### <a name="issue"></a>问题

未能将数据工厂从一个资源组移到另一个资源组，并出现以下错误：

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>解决方法

需要删除 SSIS-IR 和 Shared IR 才能允许移动操作。 如果不想删除集成运行时，最好的方法是遵循复制和克隆文档中的操作来完成复制，并在完成后删除旧的数据工厂。

###  <a name="unable-to-export-and-import-arm-template"></a>无法导出和导入 ARM 模板

#### <a name="issue"></a>问题

无法导出和导入 ARM 模板。 门户上没有错误，但在浏览器跟踪中可能会看到以下错误：

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>原因

已创建客户角色作为用户，但该角色没有必要的权限。 在 UI 中加载工厂时，将检查工厂的一系列公开控制值。 这种情况下，用户的访问角色无权访问 queryFeaturesValue API。 若要访问此 API，请关闭全局参数功能。 ARM 导出代码路径部分依赖于全局参数功能。

#### <a name="resolution"></a>解决方法

为了解决此问题，需要将以下权限添加到角色：Microsoft.DataFactory/factories/queryFeaturesValue/action。 默认情况下，此权限应包含在“数据工厂参与者”角色中。

###  <a name="cannot-automate-publishing-for-cicd"></a>无法针对 CI/CD 进行自动发布 

#### <a name="cause"></a>原因

直到最近，为部署发布 ADF 管道的唯一方法是使用 ADF 门户按钮单击。 现在可以自动执行此过程。 

#### <a name="resolution"></a>解决方法

CI/CD 过程已增强。 自动发布功能从 ADF UX 中提取、验证和导出所有 Azure 资源管理器 (ARM) 模板功能。 它通过公共可用的 npm 包 [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) 使该逻辑可供使用。 这允许你以编程方式触发这些操作，而无需转到 ADF UI 并单击按钮。 这为 CI/CD 管道提供了真正的持续集成体验。 有关详细信息，请参阅 [ADF CI/CD 发布改进](./continuous-integration-deployment-improvements.md)。 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>由于 4mb ARM 模板限制而无法发布  

#### <a name="issue"></a>问题

无法部署，因为达到 Azure 资源管理器限制 - 模板总大小为 4mb。 需要在超过限制后部署解决方案。 

#### <a name="cause"></a>原因

Azure 资源管理器将模板大小限制为 4mb。 将模板大小限制为 4 MB 以内，每个参数文件大小限制为 64 KB 以内。 4-MB 限制适用于模板使用迭代资源定义以及变量和参数值进行扩展后的最终状态。 但已经超出了限制。 

#### <a name="resolution"></a>解决方法

对于中小型解决方案，单个模板更易于理解和维护。 可以查看单个文件中的所有资源和值。 对于高级方案，使用链接模板可将解决方案分解为目标组件。 请遵循[使用链接模板和嵌套模板](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)的最佳做法。

### <a name="cannot-connect-to-git-enterprise"></a>无法连接到 GIT Enterprise  

##### <a name="issue"></a>问题

由于权限问题，无法连接到 GIT Enterprise。 可以看到“422 - 无法处理的实体”之类的错误。

#### <a name="cause"></a>原因

* 尚未为 ADF 配置 Oauth。 
* URL 配置错误。

##### <a name="resolution"></a>解决方法

首先授予 Oauth 对 ADF 的访问权限。 然后，需要使用正确的 URL 连接到 GIT Enterprise。 该配置必须设置为客户组织。 例如，ADF 将首先尝试 *https://hostname/api/v3/search/repositories?q=user%3<customer credential>....* 并失败。 然后，它将尝试 *https://hostname/api/v3/orgs/<org>/<repo>...* 并成功。 
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>无法从已删除的数据工厂恢复

#### <a name="issue"></a>问题
客户已删除数据工厂或包含数据工厂的资源组。 他想知道如何恢复已删除的数据工厂。

#### <a name="cause"></a>原因

仅当客户配置了源代码管理配置（DevOps 或 Git），才可以恢复数据工厂。 这会引入所有最新的已发布资源，并且不会恢复未发布的管道、数据集和链接服务。

如果没有源代码管理，则不可能从后端恢复已删除的数据工厂，因为一旦服务收到删除的命令，就会删除该实例，并且不会存储任何备份。

#### <a name="resolution"></a>解决方法

若要恢复包含源代码管理的已删除数据工厂，请参阅以下步骤：

 * 创建新的 Azure 数据工厂。

 * 使用相同的设置重新配置 Git，但请确保将现有数据工厂资源导入所选存储库，然后选择“新建分支”。

 * 创建拉取请求以将更改合并到协作分支并发布。

 * 如果客户在已删除的 ADF 中有自承载集成运行时，则需要在新的 ADF 中创建新的实例，还需要使用获取的新密钥在其本地计算机/VM 上卸载并重新安装该实例。 安装完 IR 后，客户需要将链接服务更改为指向新 IR 并测试连接，否则该服务将失败并显示错误“引用无效”。



## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
