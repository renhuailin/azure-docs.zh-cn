---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 6a4871b4aca10dddae46ed8f732090d9a258b3d6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468079"
---
## <a name="download-and-install-the-osm-client-binary"></a>下载并安装 OSM 客户端库

在 Linux 或[适用于 Linux 的 Windows 子系统][install-wsl]上的基于 bash 的 shell 中，使用 `curl` 下载 OSM 发行版，然后使用 `tar` 进行解压缩，如下所示：

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.4

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

`osm` 客户端二进制文件在客户端计算机上运行，这使你可以在 AKS 群集中管理 OSM。 在 Linux 或[适用于 Linux 的 Windows 子系统][install-wsl]上基于 bash 的 shell 中，使用以下命令安装 OSM `osm` 客户端二进制文件。 这些命令可将 `osm` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

可使用以下命令验证 `osm` 客户端库是否已正确添加到路径及其版本号。

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
