---
title: 将自承载网关部署到 Docker
description: 了解如何将 Azure API 管理的自承载网关组件部署到 Docker
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: danlep
ms.openlocfilehash: 3ef8e0316b6df0b95f2163b6df8ae139ebb8fe6b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580939"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>将 Azure API 管理自承载网关部署到 Docker

本文提供将 Azure API 管理的自承载网关组件部署到 Docker 环境的步骤。

> [!NOTE]
> 在 Docker 中托管自承载网关最适用于评估和开发用例。 建议将 Kubernetes 用于生产用途。 请参阅[此](how-to-deploy-self-hosted-gateway-kubernetes.md)文档以了解如何将自承载网关部署到 Kubernetes。

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)
- 创建 Docker 环境。 [Docker for Desktop](https://www.docker.com/products/docker-desktop) 是用于开发和评估的极佳选项。 有关所有 Docker 版本、其功能以及 Docker 本身的综合文档的信息，请参阅 [Docker 文档](https://docs.docker.com)。
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 自承载网关将打包为基于 x86-64 Linux 的 Docker 容器。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>将自承载网关部署到 Docker

1. 选择“部署和基础结构”下的“网关” 。
2. 选择要部署的网关资源。
3. 选择“部署”。
4. 请注意，“令牌”文本框中已使用默认“过期时间”和“机密密钥”值自动生成了访问令牌  。 如果需要，请在其中一个或两个控件中选择所需的值以生成新令牌。
5. 确保在“部署脚本”下选择“Docker”。 
6. 选择“环境”旁边的“env.conf”链接以下载该文件。 
7. 选择“运行”文本框右侧的“复制”图标，将 Docker 命令复制到剪贴板 。
8. 将该命令粘贴到终端（或命令）窗口。 根据需要调整端口映射和容器名称。 请注意，该命令假定下载的环境文件位于当前目录中。

   ```
   docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```

9. 执行命令。 该命令指示 Docker 环境使用从 Microsoft 容器注册表下载的[容器映像](https://aka.ms/apim/sputnik/dhub)运行容器，并将该容器的 HTTP (8080) 和 HTTPS (8081) 端口映射到主机上的端口 80 和 443。
10. 运行以下命令来检查网关容器是否正在运行：
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. 返回到 Azure 门户，单击“概述”并确认刚刚部署的自承载网关容器正在报告运行状况。

    ![网关状态](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> 使用 `console docker container logs <gateway-name>` 命令查看自承载网关日志的快照。
>
> 使用 `docker container logs --help` 命令查看所有日志查看选项。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)。
* [为自承载网关配置自定义域名](api-management-howto-configure-custom-domain-gateway.md)。
