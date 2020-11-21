---
title: 管理专用容器注册表中的公共内容
description: Azure 容器注册表中的做法和工作流，用于管理来自 Docker 中心和其他公共内容的公共映像的依赖项
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024732"
---
# <a name="manage-public-content-with-azure-container-registry"></a>用 Azure 容器注册表管理公共内容

本文概述了使用本地注册表（如 [Azure 容器注册表](container-registry-intro.md) ）来维护公共内容（如 Docker 中心中的容器映像）的副本的实践和工作流。 


## <a name="risks-with-public-content"></a>公共内容面临的风险

你的环境可能与公共内容（如公共容器映像、 [Helm 图](https://helm.sh/)、 (OPA) 策略或其他 [项目）存在](https://www.openpolicyagent.org/) 依赖关系。 例如，你可以运行 [nginx](https://hub.docker.com/_/nginx) 进行服务路由或 `docker build FROM alpine` 直接从 Docker 中心或其他公共注册表拉取映像。 

如果没有正确的控件，则对公共注册表内容具有依赖关系会对映像开发和部署工作流带来风险。 若要缓解此风险，请在可能的情况下保留公共内容的本地副本。 有关详细信息，请参阅 [开放容器计划博客](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/)。 

## <a name="authenticate-with-docker-hub"></a>通过 Docker 中心进行身份验证

第一步，如果你当前要从 Docker 中心拉取公共映像作为生成或部署工作流的一部分，我们建议你 [使用 Docker 中心帐户进行身份验证](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) ，而不是发出匿名拉取请求。

在频繁执行匿名拉取请求时，可能会看到类似于 `ERROR: toomanyrequests: Too Many Requests.` `You have reached your pull rate limit.` docker 中心或向 docker 中心进行身份验证的 docker 错误，以防出现这些错误。

> [!NOTE]
> 从2020年11月2日起生效， [下载速率限制](https://docs.docker.com/docker-hub/download-rate-limit) 适用于来自 Docker 免费计划帐户的对 docker 中心的匿名请求和经过身份验证的请求，并分别由 IP 地址和 Docker ID 强制执行。 
>
> 估计拉取请求数量时，请考虑在使用云提供商服务或在企业 NAT 后面工作时，会将多个用户作为 IP 地址的子集提供给 Docker 中心。 向 Docker 中心发出的请求添加 Docker 付费帐户身份验证将避免由于速率限制阻止而导致的潜在服务中断。
>
> 有关详细信息，请参阅 [docker 定价和订阅](https://www.docker.com/pricing) 以及 [docker 服务条款](https://www.docker.com/legal/docker-terms-service)。

### <a name="docker-hub-access-token"></a>Docker 中心访问令牌

当向 Docker 中心进行身份验证时，docker 中心支持 [个人访问令牌](https://docs.docker.com/docker-hub/access-tokens/) 作为 docker 密码的替代项。 建议将令牌用于从 Docker 中心提取映像的自动服务。 你可以为不同的用户或服务生成多个令牌，并在不再需要时撤销令牌。

若要使用令牌进行身份验证 `docker login` ，请在命令行中省略密码。 当系统提示输入密码时，请改为输入令牌。 如果为 Docker 中心帐户启用了双因素身份验证，则在从 Docker CLI 登录时必须使用个人访问令牌。

### <a name="authenticate-from-azure-services"></a>从 Azure 服务进行身份验证

某些 Azure 服务（包括应用服务和 Azure 容器实例）支持从公共注册表（例如用于容器部署的 Docker 中心）提取映像。 如果需要从 Docker 中心部署映像，建议使用 Docker 中心帐户将设置配置为进行身份验证。 示例:

**应用服务**

* **映像源**： Docker 中心
* **存储库访问**：专用
* **登录名**： \<Docker Hub username>
* **密码**：\<Docker Hub token>

有关详细信息，请参阅 [应用服务上的 Docker 中心经过身份验证的](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)请求。

**Azure 容器实例**

* **映像源**： Docker 集线器或其他注册表
* **映像类型**：专用
* **映像注册表登录服务器**： docker.io
* **映像注册表用户名**： \<Docker Hub username>
* **映像注册表密码**： \<Docker Hub token>
* **Image**： docker.io/ \<repo name\> ：\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>将映像导入到 Azure 容器注册表
 
若要开始管理公共映像的副本，可以创建 Azure 容器注册表（如果尚未安装）。 使用 [Azure CLI](container-registry-get-started-azure-cli.md)、 [Azure 门户](container-registry-get-started-portal.md)、 [Azure PowerShell](container-registry-get-started-powershell.md)或其他工具创建注册表。 

建议使用一次性步骤，将基本映像和其他公共内容 [导入](container-registry-import-images.md) Azure 容器注册表。 Azure CLI 中的 [az acr import](/cli/azure/acr#az_acr_import) 命令支持从公共注册表（如 Docker 中心和 Microsoft 容器注册表以及其他专用容器注册表）导入映像。 

`az acr import` 不需要本地 Docker 安装。 您可以使用 Azure CLI 的本地安装或直接在 Azure Cloud Shell 中运行它。 它支持任何 OS 类型、多体系结构图像或 OCI 项目（如 Helm 图）的图像。

例如：

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

根据组织的需要，可以将导入到专用注册表或共享注册表中的存储库。

## <a name="automate-application-image-updates"></a>自动执行应用程序映像更新

应用程序映像的开发人员应确保其代码在其控制下引用本地内容。 例如， `Docker FROM` Dockerfile 中的语句应引用专用基本映像注册表中的映像，而不是公共注册表。 

扩展图像导入，设置 [Azure 容器注册表任务](container-registry-tasks-overview.md) ，以便在更新基础映像时自动生成应用程序映像。 自动生成任务可以同时跟踪 [基本映像更新](container-registry-tasks-base-images.md) 和 [源代码更新](container-registry-tasks-overview.md#trigger-task-on-source-code-update)。

有关详细示例，请参阅 [如何使用 Azure 容器注册表任务来使用和维护公共内容](tasks-consume-public-content.md)。 

> [!NOTE]
> 单个预配置任务可以自动重建引用从属基础映像的每个应用程序映像。 
 
## <a name="next-steps"></a>后续步骤
 
* 了解有关 [ACR 任务](container-registry-tasks-overview.md) 的详细信息，以便在 Azure 中生成、运行、推送和修补容器映像。
* 请参阅 [如何使用和维护 Azure 容器注册表任务中的公共内容](tasks-consume-public-content.md) ，以获取自动指导工作流，将基本映像更新到你的环境。 
* 请参阅 [ACR 任务教程](container-registry-tutorial-quick-task.md) ，了解有关自动执行映像生成和更新的更多示例。
