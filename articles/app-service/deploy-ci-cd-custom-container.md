---
title: 到自定义容器的 CI/CD
description: 在 Azure 应用服务中设置到自定义 Windows 或 Linux 容器的持续部署。
keywords: azure 应用服务, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5c8ccbb385a72a34107f0ad3f10066c641d7fb46
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112296256"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>在 Azure 应用服务中使用自定义容器进行持续部署

在本教程中，通过托管 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)存储库或 [Docker 中心](https://hub.docker.com)为自定义容器映像配置持续部署。

## <a name="1-go-to-deployment-center"></a>1. 转到“部署中心”

在 [Azure 门户](https://portal.azure.com)中，导航到应用服务应用的管理页。

从左侧菜单中单击“部署中心” > “设置”。  

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. 选择部署源

根据具体情况选择部署源：
- 容器注册表可在容器注册表和应用服务之间设置 CI/CD。
- 如果你在 GitHub 中维护容器映像的源代码，则可以使用 GitHub Actions 选项。 当在 GitHub 存储库中提交新内容时，便会触发部署操作，该操作可直接对容器注册表运行 `docker build` 和 `docker push`，然后将应用服务应用更新为运行新映像。 有关详细信息，请参阅[如何在 GitHub Actions 中实现 CI/CD](#how-cicd-works-with-github-actions)。
- 要使用 Azure Pipelines 设置 CI/CD，请参阅[从 Azure Pipelines 部署 Azure Web 应用容器](/azure/devops/pipelines/targets/webapp-on-container-linux)。

> [!NOTE]
> 对于 Docker Compose 应用，请选择“容器注册表”。

如果选择 GitHub Actions，请单击“授权”，并按照授权提示进行操作 。 如果以前已使用 GitHub 进行授权，则可以单击“更改帐户”从其他用户的存储库进行部署。

通过 GitHub 授权 Azure 帐户后，选择要从其部署的“组织”、“存储库”和“分支”   。
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. 配置注册表设置
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. 配置注册表设置

要部署多容器 (Docker Compose) 应用，请在“容器类型”中选择 Docker Compose  。

如果看不到“容器类型”下拉列表，请向上滚动到“源”并选择“容器注册表”   。
::: zone-end

在“注册表源”中，选择容器注册表的位置 。 如果既不是 Azure 容器注册表也不是 Docker Hub，请选择“专用注册表” 。

::: zone pivot="container-linux"
> [!NOTE]
> 如果多容器 (Docker Compose) 应用使用多个专用映像，请确保这些专用映像位于同一专用注册表中，并可使用相同的用户凭据进行访问。 如果多容器应用仅使用公共映像，请选择 Docker Hub，即使某些映像不在 Docker Hub 中 。
::: zone-end  

选择与你的选择匹配的选项卡，执行后续步骤。

# <a name="azure-container-registry"></a>[Azure 容器注册表](#tab/acr)

“注册表”下拉列表显示与你的应用位于同一订阅中的注册表。 选择所需的注册表。

> [!NOTE]
>  - 若要使用托管标识锁定 ACR 访问权限，请遵循以下指南：
>    - [如何将系统分配的托管标识与应用服务和 Azure 容器注册表配合使用](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/use_system-assigned_managed_identities.md)
>    - [如何将用户分配的托管标识与应用服务和 Azure 容器注册表配合使用](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/use_user-assigned_managed_identities.md)
>  - 要从其他订阅中的注册表进行部署，请改为在“注册表源”中选择“专用注册表”  。
>   

::: zone pivot="container-windows"
选择要部署的映像和标记  。 如果需要，请在启动文件中键入 start up 命令 。 
::: zone-end
::: zone pivot="container-linux"
根据容器类型执行下一步：
- 对于 Docker Compose，请选择专用映像的注册表 。 单击“选择文件”上传 [Docker Compose](https://docs.docker.com/compose/compose-file/) 文件，或直接将 Docker Compose 文件的内容粘贴到 Config 文件   。
- 对于单个容器，请选择要部署的映像和标记   。 如果需要，请在启动文件中键入 start up 命令 。 
::: zone-end

当你启动容器时，应用服务会将启动文件中的字符串追加到 [`docker run` 命令的末尾（作为 `[COMMAND] [ARG...]` 段）](https://docs.docker.com/engine/reference/run/)。

# <a name="docker-hub"></a>[Docker 中心](#tab/dockerhub)

::: zone pivot="container-windows"
在“存储库访问”中，选择要部署的映像是公共的还是专用的 。
::: zone-end
::: zone pivot="container-linux"
在“存储库访问”中，选择要部署的映像是公共的还是专用的 。 对于具有一个或多个专用映像的 Docker Compose 应用，请选择“专用” 。
::: zone-end

如果选择专用映像，请指定 Docker 帐户的登录名（用户名）和密码  。

::: zone pivot="container-windows"
在“完整映像名称和标记”中提供映像和标记名称并用 `:` 分隔（例如 `nginx:latest`） 。 如果需要，请在启动文件中键入 start up 命令 。 
::: zone-end
::: zone pivot="container-linux"
根据容器类型执行下一步：
- 对于 Docker Compose，请选择专用映像的注册表 。 单击“选择文件”上传 [Docker Compose](https://docs.docker.com/compose/compose-file/) 文件，或直接将 Docker Compose 文件的内容粘贴到 Config 文件   。
- 对于单个容器，请在“完整映像名称和标记”中提供映像和标记名称并用 `:` 分隔（例如 `nginx:latest`）  。 如果需要，请在启动文件中键入 start up 命令 。 
::: zone-end

当你启动容器时，应用服务会将启动文件中的字符串追加到 [`docker run` 命令的末尾（作为 `[COMMAND] [ARG...]` 段）](https://docs.docker.com/engine/reference/run/)。

# <a name="private-registry"></a>[专用注册表](#tab/private)

在“服务器 URL”中，键入服务器的 URL（以 https:// 开头）  。

在“登录名”和“密码”中，键入专用注册表的登录凭据  。

::: zone pivot="container-windows"
在“完整映像名称和标记”中提供映像和标记名称并用 `:` 分隔（例如 `nginx:latest`） 。 如果需要，请在启动文件中键入 start up 命令 。 
::: zone-end
::: zone pivot="container-linux"
根据容器类型执行下一步：
- 对于 Docker Compose，请选择专用映像的注册表 。 单击“选择文件”上传 [Docker Compose](https://docs.docker.com/compose/compose-file/) 文件，或直接将 Docker Compose 文件的内容粘贴到 Config 文件   。
- 对于单个容器，请在“完整映像名称和标记”中提供映像和标记名称并用 `:` 分隔（例如 `nginx:latest`）  。 如果需要，请在启动文件中键入 start up 命令 。 
::: zone-end

当你启动容器时，应用服务会将启动文件中的字符串追加到 [`docker run` 命令的末尾（作为 `[COMMAND] [ARG...]` 段）](https://docs.docker.com/engine/reference/run/)。

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. 启用 CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. 启用 CI/CD
::: zone-end

应用服务支持 CI/CD 与 Azure 容器注册表和 Docker Hub 集成。 要启用它，请在“持续部署”中选择“启用”  。

::: zone pivot="container-linux"
> [!NOTE]
> 如果在“源”中选择 GitHub Actions，则不会看到此选项，因为 CI/CD 由 GitHub Actions 直接处理 。 相反，你会看到“工作流配置”部分，你可以在其中单击“预览文件”以检查工作流文件  。 Azure 会将此文件提交到选定的 GitHub 源存储库中，以处理生成和部署任务。 有关详细信息，请参阅[如何在 GitHub Actions 中实现 CI/CD](#how-cicd-works-with-github-actions)。
::: zone-end

启用此选项后，应用服务会将 Webhook 添加到 Azure 容器注册表或 Docker Hub 中的存储库。 每当通过 `docker push` 更新你选定的映像时，存储库便会将其发布到此 Webhook。 Webhook 会导致应用服务应用重启并运行 `docker pull` 以获取更新后的映像。

对于其他专用注册表，可以手动或在 CI/CD 管道中将其发布到 Webhook。 在 Webhook URL 中，单击“复制”按钮以获取 Webhook URL  。

::: zone pivot="container-linux"
> [!NOTE]
> 对多容器 (Docker Compose) 应用程序的支持有限： 
> - 对于 Azure 容器注册表，应用服务会在选定的注册表中创建一个范围为该注册表的 Webhook。 通过 `docker push` 命令推送到注册表中任何存储库（包括 Docker Compose 文件未引用的存储库）会触发应用重启。 你可能需要[修改 Webhook](../container-registry/container-registry-webhook.md) 以缩小范围。
> - Docker Hub 不支持注册表级别的 Webhook。 必须手动将 Webhook 添加到 Docker Compose 文件中指定的映像。
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. 保存设置
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. 保存设置
::: zone-end

单击“保存” 。

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>如何在 GitHub Actions 中实现 CI/CD

如果在“源”中选择 GitHub Actions（请参阅[选择部署源](#2-choose-deployment-source)），应用服务会通过以下方式设置 CI/CD ：

- 将 GitHub Actions 工作流文件存储到 GitHub 存储库中，以便处理目标为应用服务的生成和部署任务。
- 添加专用注册表的凭据作为 GitHub 机密。 生成的工作流文件会运行 [Azure/docker-login](https://github.com/Azure/docker-login) 操作以使用专用注册表登录，然后运行 `docker push` 以部署到该位置。
- 添加应用的发布配置文件作为 GitHub 机密。 生成的工作流文件会使用此机密对应用服务进行身份验证，然后运行 [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) 操作以配置更新后的映像，这会触发应用重启以请求更新后的映像。
- 捕获[工作流运行日志](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)中的信息，将其显示在应用的“部署中心”的“日志”选项卡中。

可通过以下方式自定义 GitHub Actions 生成提供程序：

- 在 GitHub 存储库中生成工作流文件后，对该工作流文件进行自定义。 有关详细信息，请参阅 [GitHub Actions 的工作流语法](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)。 只需确保工作流以 [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) 操作结束，即可触发应用重启。
- 如果所选分支受保护，则仍可在不保存配置的情况下预览工作流文件，然后将其与所需的 GitHub 机密一起手动添加到存储库中。 此方法不提供与 Azure 门户的日志集成。
- 请使用 Azure Active Directory 中的[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)进行部署，而不是使用发布配置文件。

#### <a name="authenticate-with-a-service-principal"></a>使用服务主体进行身份验证

此可选配置会将默认身份验证替换为生成的工作流文件中的发布配置文件。

在 [Azure CLI](/cli/azure/) 中使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 命令生成服务主体。 在以下示例中，将 \<subscription-id>、\<group-name> 和 \<app-name> 替换为自己的值  。 保存整个 JSON 输出（包括最上层的 `{}`）供下一步使用。

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> 为了安全起见，请为服务主体授予所需的最低访问权限。 上一个示例中的范围仅限于特定的应用服务应用而不是整个资源组。

在 [GitHub](https://github.com/) 中，浏览到存储库，然后选择“设置”>“机密”>“添加新机密”  。 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS`。

在部署中心生成的工作流文件中，使用如下所示的代码来修改 `azure/webapps-deploy` 步骤 ：

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>使用 CLI 进行自动化

要配置容器注册表和 Docker 映像，请运行 [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set)。

# <a name="azure-container-registry"></a>[Azure 容器注册表](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker 中心](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[专用注册表](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
要配置多容器 (Docker Compose) 应用，请在本地准备 Docker Compose 文件，然后使用 `--multicontainer-config-file` 参数运行 [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) 。 如果 Docker Compose 文件包含专用映像，请添加 `--docker-registry-server-*` 参数，如前面的示例中所示。

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

要将 CI/CD 从容器注册表配置到你的应用，请使用 `--enable-cd` 参数运行 [az webapp deployment container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config)。 该命令将输出 Webhook URL，但必须在单独的步骤中在注册表中手动创建 Webhook。 以下示例在应用上启用 CI/CD，然后使用输出中的 Webhook URL 在 Azure 容器注册表中创建 Webhook。

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>更多资源

* [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 应用服务中创建 .NET Core Web 应用](quickstart-dotnetcore.md)
* [快速入门：在应用服务上运行自定义容器](quickstart-custom-container.md)
* [Linux 上的应用服务常见问题](faq-app-service-linux.yml)
* [配置自定义容器](configure-custom-container.md)
* [要部署到 Azure 的操作工作流](https://github.com/Azure/actions-workflow-samples)
