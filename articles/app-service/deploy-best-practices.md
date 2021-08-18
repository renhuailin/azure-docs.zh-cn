---
title: 部署最佳做法
description: 了解部署到 Azure 应用服务的关键机制。 查找特定于语言的建议和其他注意事项。
keywords: Azure 应用服务, Web 应用, 部署, 部署, 管道, 生成
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 10fe758bf11f0452ef07d30329d7d48914fbb4bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737220"
---
# <a name="deployment-best-practices"></a>部署最佳做法

每个开发团队都有独特的要求，因此很难做到在任何云服务上都能实现高效的部署管道。 本文介绍了部署到应用服务的三个主要组件：部署源、生成管道和部署机制。 本文还提供了特定语言堆栈的一些最佳做法和提示。

## <a name="deployment-components"></a>部署组件

### <a name="deployment-source"></a>部署源

部署源是指应用程序代码的位置。 对于生产应用，部署源通常是由版本控制软件（例如 [GitHub、BitBucket 或 Azure Repos](deploy-continuous-deployment.md)）托管的存储库。 对于开发和测试方案，部署源可能是[本地计算机上的一个项目](deploy-local-git.md)。 应用服务还支持 [OneDrive 和 Dropbox 文件夹](deploy-content-sync.md)作为部署源。 尽管在云文件夹中可以轻松地开始使用应用服务，但对于企业级的生产应用程序，通常不建议使用此部署源。 

### <a name="build-pipeline"></a>生成管道

确定了部署源后，下一步就是选择生成管道。 生成管道将读取来自部署源的源代码并执行一系列步骤（例如，编译代码、缩小 HTML 和 JavaScript、运行测试以及打包组件等），以使应用程序处于可运行状态。 生成管道所执行的具体命令取决于你的语言堆栈。 这些操作可以在诸如 Azure Pipelines 之类的生成服务器上执行，也可以在本地执行。

### <a name="deployment-mechanism"></a>部署机制

部署机制是指将生成的应用程序放入 Web 应用程序的 */home/site/wwwroot* 目录中的操作。 */wwwroot* 目录是 Web 应用的所有实例一起共享的已装载存储位置。 当部署机制将应用程序放入此目录时，实例会收到要同步新文件的通知。 应用服务支持以下部署机制：

