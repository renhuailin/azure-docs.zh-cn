---
title: 准备好要通过 Azure Migrate 进行无代理迁移的计算机
description: 了解如何准备好要通过 Azure Migrate 进行无代理迁移的本地计算机。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: c445b6252f19415e51f46f044497ab80207ee860
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272180"
---
# <a name="prepare-for-vmware-agentless-migration"></a>准备 VMware 无代理迁移

本文概述了使用 Azure Migrate 服务器迁移工具[通过无代理迁移方法将 VMware VM 迁移到 Azure](./tutorial-migrate-vmware.md) 时执行的更改。

在将本地 VM 迁移到 Azure 之前，可能需要进行一些更改，使 VM 可用于 Azure。 这些更改非常重要，可确保迁移后的 VM 可以在 Azure 中成功启动并与 Azure VM 建立连接。
Azure Migrate 会针对下面提到的 Linux 和 Windows 操作系统版本自动处理这些配置更改。 此过程称为“混合”。

**支持混合的操作系统版本**

- Windows Server 2008 或更高版本
- Red Hat Enterprise Linux 8、7.9、7.8、7.7、7.6、7.5、7.4、7.0、6.x
- CentOS 8、7.7、7.6、7.5、7.4、6.x
- SUSE Linux Enterprise Server 15 SP0、15 SP1、12、11
- Ubuntu 20.04、19.04、19.10、18.04LTS、16.04LTS、14.04LTS
- Ubuntu 18.04LTS、16.04LTS
- Debian 9、8、7
- Oracle Linux 6、7.7、7.7-CI

你还可以按照本文所述，为上面未列出的操作系统版本手动准备要迁移到 Azure 的 VM。 概括地说，这些更改包括：

- 验证是否存在所需的驱动程序
- 启用串行控制台
- 配置网络设置
- 安装 VM 来宾代理

## <a name="hydration-process"></a>混合过程

