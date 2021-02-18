---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100019364"
---
## <a name="push-image-to-registry"></a>将映像推送到注册表

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如果还没有任何本地容器映像，请运行以下 [docker pull][docker-pull] 命令，拉取现有公共映像。 例如，从 Microsoft Container Registry 中拉取 `hello-world` 映像。

```
docker pull mcr.microsoft.com/hello-world
```

将映像推送到注册表之前，必须使用注册表登录服务器的完全限定的名称进行标记。 登录服务器名称采用 \<registry-name\>.azurecr.io（必须全小写）格式，例如 mycontainerregistry.azurecr.io 。

使用 [docker tag][docker-tag] 命令标记映像。 使用 ACR 实例的登录服务器名称替换 `<login-server>`。

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

示例：

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


最后，使用 [docker push][docker-push] 将映像推送到注册表实例。 使用注册表实例的登录服务器名称替换 `<login-server>`。 此示例创建 **hello-world** 存储库，其中包含 `hello-world:v1` 映像。

```
docker push <login-server>/hello-world:v1
```

将映像推送到容器注册表后，请从本地 Docker 环境中删除 `hello-world:v1` 映像。 （请注意，此 [docker rmi][docker-rmi] 命令不从 Azure 容器注册表中的 **hello-world** 存储库删除该映像。）

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

