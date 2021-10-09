---
title: 使用 Azure Site Recovery 安装 Linux VM 故障恢复的主目标服务器
description: 了解如何设置 Linux 主目标服务器，以便在使用 Azure Site Recovery 将 VMware VM 灾难恢复到 Azure 期间故障回复到本地站点。
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: d766903d6de975a10dfd29bdf367ac2831321e50
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777424"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>安装用于故障回复的 Linux 主目标服务器
将虚拟机故障转移到 Azure 后，可将虚拟机故障回复到本地站点。 若要故障回复，需要在本地站点中重新保护 Azure 中的虚拟机。 对于此过程，需要安装一个本地主目标服务器用于接收流量。 

如果受保护的虚拟机是 Windows 虚拟机，则需要安装 Windows 主目标。 对于 Linux 虚拟机，需要安装 Linux 主目标。 请阅读以下步骤，了解如何创建和安装 Linux 主目标。

> [!IMPORTANT]
> 不支持 LVM 上的主目标服务器。

## <a name="overview"></a>概述
本文提供 Linux 主目标的相关安装说明。

请在本文末尾或者在 [Azure 恢复服务论坛](/answers/topics/azure-site-recovery.html)中发表评论或问题。

## <a name="prerequisites"></a>先决条件

* 若要选择用于部署主目标的主机，请确定是要故障回复到现有的本地虚拟机还是新的虚拟机。 
    * 对于现有虚拟机，主目标的主机应有权访问虚拟机的数据存储。
    * 如果本地虚拟机不存在（使用“备用位置恢复”），需在主目标所在的同一台主机上创建故障回复虚拟机。 可以选择任何一台 ESXi 主机用于安装主目标。
* 主目标应在可与进程服务器和配置服务器通信的网络中。
* 主目标版本应该低于或等于进程服务器和配置服务器的版本。 例如，如果配置服务器版本为 9.4，则主目标的版本可以是 9.4 或 9.3，而不能是 9.5。
* 主目标只能是 VMware 虚拟机，而不能是物理服务器。

> [!NOTE]
> 切勿在主目标等任何管理组件上打开存储 vMotion。 如果重新保护成功后移动主目标，将无法分离虚拟机磁盘 (VMDK)。 此情况下，故障回复将失败。

## <a name="sizing-guidelines-for-creating-master-target-server"></a>创建主目标服务器时的大小调整准则

根据下列大小调整准则创建主目标：
- **RAM**：6 GB 或更多
- **OS 磁盘大小**：100 GB 或更多（用于安装 OS）
- **保留驱动器的附加磁盘大小**：1 TB
- **CPU 核心数**：4 个核心或更多
- 内核：4.16。*

## <a name="deploy-the-master-target-server"></a>部署主目标服务器

### <a name="install-ubuntu-16042-minimal"></a>安装 Ubuntu 16.04.2 最简版

按下列步骤安装 Ubuntu 16.04.2 64 位操作系统。

1.   转至[下载链接](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso)，并选择最接近的镜像，从中下载 Ubuntu 16.04.2 最简版 64 位 ISO。
将 Ubuntu 16.04.2 最简版 64 位 ISO 保存在 DVD 驱动器中，并启动系统。

>[!NOTE]
> 从 [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) 版开始，Linux 主目标服务器支持 Ubuntu 20.04 操作系统。如果希望使用最新 OS，请继续使用 Ubuntu 20.04 ISO 映像设置计算机。

1.  选择“英语”作为首选语言，再按 Enter。 
    
    ![选择一种语言](./media/vmware-azure-install-linux-master-target/image1.png)
1. 选择“安装 Ubuntu 服务器”，再按 Enter。 

    ![选择“安装 Ubuntu 服务器”](./media/vmware-azure-install-linux-master-target/image2.png)

1.  选择“英语”作为首选语言，再按 Enter。 

    ![选择“英语”作为首选语言](./media/vmware-azure-install-linux-master-target/image3.png)

1. 在“时区”选项列表中选择相应选项，再按 Enter。 

    ![选择正确的时区](./media/vmware-azure-install-linux-master-target/image4.png)

