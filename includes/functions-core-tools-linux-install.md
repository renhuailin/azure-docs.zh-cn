---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2021
ms.author: glenga
ms.openlocfilehash: 162cfe73e57c314ed1b8200d5ecacebc92cfc517
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669697"
---
以下步骤使用 [APT](https://wiki.debian.org/Apt) 在 Ubuntu/Debian Linux 发行版上安装 Core Tools。 有关其他 Linux 发行版，请参阅 [Core Tools 自述文件](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)。

1. 安装 Microsoft 包存储库 GPG 密钥，以验证包完整性：

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. 在进行 APT 更新之前，设置 APT 源列表。

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. 查看 `/etc/apt/sources.list.d/dotnetdev.list` 文件以获取下面列出的相应 Linux 版本字符串之一：

    | Linux 分发版 | 版本 |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. 启动 APT 源更新：

    ```bash
    sudo apt-get update
    ```