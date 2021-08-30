---
title: 支持的内容格式
description: 了解 Azure 容器注册表支持的内容格式，其中包括与 Docker 兼容的容器映像、Helm 图表、OCI 映像和 OCI 项目。
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 1203bc8cbebfd59ef28f03d19e939b73d707a3f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438875"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure 容器注册表中支持的内容格式

使用 Azure 容器注册表中的专用存储库来管理以下内容格式之一。 

## <a name="docker-compatible-container-images"></a>兼容 Docker 的容器映像

支持以下 Docker 容器映像格式：

* [Docker 映像清单 V2，架构 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像清单 V2，架构 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 包括允许注册表在单个 `image:tag` 引用下存储[多体系结构映像](push-multi-architecture-images.md)的清单列表

## <a name="oci-images"></a>OCI 映像

Azure 容器注册表支持符合[开放容器计划 (OCI) 映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)（包括可选的[映像索引](https://github.com/opencontainers/image-spec/blob/master/image-index.md)规范）的映像。 打包格式包括[奇点映像格式 (SIF)](https://github.com/sylabs/sif)。

## <a name="oci-artifacts"></a>OCI 项目

Azure 容器注册表支持 [OCI 分发规范](https://github.com/opencontainers/distribution-spec)，这是一个独立于供应商、与云无关的规范，用于存储、共享、保护和部署容器映像和其他内容类型（项目）。 除了容器映像外，该规范还允许注册表存储各种不同的项目。 可以使用适合于项目的工具来推送和拉取项目。 有关示例，请参阅：

* [使用 Azure 容器注册表推送和拉取 OCI 项目](container-registry-oci-artifacts.md)
* [将 Helm 图表推送和提取到 Azure 容器注册表](container-registry-helm-repos.md)

若要详细了解 OCI 项目，请参阅 GitHub 上的 [OCI 注册表即存储 (ORAS)](https://github.com/deislabs/oras) 存储库和 [OCI 项目](https://github.com/opencontainers/artifacts)存储库。

## <a name="helm-charts"></a>Helm 图表

Azure 容器注册表可以承载 [Helm 图表](https://helm.sh/)的存储库，该存储库是用来快速为 Kubernetes 管理和部署应用程序的一种打包格式。 建议使用 [Helm 客户端](https://docs.helm.sh/using_helm/#installing-helm)版本 3。 请参阅[将 Helm 图表推送和提取到 Azure 容器注册表](container-registry-helm-repos.md)。

## <a name="next-steps"></a>后续步骤

* 请参阅如何使用 Azure 容器注册表[推送和拉取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 任务](container-registry-tasks-overview.md)来生成并测试容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 以 OCI 格式生成并打包容器。

* 设置 Azure 容器注册表中承载的 [Helm 存储库](container-registry-helm-repos.md)。 