1. 选择“否”（默认选项），然后按 Enter。 

     ![配置键盘](./media/vmware-azure-install-linux-master-target/image5.png)
1. 选择“英语(美国)”作为键盘原产地语言，再按 Enter。 

1. 选择“英语(美国)”作为键盘布局，再按 Enter。 

1. 在“主机名”框中输入服务器的主机名，然后选择“继续”。 

1. 要创建用户帐户，请输入用户名，然后选择“继续”。

      ![创建用户帐户](./media/vmware-azure-install-linux-master-target/image9.png)

1. 为新用户帐户输入密码，然后选择“继续”。

1.  确认新用户的密码，再选择“继续”。

    ![确认密码](./media/vmware-azure-install-linux-master-target/image11.png)

1.  在加密主目录的下一项选择中，选择“否”（默认选项），再按 Enter。 

1. 如果显示的时区正确，请选择“是”（默认选项），然后按 Enter。  要重新配置时区，请选择“否”。

1. 在分区方法选项中选择“引导式 - 使用整个磁盘”，然后按 Enter。 

     ![选择分区方法选项](./media/vmware-azure-install-linux-master-target/image14.png)

1.  在“选择要分区的磁盘”选项中选择相应的磁盘，再按 Enter。 

    ![选择磁盘](./media/vmware-azure-install-linux-master-target/image15.png)

