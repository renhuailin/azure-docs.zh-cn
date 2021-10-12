---
title: 使用 Azure Migrate 将计算机作为物理服务器迁移到 Azure。
description: 本文介绍如何使用 Azure Migrate 将物理计算机迁移到 Azure。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: 00d069257d25441f16fb82cae720bb49b8e0f15f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536938"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>将计算机作为物理服务器迁移到 Azure

本文介绍如何使用 Azure Migrate 的服务器迁移工具将计算机作为物理服务器迁移到Azure。 通过将计算机视为物理服务器来迁移计算机在许多情况下很有用：

- 迁移本地物理服务器。
- 迁移 Xen、KVM 等平台虚拟化的 VM。
- 迁移 Hyper-V 或 VMware VM（如果由于某种原因无法使用标准迁移过程进行 [Hyper-V](tutorial-migrate-hyper-v.md) 或 [VMware](server-migrate-overview.md) 迁移）。
- 迁移私有云中运行的 VM。
- 迁移 Amazon Web Services (AWS) 或 Google Cloud Platform (GCP) 等公有云中运行的 VM。


本教程是教程系列中的第三篇教程，演示了如何评估物理服务器并将其迁移到 Azure。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备将 Azure 与 Azure Migrate 一起使用：服务器迁移。
> * 检查要迁移的计算机的要求，并准备 Azure Migrate 复制设备的计算机，用于发现计算机并将其迁移到 Azure。
> * 添加 Azure Migrate：Azure Migrate 中心内的服务器迁移工具。
> * 设置复制设备。
> * 在要迁移的计算机上安装移动服务。
> * 启用复制。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行到 Azure 的完整迁移。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看 Azure Migrate 的操作指南。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

开始学习本教程之前，应做好以下准备：

[查看](./agent-based-migration-architecture.md)迁移体系结构。

## <a name="prepare-azure"></a>准备 Azure

让 Azure 准备好使用 Azure Migrate 进行迁移：服务器迁移。

**任务** | **详细信息**
--- | ---
**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能[创建新项目](./create-manage-projects.md)。
**验证 Azure 帐户的权限** | Azure 帐户需要有权创建 VM 并将数据写入 Azure 托管磁盘。


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。
3. 你应该拥有“参与者”或“所有者”权限。 
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。


### <a name="assign-azure-account-permissions"></a>分配 Azure 帐户权限

将“虚拟机参与者”角色分配给 Azure 帐户。 这提供了执行以下操作的权限：

- 在所选资源组中创建 VM。
- 在所选虚拟网络中创建 VM。
- 写入 Azure 托管磁盘。

### <a name="create-an-azure-network"></a>创建 Azure 网络

