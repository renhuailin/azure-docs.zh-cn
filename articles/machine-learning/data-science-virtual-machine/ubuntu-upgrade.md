---
title: 如何将 Data Science Virtual Machine 升级到 Ubuntu 18.04
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何从 CentOS 和 Ubuntu 16.04 升级到最新的 Ubuntu 18.04 Data Science Virtual Machine。
keywords: 深度学习, AI, 数据科学工具, data science virtual machine, 团队数据科学过程
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659454"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>将 Data Science Virtual Machine 升级到 Ubuntu 18.04

如果你的 Data Science Virtual Machine 运行的是早期版本，例如 Ubuntu 16.04 或 CentOS，则应将 DSVM 迁移到 Ubuntu 18.04。 迁移会确保你获取最新的操作系统补丁、驱动程序、预安装软件和库版本。 本文档说明了如何从 Ubuntu 的早期版本或从 CentOS 进行迁移。 

## <a name="prerequisites"></a>先决条件

- 熟悉 SSH 和 Linux 命令行

## <a name="overview"></a>概述

可通过两种可能的方式进行迁移：

- 就地迁移，也称为“同服务器”迁移。 此迁移会升级现有 VM，而不是创建新的虚拟机。 就地迁移是从 Ubuntu 16.04 迁移到 Ubuntu 18.04 的较简单方法。
- 并行迁移，也称为“服务器间”迁移。 此迁移将数据从现有虚拟机传输到新创建的虚拟机。 并行迁移是从 Centos 迁移到 Ubuntu 18.04 的方式。 如果你认为旧安装已变得混乱，你可能更倾向于进行并行迁移以在 Ubuntu 版本之间进行升级。

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>如果需要回滚，请为你的 VM 创建快照

在 Azure 门户中，使用搜索栏查找 **快照** 功能。 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="显示了 Azure 门户和搜索栏的屏幕截图，其中突出显示了**快照**":::

1. 选择“添加”，这会转到“创建快照”页面。 选择你的虚拟机的订阅和资源组。 对于“区域”，请选择目标存储所在的区域。 选择 DSVM 存储磁盘和其他备份选项。 **标准 HDD** 是适用于此备份方案的存储类型。

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="显示了“创建快照”选项的屏幕截图":::

2. 填写所有详细信息并通过验证后，选择“查看 + 创建”来验证并创建快照。 当快照成功完成后，你会看到一条消息，告知你部署已完成。

## <a name="in-place-migration"></a>就地迁移