1.  选择“是”将更改写入磁盘，再按 Enter。 

    ![选择默认选项](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  在配置代理选项中选择默认选项，再选择“继续”按钮并按 Enter。 
     
     ![此屏幕截图显示了在何处选择“继续”，然后选择“Enter”。](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  在管理系统升级相应选项中选择“不自动更新”选项，再按 Enter。 

     ![选择如何管理升级](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > 由于 Azure Site Recovery 主目标服务器需要非常特定的 Ubuntu 版本，因此需确保已为虚拟机禁用内核升级。 如果启用，任意常规升级都会导致主目标服务器无法正常工作。 请务必选择“不自动更新”选项。

1.  选择默认选项。 若要对 SSH 连接使用 openSSH，请依次选择“OpenSSH 服务器”选项和“继续”。 

    ![选择软件](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. 在选择是否安装 GRUB 启动加载程序时，选择“是”，再按 Enter。 
     
    ![GRUB 启动安装程序](./media/vmware-azure-install-linux-master-target/image20.png)


1. 为启动加载程序安装选择相应的设备（推荐 /dev/sda），然后按 Enter。 
     
    ![选择适当的设备](./media/vmware-azure-install-linux-master-target/image21.png)

1. 选择“继续”，然后按 Enter 来完成安装。 

    ![完成安装](./media/vmware-azure-install-linux-master-target/image22.png)

1. 在完成安装后，使用新用户凭据登录 VM。 （有关详细信息，请参阅步骤 10。）

1. 按下列屏幕截图中所述步骤来设置 ROOT 用户密码。 然后以根用户身份登录。

    ![设置根用户密码](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>将计算机配置为主目标服务器

若要获取 Linux 虚拟机中每个 SCSI 硬盘的 ID，请启用 **disk.EnableUUID = TRUE** 参数。 要启用此参数，请使用以下步骤：

1. 关闭虚拟机。

2. 在左窗格中右键单击虚拟机对应的条目，并选择“编辑设置”。

3. 选择“选项”选项卡。

4. 在左窗格中，选择“高级” > “常规”，然后选择屏幕右下角的“配置参数”按钮。  

    ![打开“配置参数”](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    当计算机正在运行时，“配置参数”选项不可用。 若要使此选项卡处于活动状态，请关闭虚拟机。

5. 查看是否存在包含 **disk.EnableUUID** 的行。

   - 如果该值存在且设置为 False，请将它更改为 True。  （值不区分大小写。）

   - 如果该值存在且设置为 True，请选择“取消”。 

   - 如果该值不存在，请选择“添加行”。

   - 在名称列中，添加“disk.EnableUUID”，然后将值设置为 TRUE。 

     ![检查 disk.EnableUUID 是否存在](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>禁用内核升级

Azure Site Recovery 主目标服务器需要特定版本的 Ubuntu，请确保已为虚拟机禁用内核升级。 如果启用内核升级，它可能会导致主目标服务器无法正常工作。

#### <a name="download-and-install-additional-packages"></a>下载并安装其他包

> [!NOTE]
> 在下载并安装其他包之前，请确保已建立 Internet 连接。 如果没有 Internet 连接，需手动找到并安装这些 Deb 包。

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

>[!NOTE]
> 从 [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) 版开始，Linux 主目标服务器支持 Ubuntu 20.04 操作系统。
> 如果希望使用最新 OS，请在继续之前，将操作系统升级至 Ubuntu 20.04。 若要在以后升级操作系统，可以按照[此处](#upgrade-os-of-master-target-server-from-ubuntu-1604-to-ubuntu-2004)列出的说明进行操作。

### <a name="get-the-installer-for-setup"></a>获取安装程序

如果主目标已建立 Internet 连接，可以使用以下步骤下载安装程序。 或者，可从进程服务器复制该安装程序并执行安装。

#### <a name="download-the-master-target-installation-packages"></a>下载主目标安装包

[下载](https://aka.ms/latestlinuxmobsvc)适用于 Ubuntu 20.04 的最新 Linux 主目标安装程序。

[下载](https://aka.ms/oldlinuxmobsvc)适用于 Ubuntu 16.04 的旧版 Linux 主目标安装程序。

> [!NOTE]
> 建议使用最新的 Ubuntu 操作系统版本来设置主目标服务器。

若要使用 Linux 下载该安装程序，请键入：

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> 请务必将安装程序下载并解压缩到主目录。 如果解压缩到 /usr/Local，则安装会失败。


#### <a name="access-the-installer-from-the-process-server"></a>从进程服务器访问安装程序

1. 在进程服务器上，转到 C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository。

2. 从进程服务器复制所需的安装程序文件，并在主目录中将它保存为 latestlinuxmobsvc.tar.gz。


### <a name="apply-custom-configuration-changes"></a>应用自定义配置更改

若要应用自定义配置更改，请作为根用户使用以下步骤：

1. 运行以下命令解压缩二进制文件。

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![要运行的命令的屏幕截图](./media/vmware-azure-install-linux-master-target/image16.png)

2. 运行以下命令来指定权限。

    `chmod 755 ./ApplyCustomChanges.sh`


3. 运行以下命令来运行该脚本。
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> 仅在服务器上运行该脚本一次。 然后关闭服务器。 根据后续部分所述添加磁盘后，重启服务器。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>将保留磁盘添加到 Linux 主目标虚拟机

使用以下步骤创建保留磁盘：

1. 将新的 1-TB 磁盘附加到 Linux 主目标虚拟机，并启动计算机。

2. 通过 multipath -ll 命令了解保留磁盘的多路径 I：multipath -ll

    ![多路径 ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. 格式化驱动器，然后在新驱动器上创建文件系统：mkfs.ext4 /dev/mapper/\<Retention disk's multipath id>。
    
    ![文件系统](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. 创建文件系统后，请装载保留磁盘。

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. 创建每次系统启动期间用于装载保留驱动器的 fstab 项。
    
    `vi /etc/fstab`
    
    按 Insert 开始编辑文件。 创建新行并插入以下文本。 根据前一命令中突出显示的多路径 ID 编辑磁盘多路径 ID。

    **/dev/mapper/\<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    按 Esc，键入 :wq（写入并退出）来关闭编辑器窗口。 

### <a name="install-the-master-target"></a>安装主目标

> [!IMPORTANT]
> 主目标服务器的版本应该低于或等于进程服务器和配置服务器的版本。 如果不满足此条件，重新保护将成功，但复制会失败。


> [!NOTE]
> 安装主目标服务器之前，请检查虚拟机上的 /etc/hosts 文件是否包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。

1. 运行以下命令安装主目标。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. 在配置服务器上从 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 复制通行短语。 然后运行以下命令，将其作为 passphrase.txt 保留在同一本地目录中：

    `echo <passphrase> >passphrase.txt`

    示例： 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. 记下配置服务器的 IP 地址， 运行以下命令，将服务器注册到配置服务器。

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    示例： 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

等到脚本执行完成。 如果成功注册主目标，门户中的“Site Recovery 基础结构”页上会列出该主目标。


#### <a name="install-the-master-target-by-using-interactive-installation"></a>使用交互式安装来安装主目标

1. 运行以下命令安装主目标。 对于代理角色，选择“主目标”。

    ```
    ./install
    ```

2. 选择默认安装位置，并按 Enter 继续。

    ![选择主目标的默认安装位置](./media/vmware-azure-install-linux-master-target/image17.png)

安装完成后，使用命令行注册配置服务器。

1. 请注意配置服务器的 IP 地址。 因为下一步骤需要用到。

2. 运行以下命令，将服务器注册到配置服务器。

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     等到脚本执行完成。 如果成功注册主目标，门户中的“Site Recovery 基础结构”页上会列出该主目标。


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>在主目标服务器上安装 VMware 工具 / open-vm-tools

需将 VMware 工具或 open-vm-tools 安装在主目标上，使其可发现数据存储。 若未安装这些工具，则数据存储中不会列出重新保护屏幕。 安装 VMware 工具后，需重启计算机。

### <a name="upgrade-the-master-target-server"></a>升级主目标服务器

运行该安装程序会自动检测是否在主目标上安装了代理。 若要完成升级，请完成以下步骤：
1. 将 tar.gz 从配置服务器复制到 Linux 主目标
2. 运行以下命令来验证运行的版本：cat /usr/local/.vx_version
3. 提取 tar：tar -xvf latestlinuxmobsvc.tar.gz
4. 授予执行更改的权限：chmod 755 ./install
5. 运行升级脚本：sudo ./install
6. 安装程序应检测到代理已安装在主目标上。 选择“是”进行升级。
7. 验证代理是否正在运行新版本：cat /usr/local/.vx_version

安装完成后，可运行下列命令，检查安装的主目标版本：

`cat /usr/local/.vx_version`


“版本”字段中显示了主目标的版本号。

## <a name="upgrade-os-of-master-target-server-from-ubuntu-1604-to-ubuntu-2004"></a>将主目标服务器的 OS 从 Ubuntu 16.04 升级至 Ubuntu 20.04

从 9.42 版开始，ASR 支持 Ubuntu 20.04 上的 Linux 主目标服务器。 若要升级现有主目标服务器的 OS，请执行下列操作：

1. 确保未将 Linux 横向扩展主目标服务器用于任何受保护 VM 的重新保护操作。
2. 从计算机上卸载主目标服务器安装程序
3. 现在，将操作系统从 Ubuntu 16.04 升级至 20.04
4. 成功升级 OS 后，重启计算机。
5. 现在，[下载最新安装程序](#download-the-master-target-installation-packages)并按照[上述](#install-the-master-target)说明完成主目标服务器的安装。


## <a name="common-issues"></a>常见问题

* 切勿在主目标等任何管理组件上打开存储 vMotion。 如果重新保护成功后移动主目标，将无法分离虚拟机磁盘 (VMDK)。 此情况下，故障回复将失败。

* 主目标不应在虚拟机上留下任何快照。 如果有快照，故障回复会失败。

* 由于使用某些自定义 NIC 配置，网络接口已在启动期间被禁用，因此主目标代理无法初始化。 请确保正确设置以下属性。 在以太网卡文件 /etc/network/interfaces 中检查这些属性。
    * auto eth0
    * iface eth0 inet dhcp <br>

    使用以下命令重启网络服务： <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>后续步骤
主目标安装和注册完成后，在“Site Recovery 基础结构”中“主目标”部分的配置服务器概述下即会显示此主目标。

现在，可以继续执行[重新保护](vmware-azure-reprotect.md)过程，并执行故障回复。

