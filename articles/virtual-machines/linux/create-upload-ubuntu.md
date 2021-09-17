---
title: 在 Azure 中创建和上传 Ubuntu Linux VHD
description: 了解如何创建和上传包含 Ubuntu Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/28/2021
ms.author: srijangupta
ms.openlocfilehash: 2079e50d92c7253c7c4f642c9a51e56aa1bfae2c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689797"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>为 Azure 准备 Ubuntu 虚拟机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

Ubuntu 现已发布正式 Azure VHD，可从 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 下载。 如果需要为 Azure 构建自己专用的 Ubuntu 映像，而不是使用以下手动过程，则我们建议先使用这些已知良好的 VHD，并根据需要进行自定义。 始终可以在以下位置找到最新的映像版本：

* Ubuntu 18.04/Bionic：[bionic-server-cloudimg-amd64-azure.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64-azure.vhd.zip)
* Ubuntu 20.04/Focal：[focal-server-cloudimg-amd64-azure.vhd.zip](https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64-azure.vhd.zip)

## <a name="prerequisites"></a>先决条件
本文假设已在虚拟硬盘中安装 Ubuntu Linux 操作系统。 可使用多种工具创建 .vhd 文件，如 Hyper-V 等虚拟化解决方案。 有关说明，请参阅 [安装 Hyper-V 角色和配置虚拟机](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。

**Ubuntu 安装说明**

* 另请参阅 [常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes) ，以获取更多有关如何为 Azure 准备 Linux 的提示。
* Azure 不支持 VHDX 格式，仅支持 **固定大小的 VHD**。  可使用 Hyper-V 管理器或 `Convert-VHD` cmdlet 将磁盘转换为 VHD 格式。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 或 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)。
* 请勿在操作系统磁盘上配置交换分区或交换文件。 可以将 cloud-init 设置代理配置为在临时资源磁盘上创建交换文件或交换分区。 可以在下面的步骤中找到有关此内容的详细信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。

## <a name="manual-steps"></a>手动步骤
> [!NOTE]
> 尝试为 Azure 创建自己的自定义 Ubuntu 映像前，请考虑改用 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 中预生成的经过测试的映像。
> 
> 

1. 在 Hyper-V 管理器的中间窗格中，选择虚拟机。

2. 单击“连接”  打开虚拟机窗口。

3. 替换映像中的当前存储库，以使用 Ubuntu 的 Azure 存储库。

    编辑 `/etc/apt/sources.list`前，建议进行备份：

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 18.04 和 Ubuntu 20.04：

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Ubuntu Azure 映像现在使用的是 [Azure 定制内核](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)。 运行以下命令，将操作系统更新为最新版 Azure 定制内核，并安装 Azure Linux 工具（包括 Hyper-V 依赖项）：

    Ubuntu 18.04 和 Ubuntu 20.04：

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. 修改 Grub 的内核引导行以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开 `/etc/default/grub`，找到名为 `GRUB_CMDLINE_LINUX_DEFAULT` 的变量（或根据需要添加它）并对它进行编辑，使其包含以下参数：

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    保存并关闭此文件并运行 `sudo update-grub`。 这还可确保将所有控制台消息发送到第一个串行端口，从而可以协助 Azure 技术支持人员调试问题。

6. 请确保已安装 SSH 服务器且将其配置为在引导时启动。  这通常是默认设置。

7. 安装 cloud-init（预配代理）和 Azure Linux 代理（来宾扩展处理程序）。 Cloud-init 使用 `netplan` 在预配和每次后续引导期间配置系统网络配置。

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  安装 `walinuxagent` 包时会删除 `NetworkManager` 和 `NetworkManager-gnome` 包（如果已安装它们）。

8. 删除可能与 Azure 上的 cloud-init 配置冲突的 cloud-init 默认配置和剩余 `netplan` 工件：

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg /etc/cloud/cloud.cfg.d/99-installer.cfg /etc/cloud/cloud.cfg.d/subiquity-disable-cloudinit-networking.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. 将 cloud-init 配置为使用 Azure 数据源预配系统：

    ```bash
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. 将 Azure Linux 代理配置为依赖 cloud-init 来执行预配。 有关这些选项的详细信息，请查看 [WALinuxAgent 项目](https://github.com/Azure/WALinuxAgent)。

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. 清除 cloud-init 和 Azure Linux 代理运行时项目和日志：

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. 运行以下命令可取消对虚拟机的预配并且对其进行准备以便在 Azure 上进行预配：

    > [!NOTE]
    > `sudo waagent -force -deprovision+user` 命令通过尝试清理系统并使其适合重新预配，以此将映像通用化。 `+user` 选项会删除上次预配的用户帐户和关联的数据。

    > [!WARNING]
    > 使用上述命令取消预配无法保证清除映像中的所有敏感信息且适用于分发版。

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. 在 Hyper-V 管理器中单击“操作”->“关闭”。

14. Azure 仅接受固定大小的 VHD。 如果 VM 的 OS 磁盘不是固定大小的 VHD，请使用 `Convert-VHD` PowerShell cmdlet 并指定 `-VHDType Fixed` 选项。 请查看以下文档，了解 `Convert-VHD`：[Convert-VHD](/powershell/module/hyper-v/convert-vhd)。


## <a name="next-steps"></a>后续步骤
现在，可使用 Ubuntu Linux 虚拟硬盘在 Azure 中创建新的 Azure 虚拟机。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。
