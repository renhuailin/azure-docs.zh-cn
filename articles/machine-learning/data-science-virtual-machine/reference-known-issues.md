---
title: 参考：已知问题与故障排除
titleSuffix: Azure Data Science Virtual  Machine
description: 获取 Azure Data Science Virtual Machine 的已知问题、解决方法和故障排除的列表。
services: machine-learning
ms.service: data-science-vm
author: michalmar
ms.author: mimarusa
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: c928dfa133172f83c097aa9df7d92486524f62ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729804"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 的已知问题和故障排除方法

本文可帮助你查找和更正使用 Azure Data Science Virtual Machine 时可能遇到的错误或失败。


## <a name="ubuntu"></a>Ubuntu

### <a name="fix-gpu-on-nvidia-a100-gpu-chip---azure-ndasrv4-series"></a>解决 NVIDIA A100 GPU 芯片中的 GPU 问题 - Azure NDasrv4 系列 

ND A100 v4 系列虚拟机是 Azure GPU 系列的新增旗舰，专为高端深度学习训练以及紧密耦合的纵向扩展和横向扩展 HPC 工作负荷而设计。

由于体系结构不同，该虚拟机需要针对高要求工作负荷进行不同的设置，以使用 TensorFlow 或 PyTorch 框架享受到 GPU 加速的优势。

我们正在努力，以支持开箱即用的 ND A100 计算机 GPU。 同时，可以通过添加 NVIDIA 的 Fabric Manager 并更新驱动程序，使 GPU 正常工作。 

在终端中执行以下简单步骤：

1. 添加 NVIDIA 的存储库以安装/更新驱动程序 - 可在[此处](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts)找到分步说明
2. [可选]还可以更新你的 CUDA 驱动程序（从以上存储库中）
3. 安装 NVIDIA 的 Fabric Manager 驱动程序：

    ```
    sudo apt-get install cuda-drivers-460
    sudo apt-get install cuda-drivers-fabricmanager-460
    ```

4. 重新启动 VM（使驱动程序准备就绪）
5. 启用并启动新安装的 NVIDIA Fabric Manager 服务：

    ```
    sudo systemctl enable nvidia-fabricmanager
    sudo systemctl start nvidia-fabricmanager
    ```

现在，可以通过运行以下命令来检查驱动程序和 GPU 是否正常工作：
```
systemctl status nvidia-fabricmanager.service
``` 

之后，应会看到 Fabric Manager 服务正在运行 ![nvidia-fabric-manager-status](./media/nvidia-fabricmanager-status-ok-marked.png)


### <a name="connection-to-desktop-environment-fails"></a>无法连接到桌面环境

如果可以通过 SSH 终端（而不是 x2go）连接到 DSVM，则可能是在 x2go 中设置了错误的会话类型。
要连接到 DSVM 的桌面环境，您需要将 *x2go/session preferences/session* 中的会话类型设置为 *XFCE*。 目前不支持其他桌面环境。

### <a name="fonts-look-wrong-when-connecting-to-dsvm-using-x2go"></a>使用 x2go 连接到 DSVM 时，字体看起来错误

如果连接到 x2go 时某些字体看起来错误，这可能与 x2go 中的会话设置相关。 连接到 DSVM 之前，请取消选中会话首选项对话框的“输入/输出”选项卡中的“设置显示 DPI”复选框。

### <a name="prompted-for-unknown-password"></a>提示输入的密码未知

如果创建 DSVM 时将“设置身份验证类型”为“SSH 公钥”（建议使用密码身份验证）时，将不会为您提供密码。  但是，在某些情况下，某些应用程序仍将要求你输入密码。 运行 `sudo passwd <user_name>` 来为某些用户创建新密码。 使用 `sudo passwd`，可以为根用户创建一个新密码。

运行这些命令不会更改 SSH 的配置，允许的登录机制将保持不变。 

### <a name="prompted-for-password-when-running-sudo-command"></a>运行 sudo 命令时提示输入密码

在 Ubuntu 计算机上运行 `sudo` 命令时，系统可能会多次要求你输入密码，以确认你确实是登录用户。 此行为是预期行为，这是 Ubuntu 中的默认设置。 但是，在某些情况下，不需要重复身份验证，因为这很麻烦。

若要对大多数情况禁用重新身份验证，可以在终端中运行以下命令。

 `echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

重启终端后，sudo 不会再次要求登录，并且会认为来自会话登录的身份验证已足够。

### <a name="cannot-use-docker-as-non-root-user"></a>无法将 docker 用作非根用户

若要将 docker 用作非根用户，你的用户需要是 docker 组的成员。 可以运行 `getent group docker` 命令来检查属于该组的用户。 若要将用户添加到 docker 组，请运行 `sudo usermod -aG docker $USER`。

### <a name="docker-containers-cannot-interact-with-the-outside-via-network"></a>Docker 容器无法通过网络与外部交互

默认情况下，docker 将新容器添加到所谓的“桥接网络”，即 `172.17.0.0/16`。 如果此桥接网络的子网与 DSVM 的子网重叠或者与你的订阅中的另一个专用子网重叠，则主机和容器之间可能无法进行网络通信。 在这种情况下，将无法访问容器中运行的 Web 应用程序，并且无法通过 apt 更新包来更新容器。

若要解决此问题，需要重新配置 docker，以将 IP 地址空间用于其特定桥接网络，即不会与订阅的其他网络重叠的桥接网络。 例如，

```json
"default-address-pools": [
        {
            "base": "10.255.248.0/21",
            "size": 21
        }
    ]
```

通过添加到 `/etc/docker/daemon.json` 文件中包含的 JSON 文档，docker 会向桥接网络分配另一个子网。 （需要使用 sudo 编辑此文件，例如通过运行 `sudo nano /etc/docker/daemon.json`。）

更改后，需要运行 `service docker restart` 来重启 docker 服务。

若要检查更改是否生效，可以运行 `docker network inspect bridge`。 IPAM.Config.Subnet 下的值应该对应上面指定的地址池。

### <a name="gpus-not-available-in-docker-container"></a>docker 容器中的 GPU 不可用

DSVM 上安装的 docker 默认支持 GPU。 但是，必须满足一些先决条件。

* 显然，DSVM 的 VM 大小必须至少包含一个 GPU。
* 使用 `docker run` 启动 docker 容器时，需要添加 --gpus 参数，例如 `--gpus all`。
* 包含 NVIDIA A100 GPU 的 VM 大小需要安装其他软件包，尤其是 [NVIDIA Fabric Manager](https://docs.nvidia.com/datacenter/tesla/pdf/fabric-manager-user-guide.pdf)。 这些包可能尚未预安装在你的映像中。


## <a name="windows"></a>Windows

### <a name="accessing-sql-server"></a>访问 SQL Server

尝试连接到预安装的 SQL Server 实例时，可能会遇到“登录失败”错误。 若要成功连接到 SQL Server 实例，需要在管理员模式下运行要连接的程序，例如 SQL Server Management Studio (SSMS)。 管理员模式是必需的，因为根据 DSVM 的默认设置，只允许管理员进行连接。

### <a name="hyper-v-does-not-work"></a>Hyper-V 不起作用

该 Hyper-V 最初在 Windows 上不起作用是预期的行为。 为了保障启动性能，我们禁用了一些服务。
若要启用 Hyper-V：

1. 请在 Windows DSVM 中打开搜索栏
1. 键入“服务”，
1. 将所有 Hyper-V 服务设置为“手动”
1. 将“Hyper-V 虚拟机管理”设置为“自动”

最终屏幕应如下所示：

   

![启用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)
