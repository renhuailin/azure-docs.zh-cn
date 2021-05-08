---
title: 注册表中的多体系结构映像
description: 使用 Azure 容器注册表生成、导入、存储和部署多体系结构映像
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802448"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Azure 容器注册表中的多体系结构映像

本文介绍了多体系结构 映像，以及如何使用 Azure 容器注册表功能来帮助创建、存储和使用它们。 

多体系结构映像是一种容器映像，可以组合不同体系结构的变体，有时候还可以组合不同操作系统的变体。 当运行支持多体系结构的映像时，容器客户端会自动选择与你的操作系统和体系结构匹配的一个映像变体。

## <a name="manifests-and-manifest-lists"></a>清单和清单列表

多体系结构映像基于映像清单和清单列表。

### <a name="manifest"></a>file:///

每个容器映像都由一个[清单](container-registry-concepts.md#manifest)来表示。 清单是一个 JSON 文件，用于唯一地标识映像，并引用它的层及各层的相应大小。 

Linux `hello-world` 映像的基本清单类似于以下内容：

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
    
你可以使用 Azure 门户或其他工具（例如 Azure CLI 中的 [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) 命令）查看 Azure 容器注册表中的清单。

### <a name="manifest-list"></a>清单列表

多体系结构映像的清单列表（通常称为 OCI 映像的[映像索引](https://github.com/opencontainers/image-spec/blob/master/image-index.md)）是映像的集合（索引），你可以通过指定一个或多个映像名称来创建清单列表。 它包含有关每个映像的详细信息，例如支持的操作系统和体系结构、大小和清单摘要。 请单列表的使用方式与 `docker pull` 和 `docker run` 命令中的映像名称相同。 

`docker` CLI 使用 [docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) 命令管理清单和清单列表。

> [!NOTE]
> 当前，`docker manifest` 命令和子命令为试验性的。 有关使用试验性命令的详细信息，请参阅 Docker 文档。

你可以使用 `docker manifest inspect` 命令查看清单列表。 下面是针对多体系结构映像 `mcr.microsoft.com/mcr/hello-world:latest` 的输出，该映像包含三个清单：两个用于 Linux OS 体系结构，一个用于 Windows 体系结构。
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

当多体系结构清单列表存储在 Azure 容器注册表中时，还可以使用 Azure 门户或诸如 [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) 命令之类的工具查看清单列表。

## <a name="import-a-multi-arch-image"></a>导入多体系结构映像 

可以使用 [az acr import](/cli/azure/acr#az_acr_import) 命令将现有的多体系结构映像导入到 Azure 容器注册表中。 该映像导入语法与用于单体系结构映像的语法相同。 与导入单体系结构映像一样，导入多体系结构映像也不使用 Docker 命令。 

有关详细信息，请参阅[将容器映像导入容器注册表](container-registry-import-images.md)。

## <a name="push-a-multi-arch-image"></a>推送多体系结构映像

如果你有生成工作流为不同的体系结构创建容器映像，请按照以下步骤将多体系结构映像推送到 Azure 容器注册表。

1. 标记特定于体系结构的每个映像并将其推送到容器注册表。 下面的示例假设有两个 Linux 体系结构：arm64 和 amd64。 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. 运行 `docker manifest create` 来创建一个清单列表，将前面的映像合并到多体系结构映像中。

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. 使用 `docker manifest push` 将清单推送到容器注册表：

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. 使用 `docker manifest inspect` 命令查看清单列表。 前面的部分中显示了一个命令输出示例。

将多体系结构清单推送到注册表后，使用与处理单体系结构映像相同的方式来处理多体系结构映像。 例如，使用 `docker pull` 拉取映像，使用 [az acr repository](/cli/azure/acr/repository#az_acr_repository) 命令查看映像的标记、清单和其他属性。

## <a name="build-and-push-a-multi-arch-image"></a>生成并推送多体系结构映像

使用 [ACR 任务](container-registry-tasks-overview.md)的功能，你可以生成多体系结构映像并将其推送到 Azure 容器注册表。 例如，在 YAML 文件中定义一个[多步骤任务](container-registry-tasks-multi-step.md)，用以生成 Linux 多体系结构映像。

下面的示例假设你为两个体系结构（arm64 和 amd64）使用单独的 Dockerfile。 它生成并推送特定于体系结构的映像，然后创建并推送包含 `latest` 标记的多体系结构清单：

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

* 使用 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 为不同的体系结构生成容器映像。
* 了解如何使用试验性 Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) 插件生成多平台映像。

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
