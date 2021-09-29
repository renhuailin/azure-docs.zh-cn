---
title: 关于注册表、存储库、映像和项目
description: Azure 容器注册表、存储库和容器映像和其他项目的重要概念简介。
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: add8c20de07a2d520095f257dac0356d1c21af57
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596137"
---
# <a name="about-registries-repositories-and-artifacts"></a>关于注册表、存储库和项目

本文介绍容器注册表、存储库和容器映像以及相关项目的重要概念。 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="注册表、存储库和项目":::

## <a name="registry"></a>注册表

容器注册表是一项用来存储和分发容器映像和相关项目的服务。 Docker Hub 是公共容器注册表的一个示例，它充当 Docker 容器映像的一般目录。 Azure 容器注册表为用户提供对容器内容的直接控制、集成身份验证、支持全局分发和网络邻近部署可靠性的[异地复制](container-registry-geo-replication.md)、[采用专用链接的虚拟网络配置](container-registry-private-link.md)、[标记锁定](container-registry-image-lock.md)以及许多其他的增强功能。 

除了 Docker 兼容的容器映像以外，Azure 容器注册表还支持广泛的[内容项目](container-registry-image-formats.md)，包括 Helm 图表和开放容器计划 (OCI) 映像格式。

## <a name="repository"></a>存储库

存储库是注册表中名称相同但标记不同的容器映像或其他项目的集合。 例如，以下三个映像位于 `acr-helloworld` 存储库中：

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

存储库名称还可包括[命名空间](container-registry-best-practices.md#repository-namespaces)。 命名空间允许你使用正斜杠分隔的名称标识组织中的相关存储库和项目所有权。 但是，注册表独立管理所有存储库，而不是将其作为层次结构进行管理。 例如：

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

存储库名称只能包含小写字母数字字符、句点、短划线、下划线和正斜杠。 

有关完整的存储库命名规则，请参阅[开放容器计划分发规范](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)。

## <a name="artifact"></a>项目

注册表中的容器映像或其他项目与一个或多个标记相关联，具有一个或多个层，并由一个清单标识。 了解这些组件之间的关系有助于有效管理注册表。

### <a name="tag"></a>标记

映像或其他项目的标记指定了其版本。 存储库中的单个项目可分配有一个或多个标记，但也可能“无标记”。 也就是说，可删除映像中的所有标记，而映像的数据（其层）保留在注册表中。

映像的名称由存储库（或存储库和命名空间）和标记进行定义。 在推送或拉取操作中指定映像名称，可以推送和拉取映像。 如果未在 Docker 命令中提供标记，则默认使用标记 `latest`。

如何对容器映像进行标记由其开发或部署方案引导。 例如，建议使用稳定标记来维护基础映像，使用唯一标记来部署映像。 有关详细信息，请参阅[有关对容器映像进行标记和版本控制的建议](container-registry-image-tag-version.md)。

有关标记命名规则，请参阅 [Docker 文档](https://docs.docker.com/engine/reference/commandline/tag/)。

### <a name="layer"></a>层

容器映像和项目由一个或多个层组成。 不同的项目类型以不同的方式定义层。 例如，在 Docker 容器映像中，每个层都对应于 Dockerfile 中定义了映像的一行：

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="容器映像的层":::

注册表中的项目共享公用的层，因而提高了存储效率。 例如，不同存储库中的多个映像可能有一个公用的 Alpine Linux 基础层，但注册表中仅存储该层的一个副本。 通过让多个项目共享公用层，层共享还优化了到节点的层分布。 如果某个节点上已有的映像包含 ASP.NET Core 层作为其基础层，则在后续拉取引用同一层的不同映像时，不会将层传输到节点。 相反，它会引用节点上已存在的层。

为了针对潜在的层操作提供安全隔离和保护，层不会在注册表之间共享。

### <a name="manifest"></a>清单

推送到容器注册表的每个容器映像或项目都与一个清单相关联。 推送内容时由注册表生成的清单唯一标识映像并指定其层。

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

可以使用 Azure CLI 命令 [az acr repository show-manifests][az-acr-repository-show-manifests] 列出存储库的清单：

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，列出“acr-helloworld”存储库的清单：

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>清单摘要

清单由唯一的 SHA-256 哈希（即清单摘要）进行标识  。 每个映像或项目（无论是否标记）均由其摘要标识。 即便项目的层数据与其他项目的层数据相同，摘要值也是唯一的。 此机制使你能够反复向注册表推送标记相同的映像。 例如，你可反复向注册表推送 `myimage:latest` 而不出任何错误，因为每个映像均由其唯一摘要标识。

通过在拉取操作中指定项目的摘要，可从注册表拉取该项目。 某些系统可能配置为按摘要拉取，因为它保证即便以后向注册表推送标记相同的映像，也会拉取该映像版本。

> [!IMPORTANT]
> 如果反复推送具有相同标记的已修改项目，则可能会创建孤立的项目；此类项目不带标记，但仍占用注册表中的空间。 按标记列出或查看映像时，Azure CLI 或 Azure 门户中不显示无标记的映像。 但是，它们的层仍然存在，且占用注册表中的空间。 当清单是指向特定层的唯一清单或最后一个清单时，删除未标记的映像将释放注册表空间。 有关释放未标记映像所用空间的信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

## <a name="addressing-an-artifact"></a>对项目进行寻址

若要使用 Docker 或其他客户端工具对用于推送和拉取操作的注册表项目进行寻址，请将完全限定的注册表名称、存储库名称（包括适用的命名空间路径）以及项目标记或清单摘要组合到一起。 有关这些术语的解释，请参阅前面的部分。

  **按标记进行寻址**：`[loginServerUrl]/[repository][:tag]`
    
  **按摘要进行寻址**：`[loginServerUrl]/[repository@sha256][:digest]`  

使用 Docker 或其他客户端工具将项目拉取或推送到 Azure 容器注册表时，请使用注册表的完全限定 URL，也称为登录服务器名称。 在 Azure 云中，Azure 容器注册表的完全限定 URL 的格式为 `myregistry.azurecr.io`（全小写）。

> [!NOTE]
> * 不能在注册表登录服务器 URL 中指定端口号，例如 `myregistry.azurecr.io:443`。 
> * 如果未在命令中提供标记，则默认使用标记 `latest`。  

   
### <a name="push-by-tag"></a>按标记推送

示例： 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>按标记拉取

例如： 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>按清单摘要拉取


示例：

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>后续步骤

详细了解 Azure 容器注册表中的[注册表存储](container-registry-storage.md)和[支持的内容格式](container-registry-image-formats.md)。

了解如何从 Azure 容器注册表[推送和拉取](container-registry-get-started-docker-cli.md)映像。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
