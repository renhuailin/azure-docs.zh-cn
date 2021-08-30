---
title: 在专用容器注册表中管理公共内容
description: Azure 容器注册表中的一些做法和工作流，用于管理对来自 Docker Hub 的公共映像和其他公共内容的依赖
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 06/17/2021
ms.openlocfilehash: 806cb6b49824db65744bc653c6c467c7a816a21e
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983691"
---
# <a name="manage-public-content-with-azure-container-registry"></a>通过 Azure 容器注册表管理公共内容

本文概述了使用本地注册表（例如 [Azure 容器注册表](container-registry-intro.md)）维护公共内容副本（例如 Docker Hub 中的容器映像）的做法和工作流。 


## <a name="risks-with-public-content"></a>公共内容面临的风险

你的环境可能依赖于公共内容，例如公共容器映像、[Helm 图表](https://helm.sh/)、[Open Policy Agent](https://www.openpolicyagent.org/) (OPA) 策略或其他项目。 例如，你可以通过直接从 Docker Hub 或其他公共注册表拉取映像来运行适用于服务路由的 [nginx](https://hub.docker.com/_/nginx) 或运行 `docker build FROM alpine`。 

如果没有正确的控制措施，则依赖于公共注册表内容可能会为映像开发和部署工作流带来风险。 若要缓解风险，请在可能的情况下保留公共内容的本地副本。 有关详细信息，请参阅[开放容器计划博客](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/)。 

## <a name="authenticate-with-docker-hub"></a>使用 Docker Hub 进行身份验证

首先，如果你当前在构建或部署工作流时从 Docker Hub 拉取公共映像，建议你[使用 Docker Hub 帐户进行身份验证](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests)，而不是发出匿名拉取请求。

频繁发出匿名拉取请求时，你可能会看到 Docker 错误，类似于 `ERROR: toomanyrequests: Too Many Requests.` 或 `You have reached your pull rate limit.`。请对 Docker Hub 进行身份验证以避免这些错误。

> [!NOTE]
> 自 2020 年 11 月 2 日起，[下载速率限制](https://docs.docker.com/docker-hub/download-rate-limit)应用于 Docker 免费计划帐户对 Docker Hub 发出的匿名请求和经身份验证的请求，并且分别按 IP 地址和 Docker ID 强制实施。 
>
> 估计你的拉取请求数量时，请注意，当使用云提供商服务或在公司 NAT 后工作时，多个用户将作为一个 IP 地址子集一起呈现给 Docker Hub。 在对 Docker Hub 发出的请求中添加 Docker 付费帐户身份验证会避免由于速率限制限流而导致的潜在服务中断。
>
> 有关详细信息，请参阅 [Docker 定价和订阅](https://www.docker.com/pricing)和 [Docker 服务条款](https://www.docker.com/legal/docker-terms-service)。

### <a name="docker-hub-access-token"></a>Docker Hub 访问令牌

当向 Docker Hub 进行身份验证时，Docker Hub 支持使用[个人访问令牌](https://docs.docker.com/docker-hub/access-tokens/)作为 Docker 密码的替代项。 建议将令牌用于从 Docker Hub 拉取映像的自动化服务。 你可以为不同的用户或服务生成多个令牌，并在不再需要时撤销令牌。

若要使用令牌通过 `docker login` 进行身份验证，请在命令行上省略密码。 当提示输入密码时，改为输入令牌。 如果为你的 Docker Hub 帐户启用了双重身份验证，则在从 Docker CLI 登录时必须使用个人访问令牌。

### <a name="authenticate-from-azure-services"></a>从 Azure 服务进行身份验证

多个 Azure 服务（包括应用服务和 Azure 容器实例）支持从公共注册表（例如 Docker Hub）拉取映像来进行容器部署。 如果需要从 Docker Hub 部署映像，建议你配置设置来使用 Docker Hub 帐户进行身份验证。 示例：

**应用服务**

* **映像源**：Docker 中心
* **存储库访问**：专用
* **登录名**：\<Docker Hub username>
* **密码**：\<Docker Hub token>

有关详细信息，请参阅[应用服务上通过 Docker Hub 进行身份验证的拉取](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)。

**Azure 容器实例**

* **映像源**：Docker Hub 或其他注册表
* **映像类型**：专用
* 映像注册表登录服务器：docker.io
* **映像注册表用户名**：\<Docker Hub username>
* **映像注册表密码**：\<Docker Hub token>
* 映像：docker.io/\<repo name\>:\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>将映像导入到 Azure 容器注册表
 
若要开始管理公共映像的副本，你可以创建一个 Azure 容器注册表（如果尚未创建）。 使用 [Azure CLI](container-registry-get-started-azure-cli.md)、[Azure 门户](container-registry-get-started-portal.md)、[Azure PowerShell](container-registry-get-started-powershell.md) 或其他工具创建注册表。 

请将基础映像和其他公共内容[导入](container-registry-import-images.md)到你的 Azure 容器注册表，这是我们为你建议的一次性步骤。 Azure CLI 中的 [az acr import](/cli/azure/acr#az_acr_import) 命令支持从公共注册表（例如 Docker Hub 和 Microsoft Container Registry）和其他专用容器注册表导入映像。 

`az acr import` 不需要本地 Docker 安装。 你可以使用 Azure CLI 的本地安装运行该命令，也可以直接在 Azure 本地 Shell 中运行。 它支持任何 OS 类型的映像、多体系结构映像或 OCI 项目（例如 Helm 图表）。

示例：

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

根据你的组织的需求，你可以将内容导入到专用注册表，或导入到共享注册表中的存储库。

## <a name="update-image-references"></a>更新映像引用

应用程序映像的开发人员应确保其代码引用受其控制的本地内容。

* 更新映像引用以使用专用注册表。 例如，将 Dockerfile 中的 `FROM baseimage:v1` 语句更新为 `FROM myregistry.azurecr.io/mybaseimage:v1`
* 配置凭据或身份验证机制以使用专用注册表。 确切的机制取决于你用于访问注册表的工具以及管理用户访问的方式。
    * 如果使用 Kubernetes 群集或 Azure Kubernetes 服务访问注册表，请参阅[身份验证方案](authenticate-kubernetes-options.md)。
    * 详细了解使用 Azure 容器注册表进行[身份验证的选项](container-registry-authentication.md)。

## <a name="automate-application-image-updates"></a>自动执行应用程序映像更新

在映像导入基础上进行扩展，设置一个 [Azure 容器注册表任务](container-registry-tasks-overview.md)，以在基础映像更新时自动构建应用程序映像。 自动化的构建任务可以同时跟踪[基础映像更新](container-registry-tasks-base-images.md)和[源代码更新](container-registry-tasks-overview.md#trigger-task-on-source-code-update)。

有关详细示例，请参阅[如何使用 Azure 容器注册表任务消耗并维护公共内容](tasks-consume-public-content.md)。 

> [!NOTE]
> 单个预配置的任务可以自动重新构建引用所依赖的基础映像的每个应用程序映像。 
 
## <a name="next-steps"></a>后续步骤
* 详细了解用于在 Azure 中构建、运行、推送和修补容器映像的 [ACR 任务](container-registry-tasks-overview.md)。
* 请参阅[如何使用 Azure 容器注册表任务消耗和维护公共内容](tasks-consume-public-content.md)，了解用于更新环境的基础映像的自动化门控工作流。 
* 请参阅 [ACR 任务教程](container-registry-tutorial-quick-task.md)，了解有关自动构建和更新映像的更多示例。
