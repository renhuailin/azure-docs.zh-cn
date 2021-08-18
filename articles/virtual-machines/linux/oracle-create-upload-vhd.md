---
title: 创建和上传 Oracle Linux VHD
description: 了解如何创建和上传包含 Oracle Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: oracle
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 27bb9d7af0cf7adc9e4ec66cba3635e8e6f34d92
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458031"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>为 Azure 准备 Oracle Linux 虚拟机

本文假定已在虚拟硬盘中安装了 Oracle Linux 操作系统。 存在多个用于创建 .vhd 文件的工具，例如 Hyper-V 等虚拟化解决方案。 有关说明，请参阅[安装 Hyper-V 角色和配置虚拟机](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。

## <a name="oracle-linux-installation-notes"></a>Oracle Linux 安装说明
* 另请参阅[常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 的提示。
* Hyper-V 和 Azure 支持具有 Unbreakable Enterprise Kernel (UEK) 或 Red Hat 兼容内核的 Oracle Linux。
* Hyper-V 和 Azure 不支持 Oracle 的 UEK2，因为它不包括所需的驱动程序。
* Azure 不支持 VHDX 格式，仅支持 **固定大小的 VHD**。  可使用 Hyper-V 管理器或 convert-vhd cmdlet 将磁盘转换为 VHD 格式。
* **需要装载 UDF 文件系统的内核支持。** 在 Azure 上首次启动时，预配配置将通过附加到来宾的 UDF 格式媒体传递到 Linux VM。 Azure Linux 代理必须能够装载 UDF 文件系统才能读取其配置和预配 VM。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 或 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)。
* 低于 2.6.37 的 Linux 内核版本不支持具有更大 VM 大小的 Hyper-V 上的 NUMA。 此问题主要影响使用上游 Red Hat 2.6.32 内核的旧分发版，并已在 Oracle Linux 6.6 及更高版本中修复
* 不要在操作系统磁盘上配置交换分区。 可以在下面的步骤中找到有关此内容的详细信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。
* 请确保已启用 `Addons` 存储库。 编辑文件 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7)，并在此文件中的 [ol6_addons] 或 [ol7_addons] 下将行 `enabled=0` 更改为 `enabled=1`。

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 及更高版本
必须在操作系统中完成特定的配置步骤才能使虚拟机在 Azure 中运行。

1. 在 Hyper-V 管理器的中间窗格中，选择虚拟机。
2. 单击“连接”打开虚拟机窗口。
3. 通过运行以下命令卸载 NetworkManager：

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **注意：** 如果未安装此包，则该命令会失败，并显示一条错误消息。 这是正常情况。
4. 在包含以下文本的 `/etc/sysconfig/` 目录中创建一个名为 **network** 的文件：

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. 在包含以下文本的 `/etc/sysconfig/network-scripts/` 目录中创建一个名为 **ifcfg-eth0** 的文件：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. 修改 udev 规则，以免为以太网接口生成静态规则。 在 Microsoft Azure 或 Hyper-V 中克隆虚拟机时，这些规则可能会引发问题：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. 通过运行以下命令，确保网络服务会在引导时启动：

    ```console
    # chkconfig network on
    ```

8. 通过运行以下命令安装 python-pyasn1：

    ```console
    # sudo yum install python-pyasn1
    ```

9. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开“/boot/grub/menu.lst”，并确保内核包含以下参数：

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   这会确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。
   
   除此之外，建议 *删除* 以下参数：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   图形界面式引导和安静引导在云环境中不适用，在云环境中，我们希望所有日志都发送到串行端口。
   
   根据需要可以配置 `crashkernel` 选项，但请注意此参数会使虚拟机中的可用内存量减少 128MB 或更多，这在较小的虚拟机上可能会出现问题。
10. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。
11. 通过运行以下命令来安装 Azure Linux 代理。 最新版本为 2.0.15。

    ```console
    # sudo yum install WALinuxAgent
    ```

    请注意，如果没有如步骤 2 中所述删除 NetworkManager 包和 NetworkManager-gnome 包，则安装 WALinuxAgent 包将删除它们。
