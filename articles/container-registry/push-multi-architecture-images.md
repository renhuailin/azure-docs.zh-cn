---
title: 注册表中的多体系结构映像
description: 使用 Azure 容器注册表生成、导入、存储和部署多体系结构 (多个) 映像
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: f8467cd3108ae4faea9ecb4c9d9ae339f476c311
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012300"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Azure 容器注册表中的多体系结构映像

本文介绍 *多体系结构* (*多个*) 的映像，以及如何使用 Azure 容器注册表功能来帮助创建、存储和使用它们。 

多个形象的图像是一种容器映像，可将不同体系结构的变体和不同操作系统组合在一起。 当运行包含多体系结构支持的映像时，容器客户端将自动选择与您的操作系统和体系结构匹配的映像变体。

## <a name="manifests-and-manifest-lists"></a>清单和清单列表

多个三维图像基于图像清单和清单列表。

### <a name="manifest"></a>file:///

每个容器映像都由一个 [清单](container-registry-concepts.md#manifest)表示。 清单是一个 JSON 文件，用于唯一标识该映像，并引用其层及其相应大小。 

Linux 映像的基本清单 `hello-world` 类似于以下内容：

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
你可以使用 Azure CLI 中的 Azure 门户或工具（如 [az acr 存储库 show-](/cli/azure/acr/repository#az_acr_repository_show_manifests) manifest 命令）查看 Azure 容器注册表中的清单。

### <a name="manifest-list"></a>清单列表

多形象映像的 *清单列表* (更常见的 OCI 图像的 [映像索引](https://github.com/opencontainers/image-spec/blob/master/image-index.md)) 是图像 (索引) 的集合，并通过指定一个或多个图像名称创建一个。 其中包含有关每个映像的详细信息，如支持的操作系统和体系结构、大小和清单摘要。 清单列表的使用方式与和命令中的映像名称相同 `docker pull` `docker run` 。 

`docker`CLI 使用[docker 清单](https://docs.docker.com/engine/reference/commandline/manifest/)命令管理清单和清单列表。

> [!NOTE]
> 当前， `docker manifest` 命令和子命令为试验性。 有关使用实验性命令的详细信息，请参阅 Docker 文档。

您可以使用命令查看清单列表 `docker manifest inspect` 。 下面是多形象映像的输出 `mcr.microsoft.com/mcr/hello-world:latest` ，其中包含三个清单：两个适用于 LINUX OS 体系结构，一个用于 Windows 体系结构。
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

在 Azure 容器注册表中存储多个 "清单" 清单时，还可以使用 "Azure 门户" 或 " [az acr repository show-清单](/cli/azure/acr/repository#az_acr_repository_how_manifests) " 命令等工具查看清单列表。

## <a name="import-a-multi-arch-image"></a>导入多个图像 

可以使用 [az acr import](/cli/azure/acr#az_acr_import) 命令将现有的多形象图像导入到 Azure 容器注册表。 图像导入语法与单体系结构映像相同。 与导入单一体系结构映像类似，导入多个图像不使用 Docker 命令。 

有关详细信息，请参阅 [将容器映像导入到容器注册表](container-registry-import-images.md)。

## <a name="push-a-multi-arch-image"></a>推送多个三维图像

如果有生成工作流来为不同的体系结构创建容器映像，请按照以下步骤将多个用户的映像推送到 Azure 容器注册表。

1. 标记每个特定于体系结构的映像并将其推送到容器注册表。 下面的示例假定两个 Linux 体系结构： arm64 和 amd64。 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. 运行 `docker manifest create` 创建清单列表，将前面的图像合并成多个图像。

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. 使用以下内容将清单推送到容器注册表 `docker manifest push` ：

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. 使用 `docker manifest inspect` 命令查看清单列表。 命令输出的一个示例如前一节所示。

将多形象清单推送到注册表后，处理多个图像的方式与使用单体系结构映像的方式相同。 例如，使用提取映像 `docker pull` ，并使用 [az acr 存储库](/cli/azure/acr/repository#az_acr_repository) 命令查看图像的标记、清单和其他属性。

## <a name="build-and-push-a-multi-arch-image"></a>构建并推送多个三维图像

使用 [ACR 任务](container-registry-tasks-overview.md)的功能，可以生成多个三维图像并将其推送到 Azure 容器注册表。 例如，在 YAML 文件中定义一个 [多步骤任务](container-registry-tasks-multi-step.md) ，该任务将生成 Linux 多形象映像。

下面的示例假定你对两个体系结构（arm64 和 amd64）使用了单独的 Dockerfile。 它生成并推送特定于体系结构的映像，然后创建并推送包含标记的多个建筑清单 `latest` ：

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) 为不同的体系结构构建容器映像。
* 了解如何使用试验性 Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) 插件构建多平台映像。

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
