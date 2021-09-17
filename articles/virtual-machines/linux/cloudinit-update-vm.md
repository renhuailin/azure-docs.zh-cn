---
title: 使用 Azure 上的 Linux VM 中的 cloud-init
description: 在使用 Azure CLI 创建期间如何使用 cloud-init 在 Linux VM 中更新和安装包
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/01/2021
ms.author: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: a5bd15f19cd641ce7c10ea932084d36cca0d7901
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687898"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>使用 cloud-init 在 Azure 的 Linux VM 中更新和安装包

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

本文演示如何在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时间更新 Linux 虚拟机 (VM) 或虚拟机规模集上的包。 Azure 预配资源后，这些 cloud-init 脚本即会在第一次启动时运行。 有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>使用 cloud-init 更新 VM
出于安全目的，你需要配置 VM，以便在首次启动时应用最新的更新。 由于 cloud-init 支持不同 Linux 发行版，因此无需为包管理器指定 `apt` 或 `yum`。 相反，你需要定义 `package_upgrade`，并让 cloud-init 进程确定正在使用的发行版的适当机制。 

就此示例来说，我们将使用 Azure Cloud Shell。 若要查看操作中的升级进程，请创建一个名为“cloud_init_upgrade.txt”的文件并粘贴下面的配置。 

在下面的代码块中选择“试用”按钮，以打开 Cloud Shell。 若要在 Cloud Shell 中创建文件并查看可用编辑器的列表，请键入以下内容：

```azurecli-interactive
sensible-editor cloud_init_upgrade.txt 
```

复制下面的文本并将其粘贴到 `cloud_init_upgrade.txt` 文件中。 请确保已正确复制整个 cloud-init 文件，尤其是第一行。  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

在部署之前，需要使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vm create](/cli/azure/vm) 创建 VM，并通过 `--custom-data` 参数指定 cloud-init 文件，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的 **publicIpAddress**：

```bash
ssh <publicIpAddress>
```

运行包管理工具并检查更新。

```bash
sudo yum update
```

由于 cloud-init 在启动时已检查和安装更新，因此，应没有要应用的其他更新。  你可以通过运行 `yum history` 来查看更新过程、更改的程序包数量以及 `httpd` 的安装，并查看类似于以下内容的输出。

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下文章：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