12. 不要在 OS 磁盘上创建交换空间。
    
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是 *临时* 磁盘，并可能在取消预配 VM 时被清空。 在安装 Azure Linux 代理（请参见前一步骤）后，相应地在 /etc/waagent.conf 中修改以下参数：

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. 运行以下命令可取消对虚拟机的预配并且对其进行准备以便在 Azure 上进行预配：

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. 在 Hyper-V 管理器中单击“操作”->“关闭”。 Linux VHD 现已准备好上传到 Azure。

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 及更高版本
**Oracle Linux 7 中的更改**

为 Azure 准备 Oracle Linux 7 虚拟机非常类似于 Oracle Linux 6，但有几个值得注意的重要区别：

* Azure 支持具有 Unbreakable Enterprise Kernel (UEK) 或 Red Hat 兼容内核的 Oracle Linux。 建议使用具有 UEK 的 Oracle Linux。
* NetworkManager 包不再与 Azure Linux 代理冲突。 默认情况下将安装此包，建议不要删除它。
* GRUB2 现在用作默认引导加载程序，因此用于编辑内核参数的过程已更改（请参见下文）。
* XFS 现在是默认文件系统。 如果需要，仍可以使用 ext4 文件系统。

**配置步骤**

1. 在 Hyper-V 管理器中，选择虚拟机。
2. 单击“连接”打开该虚拟机的控制台窗口。
3. 在包含以下文本的 `/etc/sysconfig/` 目录中创建一个名为 **network** 的文件：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. 在包含以下文本的 `/etc/sysconfig/network-scripts/` 目录中创建一个名为 **ifcfg-eth0** 的文件：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. 修改 udev 规则，以免为以太网接口生成静态规则。 在 Microsoft Azure 或 Hyper-V 中克隆虚拟机时，这些规则可能会引发问题：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. 通过运行以下命令，确保网络服务会在引导时启动：

    ```console
    # sudo chkconfig network on
    ```

7. 通过运行以下命令安装 python-pyasn1 包：

    ```console
    # sudo yum install python-pyasn1
    ```

8. 运行以下命令以清除当前 yum 元数据并安装所有更新：

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开“/etc/default/grub”并编辑 `GRUB_CMDLINE_LINUX` 参数，例如：

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   这还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 它还会关闭具有 Unbreakable Enterprise Kernel 的 Oracle Linux 7 中 NIC 的命名约定。 除此之外，建议 *删除* 以下参数：

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   图形界面式引导和安静引导在云环境中不适用，在云环境中，我们希望所有日志都发送到串行端口。
   
   根据需要可以配置 `crashkernel` 选项，但请注意此参数会使虚拟机中的可用内存量减少 128MB 或更多，这在较小的虚拟机上可能会出现问题。
10. 完成后，请按照上面所示编辑“/etc/default/grub”，运行以下命令以重新生成 grub 配置：

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。
12. 安装 Azure Linux 代理和依赖项：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. 安装 cloud-init 来处理预配

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. 交换配置：不要在操作系统磁盘上创建交换空间。

    以前，Azure Linux 代理用于使用在 Azure 上预配虚拟机后附加到虚拟机的本地资源磁盘自动配置交换空间。 但是，现在这由 cloud-init 处理，你不能使用 Linux 代理来格式化资源磁盘以创建交换文件，请在 `/etc/waagent.conf` 中相应地修改以下参数：

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    如果需要装载、格式化和创建交换文件，可以使用以下任一方法：
    * 每次创建 VM 时，将此代码作为 cloud-init 配置传入
    * 使用融入到映像的 cloud-init 指令，从而在每次创建 VM 时执行此操作：

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```


15. 运行以下命令可取消对虚拟机的预配并且对其进行准备以便在 Azure 上进行预配：

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. 在 Hyper-V 管理器中单击“操作”->“关闭”。 Linux VHD 现已准备好上传到 Azure。

## <a name="next-steps"></a>后续步骤
现在，已准备就绪，可以使用 Oracle Linux .vhd 在 Azure 中创建新的虚拟机了。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。