[设置](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure 虚拟网络 (VNet)。 复制到 Azure 时，将创建 Azure VM 并将其加入到在设置迁移时指定的 Azure VNet。

## <a name="prepare-for-migration"></a>准备迁移

若要为物理服务器迁移做准备，你需要验证物理服务器设置，并做好准备来部署复制设备。

### <a name="check-machine-requirements-for-migration"></a>检查要迁移的计算机的要求

确保计算机符合迁移到 Azure 的要求。

> [!NOTE]
> 迁移物理计算机时，Azure Migrate 的服务器迁移工具会使用与 Azure Site Recovery 服务中基于代理的灾难恢复功能相同的复制体系结构，并且某些组件共享同一代码库。 某些内容可能链接到 Site Recovery 文档。

1. [验证](migrate-support-matrix-physical-migration.md#physical-server-requirements)物理服务器要求。
2. 验证复制到 Azure 的本地计算机是否符合 [Azure VM 要求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。
3. 在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。
    - 对于某些操作系统，Azure Migrate 可自动做出这些更改。
    - 在开始迁移之前，必须做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。
查看在 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 上需要进行的更改。

### <a name="prepare-a-machine-for-the-replication-appliance"></a>为复制设备准备计算机

“Azure Migrate:Azure Migrate 使用复制设备将计算机复制到 Azure。 复制设备运行以下组件。

- **配置服务器**：配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 中的缓存存储帐户。

为设备部署做好准备，如下所述：

- 准备好用来托管复制设备的计算机。 [查看](migrate-replication-appliance.md#appliance-requirements)计算机要求。
- 复制设备使用 MySQL。 查看在设备上安装 MySQL 的[选项](migrate-replication-appliance.md#mysql-installation)。
- 查看复制设备访问[公有云](migrate-replication-appliance.md#url-access)和[政府云](migrate-replication-appliance.md#azure-government-url-access)时所需的 Azure URL。
- 查看复制设备的[端口](migrate-replication-appliance.md#port-access)访问要求。

> [!NOTE]
> 复制设备不应安装在要复制的源计算机上，也不应安装在以前可能已安装的 Azure Migrate 发现和评估设备上。

## <a name="set-up-the-replication-appliance"></a>设置复制设备

第一个迁移步骤是设置复制设备。 若要设置设备以进行物理服务器迁移，请下载设备的安装程序文件，然后在[准备好的计算机](#prepare-a-machine-for-the-replication-appliance)上运行。 安装设备后，将其注册到 Azure Migrate 的服务器迁移。


### <a name="download-the-replication-appliance-installer"></a>下载复制设备安装程序

1. 在 Azure Migrate 项目中选择“服务器”，在“Azure Migrate: 服务器迁移”中，单击“发现”。

    ![发现 VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“未虚拟化/其他”。  
3. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。
4. 选择“确认迁移的目标区域为 <区域名称>”。
5. 单击“创建资源”。 随即会在后台创建一个 Azure Site Recovery 保管库。
    - 如果已使用 Azure Migrate 的服务器迁移工具设置迁移，则由于已设置资源，因此无法配置目标选项。    
    - 单击此按钮后，无法更改此项目的目标区域。
    - 所有后续迁移的目标都是此区域。
    > [!NOTE]
    > 如果在创建 Azure Migrate 项目时选择了专用终结点作为该项目的连接方法，则还将为专用终结点连接配置恢复服务保管库。 确保可从复制设备访问专用终结点。 [**了解详细信息**](troubleshoot-network-connectivity.md)

6. 在“是否安装新的复制设备?”中，选择“安装复制设备”。 
7. 在“下载并安装复制设备软件”中，下载设备安装程序和注册密钥。 需要使用该密钥来注册设备。 下载的密钥有效期为 5 天。

    ![下载提供程序](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. 将设备安装程序文件和密钥文件复制到为该设备创建的 Windows Server 2016 计算机。

9. 安装完成后，设备配置向导将自动启动（也可使用在设备的桌面上创建的 cspsconfigtool 快捷方式手动启动该向导）。 在本教程中，我们将在要复制的源 VM 上手动安装移动服务，因此请在此步骤中创建一个虚拟帐户，然后继续。 可以提供以下详细信息来创建虚拟帐户：“guest”作为友好名称，“username”作为用户名，“password”作为帐户密码。 你将在“启用复制”阶段使用此虚拟帐户。

10. 设备已安装并重启后，在“发现计算机”中的“选择配置服务器”内选择新设备，然后单击“完成注册”。   “完成注册”步骤会执行最终的几个任务来准备复制设备。

    ![完成注册](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

完成注册后，可能需要一些时间才能将发现的计算机显示在 Azure Migrate 的服务器迁移。 随着 VM 的发现，“已发现的服务器”计数会不断增大。

![已发现的服务器](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>安装移动服务

在要迁移的计算机上，需要安装移动服务代理。 复制设备上已提供代理安装程序。 请找到适当的安装程序，并在要迁移的每台计算机上安装该代理。 请按如下所述执行此操作：

1. 登录到复制设备。
2. 导航到 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**。
3. 找到适用于计算机操作系统和版本的安装程序。 查看[支持的操作系统](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 将安装程序文件复制到要迁移的计算机。
5. 确保已准备好在部署设备时生成的通行短语。
    - 将该通行短语存储在计算机上的某个临时文本文件中。
    - 可在复制设备上获取通行短语。 在命令行中，运行 **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** 查看当前的通行短语。
    - 不要重新生成通行短语。 否则会中断连接，并需要重新注册复制设备。

> [!NOTE]
> 在“/Platform”参数中，指定 VMware（如果你迁移 VMware VM）或物理计算机 。

### <a name="install-on-windows"></a>在 Windows 上安装

1. 将安装程序文件的内容解压缩到计算机上的某个本地文件夹（例如 C:\Temp），如下所示：

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. 运行移动服务安装程序：
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. 将代理注册到复制设备：
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>在 Linux 上安装

1. 将安装程序 tarball 的内容解压缩到计算机上的某个本地文件夹（例如 /tmp/MobSvcInstaller），如下所示：
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. 运行安装程序脚本：
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. 将代理注册到复制设备：
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>复制计算机

现在，选择要迁移的 计算机。

> [!NOTE]
> 最多可以同时复制 10 台计算机。 如果需要复制更多，则可以每批 10 台同时复制它们。

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate: 服务器迁移”，然后单击“复制”。

    ![“Azure Migrate - 服务器”屏幕的屏幕截图，显示在 Azure Migrate:服务器迁移”的“迁移工具”下选择了“复制”按钮。](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化?”中，选择“未虚化/其他”   。
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称。
4. 在“进程服务器”中，选择复制设备的名称。
5. 在“来宾凭据”中，请选择以前在[复制安装程序设置](#download-the-replication-appliance-installer)过程中创建的虚拟帐户，以手动安装出行服务（不支持推送安装）。 然后单击“下一页:虚拟机”。   

    ![“复制”屏幕中“源设置”选项卡的屏幕截图，其中突出显示了“来宾凭据”字段。](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

6. 在“虚拟机”中的“从评估中导入迁移设置?”内，保留默认设置“否，我将手动指定迁移设置”。  
7. 检查要迁移的每个 VM。 然后单击“下一页:目标设置”。

    ![选择 VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


8. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。
9. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。   
10. 在“缓存存储帐户”中，保留默认选项，以使用为项目自动创建的缓存存储帐户。 如果要指定其他存储帐户用作复制的缓存存储帐户，请使用下拉列表。 <br/>
    > [!NOTE]
    >
    > - 如果选择了专用终结点作为 Azure Migrate 项目的连接方法，请向恢复服务保管库授予对缓存存储帐户的访问权限。 [**了解详细信息**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - 若要使用结合专用对等互连的 ExpressRoute 进行复制，请为缓存存储帐户创建专用终结点。 [**了解详细信息**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional)

11. 在“可用性选项”中，选择：
    -  可用性区域，将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 如果选择此选项，则需要在“计算”选项卡中指定用于每个选定计算机的可用性区域。仅当为迁移选择的目标区域支持可用性区域时，此选项才可用
    -  可用性集，将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。
    - 如果不需要为迁移的计算机使用其中任何可用性配置，则选择“无需基础结构冗余”选项。

12. 在“磁盘加密类型”中，选择以下类型：
    - 使用平台管理的密钥进行静态加密
    - 使用客户管理的密钥进行静态加密
    - 通过平台管理的密钥和客户管理的密钥进行双重加密

   > [!NOTE]
   > 若要使用 CMK 复制 VM，需要在目标资源组下[创建磁盘加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)。 磁盘加密集对象将托管磁盘映射到包含 CMK 的密钥保管库，以用于 SSE。

13. 在“Azure 混合权益”中：

    - 如果你不想要应用 Azure 混合权益，请选择“否”。 然后单击“下一步”。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。 然后单击“下一步”。

    ![目标设置](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

14. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性配置（如果在上一步中选定）。 VM 必须符合 [Azure 要求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表会显示建议大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。
    - **可用性区域**：指定要使用的可用性区域。
    - **可用性集**：指定要使用的可用性集。

![计算设置](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。 然后单击“下一步”。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。

    ![磁盘设置](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。 

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。 开始复制后无法更改设置。

## <a name="track-and-monitor"></a>跟踪和监视

- 单击“复制”会启动一个“开始复制”作业。
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。


可以在门户通知中跟踪作业状态。

可以通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器” 来监视复制状态。
![监视复制](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器迁移”中，单击“测试已迁移的服务器”。

     ![测试已迁移的服务器](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。

    :::image type="content" source="./media/tutorial-migrate-physical-virtual-machines/test-migrate-inline.png" alt-text="显示单击“测试迁移”后的结果的屏幕截图。" lightbox="./media/tutorial-migrate-physical-virtual-machines/test-migrate-expanded.png":::
 
3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。 建议使用非生产 VNet。
4. “测试迁移”作业随即启动。 在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。 计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。

    :::image type="content" source="./media/tutorial-migrate-physical-virtual-machines/clean-up-inline.png" alt-text="显示清理测试迁移后的结果的屏幕截图。" lightbox="./media/tutorial-migrate-physical-virtual-machines/clean-up-expanded.png":::

    > [!NOTE]
    > 现在可以使用 SQL VM RP 注册运行 SQL Server 的服务器，以利用自动修补、自动备份和使用 SQL IaaS 代理扩展简化的许可证管理。
    >- 选择“管理” > “复制服务器” > “包含 SQL Server 的计算机” > “计算和网络”，然后选择“是”，注册 SQL VM RP。
    >- 如果你的 SQL Server 实例享有有效软件保障或 SQL Server 订阅的权益，并且你需要将此权益应用到所要迁移的计算机，请选择“面向 SQL Server 的 Azure 混合权益”。

## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate: 服务器迁移”，然后单击“复制服务器”。

    ![复制服务器](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。   
    - 如果你不想要关闭 VM，请选择“否”

    注意：对于物理服务器迁移，建议在迁移时段中将应用程序关闭（不要让应用程序接受任何连接），然后启动迁移（服务器需要保持运行，这样，剩余的更改可以在迁移完成前同步）。

4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止复制”。 这样会执行以下操作：
    - 停止本地计算机的复制。
    - 从 Azure Migrate 的“复制服务器”计数中删除该计算机：服务器迁移。
    - 清除计算机的复制状态信息。
1. 验证并[排查 Azure VM 上的所有 Windows 激活问题。](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems) 
1. 执行任何迁移后的应用调整，例如更新主机名、数据库连接字符串和 Web 服务器配置。
1. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
1. 将流量交接到已迁移的 Azure VM 实例。
1. 从本地 VM 清单中删除本地 VM。
1. 从本地备份中删除本地 VM。
1. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](../security-center/security-center-just-in-time.md)锁定和限制入站流量访问。
    - 使用[网络安全组](../virtual-network/network-security-groups-overview.md)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
    - 考虑部署[Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)以监视资源使用率和支出。


## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。