如果迁移的是较旧的 Ubuntu 版本，可以选择进行就地迁移。 此迁移不会创建新的虚拟机，并且执行的步骤少于并行迁移的步骤。  如果因为需要更多控制或要从另一发行版（例如 CentOS）进行迁移而希望进行并行迁移，请跳到[并行迁移](#side-by-side-migration)部分。 

1. 在 Azure 门户中，启动你的 DSVM 并使用 SSH 登录。 为此，请选择“连接”和“SSH”并按照连接说明进行操作。 

1. 连接到 DSVM 上的终端会话后，运行以下升级命令：

    ```bash
    sudo do-release-upgrade
    ```

升级过程需要一段时间才能完成。 结束时，程序会要求获得重启虚拟机的权限。 回答“是”。 系统重启时，会断开与 SSH 会话的连接。

### <a name="if-necessary-regenerate-ssh-keys"></a>如有必要，请重新生成 SSH 密钥

> [!IMPORTANT] 
> 升级并重启后，可能需要重新生成 SSH 密钥。

在你的 VM 升级并重启后，尝试通过 SSH 再次访问它。 IP 地址可能在重启过程中发生了更改，因此请在尝试连接之前进行确认。

如果收到“远程主机标识已更改”错误，则需要重新生成 SSH 凭据。

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="显示了“远程主机标识已更改”警告的 PowerShell 屏幕截图":::

为此，请在本地计算机上运行以下命令：

```bash
ssh-keygen -R "your server hostname or ip"
```

你现在应能通过 SSH 进行连接。 如果仍然遇到问题，请在“连接”页上单击“排查 SSH 连接性问题”链接。

## <a name="side-by-side-migration"></a>并行迁移

如果要从 CentOS 迁移或要进行干净的 OS 安装，可以进行并行迁移。 此类型的迁移包含更多步骤，但允许你精确控制要迁移的文件。

从基于同一组上游源包的其他系统进行迁移应该相对简单，例如[常见问题解答/CentOS3](https://wiki.centos.org/FAQ/CentOS3)。

你可以选择升级文件系统的操作系统部分，并保留用户目录，例如 `/home`。 如果你将旧的用户主目录保留在原来的位置，那么 GNOME/KDE 菜单和其他桌面项可能会出现一些问题。 最简单的方法是在文件系统中创建新的用户帐户，并将旧目录装载到文件系统中的其他某个位置，以便在迁移后引用、复制或链接用户的资料。

### <a name="migration-at-a-glance"></a>迁移概览

1.  创建现有 VM 的快照，如前文所述

1.  基于该快照创建磁盘

1.  创建新的 Ubuntu Data Science Virtual Machine

1.  在新虚拟机上重新创建用户帐户

1.  在新的 Data Science Virtual Machine 上装载快照 VM 的磁盘作为数据磁盘

1.  手动复制所需的数据

### <a name="create-a-disk-from-your-vm-snapshot"></a>基于 VM 快照创建磁盘

如果尚未按前文所述创建 VM 快照，请执行该操作。 

1. 在 Azure 门户中，搜索“磁盘”并选择“添加”，这将打开“磁盘”页面。

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Azure 门户的屏幕截图，其中显示了搜索“磁盘”时的页面和“添加”按钮":::

2. 将“订阅”、“资源组”和“区域”设置为你的 VM 快照的值。 为要创建的磁盘选择一个 **名称**。

3. 选择“快照”作为“源类型”，并选择 VM 快照作为“源快照”。 查看并创建磁盘。 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="磁盘创建对话框的屏幕截图，其中显示了选项":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>创建新的 Ubuntu Data Science Virtual Machine

使用 [Azure 门户](https://portal.azure.com)或 [ARM 模板](./dsvm-tutorial-resource-manager.md)创建新的 Ubuntu Data Science Virtual Machine。 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>在新的 Data Science Virtual Machine 上重新创建用户帐户

由于你只需从旧计算机复制数据，因此需要重新创建要在新计算机上使用的任何用户帐户和软件环境。

Linux 足够灵活，允许你在新安装中自定义目录和路径，以遵循你的旧计算机的要求。 但一般情况下，在使用新式 Ubuntu 的首选布局的基础上根据情况修改用户环境和脚本更为容易。

有关详细信息，请参阅[快速入门：设置适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](./dsvm-ubuntu-intro.md)。

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>在新的 Data Science Virtual Machine 上装载快照 VM 的磁盘作为数据磁盘

1. 在 Azure 门户中，确保你的 Data Science Virtual Machine 正在运行。

2. 在 Azure 门户中，转到你的 Data Science Virtual Machine 的页面。 选择左侧导轨上的“磁盘”边栏选项卡。 选择“附加现有磁盘”。

3. 在“磁盘名称”下拉列表中，选择基于旧 VM 的快照创建的磁盘。

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="DSVM 选项页的屏幕截图，其中显示了磁盘附加选项":::

4. 选择“保存”以更新你的虚拟机。

> [!Important]
> 在你附加数据磁盘时，你的 VM 应当正在运行。 如果 VM 未运行，则可能会按不正确的顺序添加磁盘，从而导致令人费解且可能无法启动的系统。 如果在 VM 关闭的情况下添加数据磁盘，请选择数据磁盘旁边的 **X**，启动 VM，然后重新附加它。

### <a name="manually-copy-the-wanted-data"></a>手动复制所需的数据 

1. 使用 SSH 登录到正在运行的虚拟机。

2. 通过运行以下命令，确认已附加了基于旧 VM 的快照创建的磁盘：

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    结果应当类似于下图。 在图中，磁盘 `sda1` 装载到根目录上，`sdb2` 是 `/mnt` 暂存磁盘。 基于旧 VM 的快照创建的数据磁盘会被标识为 `sdc1` 但尚不可用，表现为缺少装载位置。 你的结果可能具有不同的标识符，但你应当会看到类似的模式。
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="lsblk 输出的屏幕截图，其中显示了未装载的数据驱动器":::
    
3. 若要访问数据驱动器，请为其创建一个位置并装载它。 将 `/dev/sdc1` 替换为 `lsblk` 返回的相应值：

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. 现在，`/datadrive` 包含旧 Data Science Virtual Machine 的目录和文件。 根据需要将所需的目录或文件从数据驱动器移动或复制到新 VM。

有关详细信息，请参阅[使用门户将数据磁盘附加到 Linux VM](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)。

## <a name="connect-and-confirm-version-upgrade"></a>连接并确认版本升级

无论是就地迁移还是并行迁移，都请确认是否已成功升级。 从终端会话中，运行： 

```bash
cat /etc/os-release
```

你应该会看到你正在运行 Ubuntu 18.04。

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Ubuntu 终端的屏幕截图，其中显示了 OS 版本数据":::

版本的更改也会显示在 Azure 门户中。

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="门户屏幕截图，其中显示了 DSVM 属性，包括 OS 版本":::

## <a name="next-steps"></a>后续步骤

- [使用 Azure 中的 Ubuntu Data Science Machine 完成数据科学任务](./linux-dsvm-walkthrough.md)
- [哪些工具包含在 Azure Data Science Virtual Machine 中？](./tools-included.md)