在迁移之前，必须对 VM 配置进行一些更改，以确保迁移后的 VM 在 Azure 上正常运行。 Azure Migrate 通过混合过程处理这些配置更改。 混合过程仅适用于上面给出的 Azure 支持的操作系统版本。 在迁移之前，可能需要为上面未列出的其他操作系统版本手动执行所需的更改。 如果在没有进行所需更改的情况下迁移 VM，VM 可能无法启动，或者你可能无法连接到迁移后的 VM。 下图显示了 Azure Migrate 如何执行混合过程。

 [![混合步骤](./media/concepts-prepare-vmware-agentless-migration/hydration-process-inline.png)](./media/concepts-prepare-vmware-agentless-migration/hydration-process-expanded.png#lightbox)

当用户触发 Test Migrate 或 Migrate 时，Azure Migrate 会执行混合过程来准备要迁移到 Azure 的本地 VM。
为了设置混合过程，Azure Migrate 创建一个临时 Azure VM 并附加源 VM 的磁盘以执行更改，使源 VM 可用于 Azure。 临时 Azure VM 是在创建最终迁移 VM 之前在迁移过程中创建的中间 VM。 Azure Migrate 将使用某个市场 OS 映像创建具有类似 OS 类型 (Windows/Linux) 的临时 VM。 如果本地 VM 运行 Windows，则本地 VM 的操作系统磁盘将作为数据磁盘附加到临时 VM，以执行更改。 如果是 Linux 服务器，则附加到本地 VM 的所有磁盘都将作为数据磁盘附加到临时 Azure VM。

Azure Migrate 将创建网络接口、新的虚拟网络、子网和网络安全组 (NSG) 来托管临时 VM。 这些资源在客户的订阅中创建。 如果存在阻止创建网络项目的冲突策略，Azure Migrate 将尝试在作为复制目标设置选项一部分提供的虚拟网络和子网中创建临时 Azure VM。

创建虚拟机后，Azure Migrate 将使用 Azure 虚拟机 REST API 在临时 VM 上调用[自定义脚本扩展](../virtual-machines/extensions/custom-script-windows.md)。 自定义脚本扩展实用工具将在附加到临时 Azure VM 的本地 VM 磁盘上执行准备脚本，该脚本包含 Azure 就绪所需的配置。 准备脚本是从 Azure Migrate 拥有的存储帐户下载的。 虚拟网络的网络安全组规则将配置为允许临时 Azure VM 访问 Azure Migrate 存储帐户，以调用脚本。

 ![迁移步骤](./media/concepts-vmware-agentless-migration/migration-steps.png)

## <a name="changes-performed-during-the-hydration-process"></a>在混合过程中执行的更改

准备脚本根据要迁移的源 VM 的 OS 类型执行以下更改。 你还可以将此部分作为指南，针对不支持混合的操作系统版本手动准备要迁移的 VM。

### <a name="changes-performed-on-windows-servers"></a>在 Windows 服务器上执行的更改

1. **发现并准备 Windows OS 卷**

   在执行相关配置更改之前，准备脚本将验证是否选择了正确的 OS 磁盘进行迁移。 准备脚本将查看系统可见的所有附加卷，并在 SYSTEM 注册表配置单元文件路径中查找源 OS 卷。

   此步骤将执行以下操作：
   - 在附加到临时 VM 的 OS 磁盘上装载每个分区。

   - 装载分区后查找 \Windows\System32\Config\System 注册表文件。
   - 如果找不到这些文件，则卸载该分区，并继续搜索正确的分区。
   - 如果这些文件不在任何分区上，则可能表示选择了错误的 OS 磁盘，或者 OS 磁盘已损坏。 Azure Migrate 将使迁移过程失败并显示相应的错误。

   >[!NOTE]
   >如果正在手动准备要迁移的服务器，则此步骤不适用。

1. **进行与启动和连接相关的更改**

   检测到源 OS 卷文件后，准备脚本会将 SYSTEM 注册表配置单元加载到临时 Azure VM 的注册表编辑器中，并执行以下更改以确保 VM 正常启动和连接。 如果 OS 版本不支持混合，则需要手动配置这些设置。

   1. **验证是否存在所需的驱动程序**
      
      确保所需的驱动程序已安装并设置为在引导启动时加载。 这些 Windows 驱动程序允许服务器与硬件和其他连接设备进行通信。

      - IntelIde.sys
      - Atapi
      - Storfit
      - Storvsc
      - VMbus

   1. **将存储区域网络 (SAN) 策略设置为“全部联机”**

      此操作可确保 Azure VM 中的 Windows 卷使用的驱动器号分配与本地 VM 相同。 默认情况下，将为 Azure VM 分配驱动器 D: 用作临时存储。 这种驱动器分配会导致所有其他附加存储驱动器分配递增一个字母。 若要避免这种自动分配，并确保 Azure 将下一个可用驱动器号分配给其临时卷，请将存储区域网络 (SAN) 策略设置为“全部联机”。

      若要手动配置此设置，请执行以下操作：

      - 在本地服务器上，使用提升的权限打开命令提示符并输入 diskpart。

        ![手动配置](./media/concepts-prepare-vmware-agentless-migration/command-prompt-diskpart.png)

      - 输入 SAN。 如果未保留来宾操作系统的驱动器号，则会返回 Offline All 或 Offline Shared。

      - 在 DISKPART 提示符下，输入 SAN Policy=OnlineAll。 此设置可确保将磁盘联机，并确保可以读取和写入这两个磁盘。

        ![管理员命令提示符 diskpart 联机策略](./media/concepts-prepare-vmware-agentless-migration/diskpart-online-policy.png)

1. **设置 DHCP 启动类型**

   准备脚本还会将 DHCP 服务启动类型设置为自动。 这将使迁移后的 VM 能够获取 IP 地址并在迁移后建立连接。 请确保配置了 DHCP 服务，并且其状态为“正在运行”。

    ![设置 DHCP 启动类型](./media/concepts-prepare-vmware-agentless-migration/get-service-dhcp.png)

   若要手动编辑 DHCP 启动设置，请在 Windows PowerShell 中运行以下示例：

   ```
   Get-Service -Name Dhcp
   Where-Object StartType -ne Automatic
   Set-Service -StartupType Automatic
   ```

1. **禁用 VMware 工具**

   将“VMware 工具”服务启动类型（如果存在）设置为“已禁用”，因为 Azure 中的 VM 不需要它们。

   >[!NOTE]                        
   >若要连接到 Windows Server 2003 VM，必须在 Azure VM 上安装 Hyper-V 集成服务。 Windows Server 2003 计算机默认未安装此程序。 请参阅[此文](./prepare-windows-server-2003-migration.md)，了解如何安装和准备迁移。

1. **安装 Windows Azure 来宾代理**

    Azure Migrate 将尝试安装 Microsoft Azure 虚拟机代理（VM 代理），这是一种安全的轻型进程，用于管理虚拟机 (VM) 与 Azure 结构控制器的交互。 VM 代理在启用和执行 Azure 虚拟机扩展方面起主要作用，这些扩展支持 VM 的部署后配置，例如安装和配置软件。 Azure Migrate 会在 Windows Server 2008 R2 及更高版本上自动安装 Windows VM 代理。

    可以使用 Windows 安装程序包手动安装 Windows VM 代理。 若要手动安装 Windows VM 代理，[下载 VM 代理安装程序](https://go.microsoft.com/fwlink/?LinkID=394789)。 你还可以在 [GitHub Windows IaaS VM 代理版本](https://github.com/Azure/WindowsVMAgent/releases)中搜索特定版本。 VM 代理在 Windows Server 2008 (64 bit) 和更高版本上受支持。

    若要检查 Azure VM 代理是否已成功安装，请打开任务管理器，选择“详细信息”选项卡，并查找名为 WindowsAzureGuestAgent.exe 的进程。 存在该进程表示 VM 代理已安装。 你还可以使用 [PowerShell 检测 VM 代理。](../virtual-machines/extensions/agent-windows.md#powershell)

    ![成功安装 Azure VM 代理](./media/concepts-prepare-vmware-agentless-migration/installation-azure-vm-agent.png)

    执行上述更改后，系统分区将被卸载。 VM 现在已准备好进行迁移。
    [详细了解对 Windows 服务器的更改。](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

### <a name="changes-performed-on-linux-servers"></a>在 Linux 服务器上执行的更改

1. **发现并装载 Linux OS 分区**  

   在执行相关配置更改之前，准备脚本将验证是否选择了正确的 OS 磁盘进行迁移。 脚本将收集有关所有分区、其 UUID 和装入点的信息。 脚本将在所有这些可见分区中查找 /boot 和 /root 分区。

   此步骤将执行以下操作：

   - 发现 /root 分区：
     - 装载每个可见分区并查找 etc/fstab。
      - 如果找不到 fstab 文件，则卸载该分区，并继续搜索正确的分区。
      - 如果找到 fstab 文件，则读取 fstab 内容以识别根设备，并将其装载为基本装入点。
   - 发现 /boot 和其他系统分区：
     - 使用 fstab 内容确定 /boot 是否是单独的分区。 如果是单独的分区，则从 fstab 内容中获取引导分区设备名称，或查找具有引导标志的分区。
     - 脚本将继续在“/mnt/azure_sms_root”上发现并装载 /boot 和其他必要的分区，以生成 chroot jail 所需的根文件系统树。 其他必要的分区包括：/boot/grub/menu.lst、/boot/grub/grub.conf、/boot/grub2/grub.cfg、/boot/grub/grub.cfg、/boot/efi（用于 UEFI 引导）、/var、/lib、/etc、/usr 等。

1. **发现 OS 版本**

   一旦发现根分区，脚本就会使用以下文件来确定 Linux 操作系统的分发版和版本。

   - RHEL/CentOS：etc/redhat-release
   - OL：etc/oracle-release
   - SLES：etc/SuSE-release
   - Ubuntu：etc/lsb-release
   - Debian：etc/debian_version

1. **安装 Hyper-V Linux 集成服务并重新生成内核映像**  

   下一步是检查内核映像并重新生成 Linux init 映像，使其在初始 ramdisk 上包含必要的 Hyper-V 驱动程序（hv_vmbus、hv_storvsc、hv_netvsc）。 重新生成 init 映像可以确保 VM 在 Azure 中启动。

   Azure 在 Hyper-V 虚拟机监控程序上运行。 因此，Linux 需要某些内核模块才能在 Azure 中运行。 若要准备 Linux 映像，你需要重新生成 initrd，使初始 ramdisk 上至少包含 hv_vmbus 和 hv_storvsc 内核模块。 重新生成 initrd 或 initramfs 映像的机制可能会因分发而有所不同。 查阅分发的文档或相应过程的支持。 以下示例演示如何使用 mkinitrd 实用工具重新生成 initrd：

   1. 查找系统上安装的内核列表 (/lib/modules)
   1. 对于每个模块，检查是否已包含 Hyper-V 驱动程序。
   1. 如果缺少这些驱动程序中的任何一个，请添加所需的驱动程序，并为相应的内核版本重新生成映像。

      >[!NOTE]
      >此步骤可能不适用于 Ubuntu 和 Debian VM，因为这些 VM 默认情况下已内置 Hyper-V 驱动程序。 [详细了解这些更改](../virtual-machines/linux/create-upload-generic.md#installing-kernel-modules-without-hyper-v)。

      重新生成 initrd 的演示示例

      - 备份现有 initrd 映像

        ```
        cd /boot
        sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
        ```

      - 使用 hv_vmbus 和 hv_storvsc 内核模块重新生成 initrd：

        ```
          sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
        ```
   默认情况下，大多数最新版本的 Linux 分发版已包含此服务。 如果不包含，请使用上述步骤为所有版本（以上所示版本除外）手动安装此映像。

1. **启用 Azure 串行控制台日志记录**

   之后，脚本将进行更改，以启用 Azure 串行控制台日志记录。 启用控制台日志记录有助于排查 Azure VM 上的问题。 若要详细了解适用于 Linux 的 Azure 串行控制台，请参阅[适用于 Linux 的 Azure 串行控制台 - 虚拟机 | Microsoft Docs](/troubleshoot/azure/virtual-machines/serial-console-linux)。

   修改 GRUB 或 GRUB2 中的内核引导行并包含以下参数，以便将所有控制台消息发送到第一个串行端口。 这些消息可以协助 Azure 支持人员调试任何问题。

   ```
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
   ```

   我们还建议删除以下参数（如果存在）。

   ```
   rhgb quiet crashkernel=auto
   ```
    [请参阅此文](../virtual-machines/linux/create-upload-generic.md#general-linux-system-requirements)了解具体的更改。

1. **为建立连接进行的网络更改**

   根据 OS 版本，脚本将执行所需的网络更改，以连接到迁移后的 VM。 更改包括：

   1. 移动（或删除）udev 规则，以避免产生以太网接口的静态规则。 在 Azure 中克隆虚拟机时，这些规则会引发问题。

      RedHat 服务器的演示示例

      ```console
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
      ```

   1. 必要时删除网络管理器。 网络管理器可能会干扰一些 OS 版本的 Azure Linux 代理。 建议对运行 RedHat 和 Ubuntu 分发版的服务器进行这些更改。

   1. 请运行以下命令来卸载该包：
    
      RedHat 服务器的演示示例

      ```console
         # sudo rpm -e --nodeps NetworkManager
      ```

   1. 备份现有 NIC 设置，并使用 DHCP 设置创建 eth0 NIC 配置文件。 为此，脚本将创建或编辑 /etc/sysconfig/network-scripts/ifcfg-eth0 文件，并添加以下文本：

      RedHat 服务器的演示示例

      ```config
         DEVICE=eth0
         ONBOOT=yes
         BOOTPROTO=dhcp
         TYPE=Ethernet
         USERCTL=no
         PEERDNS=yes
         IPV6INIT=no
         PERSISTENT_DHCLIENT=yes
         NM_CONTROLLED=yes
      ```

   1. 按下面所示重置 etc/sysconfig/network 文件：

      RedHat 服务器的演示示例

      ```config
         NETWORKING=yes
         HOSTNAME=localhost.localdomain
      ```

1. **Fstab 验证**

    Azure Migrate 将验证 fstab 文件的条目，并在需要时使用永久性卷标识符 UUID 替换 fstab 条目。 此操作可确保无论附加到哪个系统，驱动器/分区名称都保持不变。

   - 如果设备名称是标准设备名称（比如 /dev/sdb1），则：
     - 如果它是根分区或启动分区，则将其替换为 UUID。
     - 如果该分区作为标准分区与根分区或启动分区共存于同一磁盘上，则将其替换为 UUID。
   - 如果设备名称为 UUID/LABEL/LV，则不会进行任何更改。
   - 如果它是网络设备（nfs、cifs、smbfs 等），脚本将对相应条目进行注释。 若要访问它，你可以对同一条目取消注释并重新启动 Azure VM。

1. **安装 Linux Azure 来宾代理**

    Azure Migrate 将尝试安装 Microsoft Azure Linux 代理 (waagent)，这是一种安全的轻型进程，用于管理 Linux 和 FreeBSD 预配以及 VM 与 Azure 结构控制器的交互。  [详细了解](../virtual-machines/extensions/agent-linux.md)通过 Linux 代理为 Linux 和 FreeBSD IaaS 部署启用的功能。

    查看[必需包](../virtual-machines/extensions/agent-linux.md#requirements)的列表以安装 Linux VM 代理。 使用 VMware 迁移的无代理方法时，Azure Migrate 会自动为 RHEL 8/7/6、CentOS 8/7/6、Ubuntu 14.04/16.04/18.04/19.04/19.10/20.04、SUSE 15 SP0/15 SP1/12、Debian 9/8/7 和 Oracle 7 安装 Linux VM 代理。 按照这些说明为其他 OS 版本[手动安装 Linux 代理](../virtual-machines/extensions/agent-linux.md#installation)。

    可以使用命令验证 Azure Linux 代理的服务状态，确保它正在运行。 服务名称可能是 walinuxagent 或 waagent。
    完成混合更改后，脚本将卸载所有已装载的分区，停用卷组，然后刷新设备。

   ```
    $ vgchange -an <vg-name>
    $ blockdev –flushbufs <disk-device-name>
   ```

   [详细了解对 Linux 服务器的更改。](../virtual-machines/linux/create-upload-generic.md)

### <a name="clean-up-the-temporary-vm"></a>清理临时 VM

执行必要的更改后，Azure Migrate 将停止临时 VM 并释放附加的 OS 磁盘（和数据磁盘）。 这标志着混合过程的结束。     

此后，将克隆修改后的 OS 磁盘和包含复制数据的数据磁盘。 在目标区域、虚拟网络和子网中创建一个新的虚拟机，并将克隆的磁盘附加到该虚拟机。 这标志着迁移过程的完成。

## <a name="learn-more"></a>了解更多信息

- [准备好要迁移到 Azure 的本地计算机。](./prepare-for-migration.md)
