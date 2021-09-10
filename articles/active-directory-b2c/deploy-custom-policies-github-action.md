---
title: 使用 GitHub Actions 部署自定义策略
titleSuffix: Azure AD B2C
description: 了解如何使用 GitHub Actions 在 CI/CD 管道中部署 Azure AD B2C 自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 956803f14d9df49e99411eae2cb6a76db7636b03
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187795"
---
# <a name="deploy-custom-policies-with-github-actions"></a>使用 GitHub Actions 部署自定义策略

[GitHub Actions](https://docs.github.com/actions/quickstart) 支持直接在 GitHub 存储库中创建自定义持续集成 (CI) 和持续部署 (CD) 工作流。 本文介绍如何使用 GitHub Actions 自动部署 Azure Active Directory B2C (Azure AD B2C) [自定义策略](user-flow-overview.md)。

若要自动执行自定义策略部署过程，请使用[用于部署 Azure AD B2C 自定义策略的 GitHub Action](https://github.com/marketplace/actions/deploy-azure-ad-b2c-custom-policy)。 此 GitHub Action 由 [Azure AD B2C 社区](https://github.com/azure-ad-b2c/deploy-trustframework-policy)开发。 

此操作使用 [Microsoft Graph API](/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true) 将Azure AD B2C 自定义策略部署到 Azure AD B2C 租户中。 如果租户中尚不存在该策略，将创建该策略。 如果存在，将替换该策略。

> [!IMPORTANT]
> 借助 Azure Pipelines 管理 Azure AD B2C 自定义策略目前使用 Microsoft Graph API `/beta` 终结点上提供的预览操作。 不支持在生产应用程序中使用这些 API。 有关详细信息，请参阅 [Microsoft Graph REST API beta 版本终结点参考](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)。

## <a name="prerequisites"></a>先决条件

* 完成 [Active Directory B2C 中的自定义策略入门](tutorial-create-user-flows.md)中的步骤。
* 如果尚未创建 GitHub 存储库，[请创建一个](https://docs.github.com/en/get-started/quickstart/create-a-repo)。  

## <a name="select-a-custom-policies-folder"></a>选择自定义策略文件夹

你的 GitHub 存储库可以包含所有 Azure AD B2C 策略文件和其他资产。 在存储库的根目录中，创建或选择包含自定义策略的现有文件夹。 

例如，选择名为“policies”的文件夹。 将 Azure AD B2C 自定义策略文件添加到“policies”文件夹。 然后提交更改。

请勿推送更改。 稍后将在设置部署工作流后执行此操作。

## <a name="register-a-microsoft-graph-application"></a>注册 Microsoft Graph 应用程序

若要允许 GitHub Action 与 [Microsoft Graph API](microsoft-graph-operations.md) 交互，请在 Azure AD B2C 租户中创建应用程序注册。 如果你尚未进行这一步，请[注册 Microsoft Graph 应用程序](microsoft-graph-get-started.md)。

若要让 GitHub Action 访问 Microsoft Graph 中的数据，请向已注册的应用程序授予相关[应用程序权限](/graph/permissions-reference)。 在应用程序注册的“API 权限”内授予了“Microsoft Graph” > “策略” > “Policy.ReadWrite.TrustFramework”权限。

## <a name="create-a-github-encrypted-secret"></a>创建 GitHub 加密的机密

GitHub 机密是指在组织、存储库或存储库环境中创建的加密环境变量。 在此步骤中，将存储之前在[注册 MS Graph 应用程序](#register-a-microsoft-graph-application)步骤中注册的应用程序的应用程序机密。

用于部署 Azure AD B2C 自定义策略的 GitHub Action 使用机密来获取用于与 Microsoft Graph API 交互的访问令牌。 有关详细信息，请参阅[为存储库创建加密机密](https://docs.github.com/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)。

若要创建 GitHub 机密，请按这些步骤操作：

1. 在 GitHub 上，导航到存储库的主页。
1. 在存储库名称下，选择“设置”。
1. 在左侧边栏中，选择“机密”。
1. 选择“新建存储库机密”。
1. 对于“名称”，键入“ClientSecret”。
1. 对于“值”，输入之前创建的应用程序机密。
1. 选择“添加机密”。

## <a name="create-a-github-workflow"></a>创建 GitHub 工作流

GitHub 工作流是你添加到存储库中的自动化流程。 工作流由一个或多个作业组成，可进行计划或通过事件触发。 在此步骤中，你将创建部署自定义策略的工作流。

若要创建工作流，请执行下列步骤：

1. 在 GitHub 上，导航到存储库的主页。
1. 在存储库名称下，选择“操作”。

   ![显示 GitHub Actions 选项卡的屏幕截图](media/deploy-custom-policies-github-action/github-actions-tab.png)

1. 如果之前未配置工作流，请选择“自行设置工作流”。 否则，请选择“新建工作流”。

   ![显示如何新建工作流的屏幕截图](media/deploy-custom-policies-github-action/set-up-a-workflow.png)

1. GitHub 在 `.github/workflows` 文件夹中创建名为 `main.yml` 的工作流文件。 此文件包含有关工作流的信息，包括 Azure AD B2C 环境以及要部署的自定义策略。 在 GitHub Web 编辑器中，添加以下 YAML 代码：

    ```yml
    on: push

    env:
      clientId: 00000000-0000-0000-0000-000000000000
      tenant: your-tenant.onmicrosoft.com
    
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
    
        - name: 'Upload TrustFrameworkBase Policy'
          uses: azure-ad-b2c/deploy-trustframework-policy@v3
          with:
            folder: "./Policies"
            files: "TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
            tenant: ${{ env.tenant }}
            clientId: ${{ env.clientId }}
            clientSecret: ${{ secrets.clientSecret }}
    ```

1.  更新 YAML 文件的以下属性：

    | 部分| 名称 | 值 |
    | ---- | ----- |----- |
    | `env` | `clientId` | 在[注册 MS Graph 应用程序](#register-a-microsoft-graph-application)步骤中注册的应用程序的“应用程序(客户端) ID”。 |
    |`env`| `tenant` | Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)（例如 contoso.onmicrosoft.com）。 |
    | `with`| `folder`| 存储自定义策略文件的文件夹，例如 `./Policies`。|
    | `with`| `files` | 要部署的策略文件的以逗号分隔的列表，例如 `TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`。|
    
    > [!IMPORTANT]
    > 运行代理并上传策略文件时，请确保按正确顺序上传：
    >
    > 1. TrustFrameworkBase.xml
    > 1. TrustFrameworkExtensions.xml
    > 1. SignUpOrSignin.xml
    > 1. ProfileEdit.xml
    > 1. PasswordReset.xml

1. 选择“开始提交”。
1. 在“提交消息”字段下，指示是将提交添加到当前分支还是添加到新分支。 选择“提交新文件”或“建议新文件”以创建拉取请求。

## <a name="test-your-workflow"></a>测试工作流

若要测试创建的工作流，请推送自定义策略的更改。 作业开始运行后，可以看到运行进度的可视化图，并在 GitHub 上查看每个步骤的活动。

1. 在 GitHub 上，导航到存储库的主页。
1. 在存储库名称下，选择“操作”。
1. 在左侧边栏中，选择创建的工作流。
1. 在“工作流运行”下，选择想要查看的运行的名称。

   ![显示如何选择工作流活动的屏幕截图](media/deploy-custom-policies-github-action/workflow-report.png)

1. 在“作业”或可视化图中，选择要查看的作业。
1. 查看每个步骤的结果。 以下屏幕截图演示了“上传自定义策略”步骤日志。
 
   ![上传自定义策略步骤日志](media/deploy-custom-policies-github-action/job-activity.png)


## <a name="optional-schedule-your-workflow"></a>可选：计划工作流

创建的工作流由[推送](https://docs.github.com/actions/reference/events-that-trigger-workflows#push)事件触发。 如果需要，可以选择另一个事件来触发工作流，例如[拉取请求](https://docs.github.com/actions/reference/events-that-trigger-workflows#pull_request)。

还可使用 [POSIX cron 语法](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07)将工作流计划为在特定的 UTC 时间运行。 通过计划事件，可以在计划的时间触发工作流。 有关详细信息，请参阅[计划事件](https://docs.github.com/actions/reference/events-that-trigger-workflows#scheduled-events)。

以下示例在每天 5:30 和 17:30 UTC 触发工作流：

```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

若要编辑工作流，请执行以下操作：

1. 在 GitHub 上，导航到存储库的主页。
1. 在存储库名称下，选择“操作”。
1. 在左侧边栏中，选择创建的工作流。
1. 在“工作流运行”下，选择想要查看的运行的名称。
1. 从菜单中，选择三个点“...”，然后选择“查看工作流文件”。

   ![显示如何查看服务的屏幕截图](media/deploy-custom-policies-github-action/edit-workflow.png)
   
1. 在 GitHub Web 编辑器中，选择“编辑”。
1. 将 `on: push` 更改为以上示例。
1. 提交更改。

## <a name="next-steps"></a>后续步骤

- 了解如何配置[触发工作流的事件](https://docs.github.com/actions/reference/events-that-trigger-workflows)


