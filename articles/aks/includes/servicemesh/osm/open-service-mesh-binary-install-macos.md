---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: e72c6536ab15246466e16e439997b6deb6547604
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439808"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>下载并安装 Open Service Mesh (OSM) 客户端二进制文件

在基于 bash 的 shell 中，使用 `curl` 下载 OSM 发行版，然后使用 `tar` 进行解压缩，如下所示：

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.9.1

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

`osm` 客户端二进制文件在客户端计算机上运行，这使你可以在 AKS 群集中管理 OSM。 在基于 bash 的 shell 中使用以下命令安装 OSM `osm` 客户端二进制文件。 这些命令可将 `osm` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

可使用以下命令验证 `osm` 客户端库是否已正确添加到路径及其版本号。

```
osm version
```