- Kudu 终结点：[Kudu](https://github.com/projectkudu/kudu/wiki) 是一种开放源代码开发人员生产力工具，在 Windows 应用服务中是作为单独进程运行，在 Linux 应用服务中是作为第二个容器来运行。 Kudu 可以处理持续部署，并提供用于部署的 HTTP 终结点，例如 zipdeploy。
- FTP 和 WebDeploy：通过使用[站点或用户凭据](deploy-configure-credentials.md)，可以[通过 FTP](deploy-ftp.md) 或 WebDeploy 上传文件。 这些机制都无需经过 Kudu。  

诸如 Azure Pipelines、Jenkins 和编辑器插件之类的部署工具将使用这些部署机制之一。

## <a name="use-deployment-slots"></a>使用部署槽位

在部署新的生产版本时，应尽可能使用[部署槽位](deploy-staging-slots.md)。 如果使用标准应用服务计划层或更好的层级，则可以将应用部署到过渡环境、验证更改并执行冒烟测试。 准备就绪后，即可交换暂存和生产槽位。 交换操作将根据生产规模所需预热必要的辅助角色实例，从而缩短故障时间。

### <a name="continuously-deploy-code"></a>持续部署代码

如果项目中指定了用于测试、QA 和过渡的分支，则每个分支都应持续部署到过渡槽位。 （这称为 [Gitflow 设计](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)。）这使利益干系人可以轻松地评估和测试已部署的分支。 

对于生产槽位，不得启用持续部署。 而是应将生产分支（通常是主分支）部署到非生产槽位上。 准备好发布基本分支后，将其交换到生产槽位中。 交换到生产槽位（而不是部署到生产槽位）可以防止故障停机，并且允许通过再次交换来回滚更改。 

![此图显示了开发、过渡和主分支与它们所部署到的槽位之间的流动。](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>持续部署容器

对于 Docker 或其他容器注册表中的自定义容器，请将映像部署到过渡槽位后交换到生产槽位中，以避免故障停机。 这一自动过程比代码部署更复杂，因为必须将映像推送到容器注册表上，并更新 Webapp 上的映像标记。

对于想要部署到槽位的每个分支，请设置自动化过程，以在每次提交到分支时执行以下操作。

1. **生成并标记映像**。 作为生成管道的一部分，使用 Git 提交 ID、时间戳或其他可识别信息来标记映像。 最好不要使用默认的“latest”标记。 否则将很难追溯到当前部署的代码，导致调试难度加大。
1. **推送已标记的映像**。 生成并标记了映像后，管道会将该映像推送到容器注册表。 下一步，部署槽位将从容器注册表中拉取已标记的映像。
1. **使用新的映像标记更新部署槽位**。 此属性更新时，站点将自动重启并拉取新的容器映像。

![槽位使用情况的可视化效果](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

下面是常用的自动化框架示例。

### <a name="use-azure-devops"></a>使用 Azure DevOps

应用服务通过部署中心为容器提供[内置的持续交付](deploy-continuous-deployment.md)。 导航到 [Azure 门户](https://portal.azure.com/)中的应用，然后在“部署”下选择“部署中心”。 按照说明选择存储库和分支。 这将配置 DevOps 生成和发布管道，以便在将新提交推送到所选分支时，自动生成、标记和部署容器。

### <a name="use-github-actions"></a>使用 GitHub Actions

还可以[通过 GitHub Actions](./deploy-ci-cd-custom-container.md) 完成自动化容器部署。  以下工作流文件将生成和标记带有提交 ID 的容器，并将其推送到容器注册表，并使用新的映像标记来更新指定的站点槽位。

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>使用其他自动化提供程序

前面列出的步骤同样适用于其他自动化实用程序，例如 CircleCI 或 Travis CI。 但是在最后一步中，需要使用 Azure CLI 更新包含了新的映像标记的部署槽位。 若要在自动化脚本中使用 Azure CLI，请使用以下命令生成服务主体。

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

在脚本中，使用 `az login --service-principal` 进行登录，提供主体的信息。 然后，可以使用 `az webapp config container set` 来设置容器名称、标记、注册表 URL 和注册表密码。 下面是一些有用的链接，有助于构造容器 CI 过程。

- [如何登录到 Circle CI 上的 Azure CLI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>特定于语言的注意事项

### <a name="java"></a>Java

使用 Kudu [zipdeploy/](deploy-zip.md) API 部署 JAR 应用程序，并使用 [wardeploy/](deploy-zip.md#deploy-war-file) 部署 WAR 应用。 如果使用的是 Jenkins，则可以在部署阶段直接使用这些 API。 有关详细信息，请参阅[此文章](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli)。

### <a name="node"></a>节点

默认情况下，Kudu 会执行 Node 应用程序的生成步骤 (`npm install`)。 如果使用的是生成服务（例如 Azure DevOps），则不需要 Kudu 生成。 若要禁用 Kudu 生成，请创建一个值为 `false` 的应用设置 `SCM_DO_BUILD_DURING_DEPLOYMENT`。

### <a name="net"></a>.NET 

默认情况下，Kudu 会执行 Node 应用程序的生成步骤 (`dotnet build`)。 如果使用的是生成服务（例如 Azure DevOps），则不需要 Kudu 生成。 若要禁用 Kudu 生成，请创建一个值为 `false` 的应用设置 `SCM_DO_BUILD_DURING_DEPLOYMENT`。

## <a name="other-deployment-considerations"></a>其他部署注意事项

### <a name="local-cache"></a>本地缓存

Azure 应用服务内容存储在 Azure 存储中，作为内容共享持续提供。 但是，某些应用只需要高性能的只读内容存储，就能以高可用性运行。 这些应用受益于[本地缓存](overview-local-cache.md)的使用。 对于内容管理站点（例如 WordPress），不建议使用本地缓存。

本地缓存应始终与[部署槽位](deploy-staging-slots.md)一起使用，以避免故障停机。 有关将这些功能一起使用的信息，请参阅[此部分](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或内存

如果应用服务计划占用了超过 90% 的可用 CPU 或内存，则基础虚拟机在处理部署时可能会遇到问题。 发生这种情况时，请暂时纵向扩展实例计数以执行部署。 完成部署后，可以将实例计数返回至以前的值。

有关最佳做法的详细信息，请访问[应用服务诊断](./overview-diagnostics.md)，了解特定于资源的可操作最佳做法。

- 在 [Azure 门户](https://portal.azure.com)中导航到 Web 应用。
- 在左侧导航栏中单击“诊断并解决问题”，打开“应用服务诊断”。
- 选择“最佳做法”主页磁贴。
- 单击“针对可用性和性能的最佳做法”或“针对理想配置的最佳做法”，基于这些最佳做法查看应用当前的状态 。

还可以单击此链接，直接打开资源对应的“应用服务诊断”：`https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`。

## <a name="more-resources"></a>更多资源

[环境变量和应用设置参考](reference-app-settings.md)