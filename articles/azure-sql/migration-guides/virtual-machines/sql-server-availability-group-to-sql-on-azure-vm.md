---
title: 迁移可用性组
description: 了解如何使用 Azure Migrate 将 Always On 可用性组高可用性解决方案直接迁移到 Azure VM 上的 SQL Server。
ms.service: virtual-machines-sql
ms.subservice: migration-guide
author: rahugup
manager: bsiva
ms.topic: how-to
ms.date: 4/25/2021
ms.author: rahugup
ms.openlocfilehash: 26c068450b93272986a1a3c82af0530adfa30ebb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766467"
---
# <a name="migrate-availability-group-to-sql-server-on-azure-vm"></a>将可用性组迁移到 Azure VM 上的 SQL Server

本文介绍如何使用 [Azure Migrate: 服务器迁移工具](../../../migrate/migrate-services-overview.md#azure-migrate-server-migration-tool)将 SQL Server Always On 可用性组迁移到 Azure VM 上的 SQL Server。 使用迁移工具，能够将可用性组中的每个副本迁移到托管 SQL Server、群集元数据、可用性组元数据和其他必要的高可用性组件的 Azure VM。 

在本文中，学习如何： 

> [!div class="checklist"]
> * 准备 Azure 和源环境以进行迁移。
> * 开始复制服务器。
> * 监视复制。
> * 运行完整的服务器迁移。
> * 重新配置 Always On 可用性组。 


本指南使用 Azure Migrate 的基于代理的迁移方法，该方法将任何服务器或虚拟机视为物理服务器。 迁移物理计算机时，“Azure Migrate: 服务器迁移”使用与 Azure Site Recovery 服务中基于代理的灾难恢复功能相同的复制体系结构，并且某些组件共享相同的代码库。 某些内容可能链接到 Site Recovery 文档。


## <a name="prerequisites"></a>先决条件


在开始学习本教程之前，应完成以下先决条件：

1. Azure 订阅。 如有必要，请创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。 
1. 安装 [Azure PowerShell `Az` 模块](/powershell/azure/install-az-ps)。 
1. 从 GitHub 存储库下载 [PowerShell 示例脚本](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/SQL%20Migration)。

## <a name="prepare-azure"></a>准备 Azure

准备 Azure 以使用[服务器迁移工具](../../../migrate/migrate-services-overview.md#azure-migrate-server-migration-tool)进行迁移。

|**任务** | **详细信息**|
|--- | ---
|**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能[创建新项目](../../../migrate/create-manage-projects.md)。|
|**验证 Azure 帐户的权限** | Azure 帐户需要拥有 Azure 订阅的参与者或所有者权限、用于注册 Azure Active Directory (AAD) 应用的权限、Azure 订阅上用于创建 Key Vault、创建 VM 以及写入 Azure 托管磁盘的“用户访问管理员”权限。 |
|**设置 Azure 虚拟网络** | [设置](../../../virtual-network/virtual-networks-overview.md) Azure 虚拟网络 (VNet)。 复制到 Azure 时，将创建 Azure VM 并将其加入到在设置迁移时指定的 Azure VNet。|


若要检查是否具有适当的权限，请执行以下步骤： 

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中，找到相关帐户并选择它以查看权限。
3. 你应该拥有“参与者”或“所有者”权限。 
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

如果需要分配权限，请按照[准备 Azure 用户帐户](../../../migrate/tutorial-discover-vmware.md#prepare-an-azure-user-account)中的步骤进行操作。


## <a name="prepare-for-migration"></a>准备迁移

若要为服务器迁移做准备，验证物理服务器设置，并做好准备来部署复制设备。

### <a name="check-machine-requirements"></a>检查计算机要求 


请确保源计算机符合迁移到 Azure 的要求。 请按照以下步骤操作： 

1. [验证](../../../migrate/migrate-support-matrix-physical-migration.md#physical-server-requirements)服务器要求。
1. 验证复制到 Azure 的源计算机是否符合 [Azure VM 要求](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements)。
1. 某些 [Windows](../../../migrate/prepare-for-migration.md#windows-machines) 源需要进行一些其他更改。 在进行这些更改之前迁移源会阻止 VM 在 Azure 中启动。 对于某些操作系统，Azure Migrate 可自动做出这些更改。 


### <a name="prepare-for-replication"></a>为复制做准备 

“Azure Migrate:Azure Migrate 使用复制设备将计算机复制到 Azure。 复制设备运行以下组件： 

- **配置服务器**：配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 中的缓存存储帐户。 

为设备部署做好准备，如下所述：

- 创建一台用于托管复制设备的 Windows Server 2016 计算机。 查看[计算机要求](../../../migrate/migrate-replication-appliance.md#appliance-requirements)。
- 复制设备使用 MySQL。 查看在设备上安装 MySQL 的[选项](../../../migrate/migrate-replication-appliance.md#mysql-installation)。
- 查看复制设备访问[公有云](../../../migrate/migrate-replication-appliance.md#url-access)和[政府云](../../../migrate/migrate-replication-appliance.md#azure-government-url-access)时所需的 Azure URL。
- 查看复制设备的[端口](../../../migrate/migrate-replication-appliance.md#port-access)访问要求。

> [!NOTE]
> 复制设备不应安装在要复制或迁移的源计算机上，也不应安装在以前可能已安装 Azure Migrate 发现和评估设备的计算机上。

### <a name="download-replication-appliance-installer"></a>下载复制设备安装程序

若要下载复制设备安装程序，请执行以下步骤： 

1. 在 Azure Migrate 项目中选择“服务器”，在“Azure Migrate: 服务器迁移”中，选择“发现”。

    ![发现 VM](../../../migrate/media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

1. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“物理或其他(AWS、GCP 和 Xen 等)”  。
1. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。
1. 选择“确认迁移的目标区域为 <区域名称>”。
1. 选择“创建资源”。 随即会在后台创建一个 Azure Site Recovery 保管库。
    - 如果已使用 Azure Migrate 的服务器迁移工具设置迁移，则由于已设置资源，因此无法配置目标选项。    
    - 选择此按钮后，无法更改此项目的目标区域。
    - 所有后续迁移的目标都是此区域。

1. 在“是否安装新的复制设备?”中，选择“安装复制设备”。 
1. 在“下载并安装复制设备软件”中，下载设备安装程序和注册密钥。 需要使用该密钥来注册设备。 下载的密钥有效期为 5 天。

    ![下载提供程序](../../../migrate/media/tutorial-migrate-physical-virtual-machines/download-provider.png)

1. 将设备安装程序文件和密钥文件复制到为该设备创建的 Windows Server 2016 计算机。
1. 安装完成后，设备配置向导将自动启动（也可使用在设备计算机的桌面上创建的 cspsconfigtool 快捷方式手动启动该向导）。 使用向导的“管理帐户”选项卡创建具有以下详细信息的虚拟帐户：

   -  “guest”作为易记名称
   -  “username”作为用户名
   -  “password”作为帐户密码。 
   
   你将在“启用复制”阶段使用此虚拟帐户。 

1. 设备已安装并重启后，在“发现计算机”中的“选择配置服务器”内选择新设备，然后选择“完成注册”。 “完成注册”步骤会执行最终的几个任务来准备复制设备。

    ![完成注册](../../../migrate/media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)


## <a name="install-mobility-service"></a>安装移动服务

在要迁移的服务器上安装移动服务代理。 复制设备上已提供代理安装程序。 找到适当的安装程序，并在要迁移的每台计算机上安装该代理。 


若要安装移动服务，请执行以下步骤： 

1. 登录到复制设备。
1. 导航到 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**。
1. 找到适用于计算机操作系统和版本的安装程序。 查看[支持的操作系统](../../../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 
1. 将安装程序文件复制到要迁移的计算机。
1. 确保已准备好在部署设备时生成的通行短语。
    - 将该通行短语存储在计算机上的某个临时文本文件中。
    - 可在复制设备上获取通行短语。 在命令行中，运行 **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** 查看当前的通行短语。
    - 不要重新生成通行短语。 否则会中断连接，并需要重新注册复制设备。
    - 在“/Platform”参数中，对于 VMware 计算机和物理计算机，均指定 VMware。 

1. 连接到计算机并将安装程序文件的内容提取到本地文件夹（例如 c:\temp）。 在管理员命令提示符中运行以下命令： 

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

安装发现的计算机后，可能需要一些时间才能将其显示在“Azure Migrate: 服务器迁移”中。 随着 VM 的发现，“已发现的服务器”计数会不断增大。

![已发现的服务器](../../../migrate/media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)

## <a name="prepare-source-machines"></a>准备源计算机

若要准备源计算机，请对群集节点运行 `Get-ClusterInfo.ps1` 脚本以检索有关群集资源的信息。 该脚本将在 `Cluster-Config.csv` 文件中输出角色名称、资源名称、IP 和探测端口。 

```powershell
./Get-ClusterInfo.ps1
```

## <a name="create-load-balancer"></a>创建负载均衡器

为了使群集和群集角色正确响应请求，需要 Azure 负载均衡器。 如果没有负载均衡器，其他 VM 将无法访问群集 IP 地址，因为它无法被识别为属于网络或群集。 

若要创建负载均衡器，请执行以下步骤： 

1. 填写 `Cluster-Config.csv` 文件中的列：

列标题 | **说明**
--- | ---
NewIP | 为 CSV 文件的每个资源指定 Azure 虚拟网络（或子网）中的 IP 地址。
ServicePort | 指定 CSV 文件中每个资源使用的服务端口。 对于 SQL 群集资源，请使用与 CSV 中的探测端口相同的服务端口值。 对于其他群集角色，使用的默认值为 1433，但你可以继续使用在当前设置中配置的端口号。 


2. 运行 `Create-ClusterLoadBalancer.ps1` 脚本，使用以下参数创建负载均衡器： 

**参数** | **类型** | **说明**
--- | --- | ---
ConfigFilePath | 必需| 指定在上一步中填写的 `Cluster-Config.csv` 文件的路径。
ResourceGroupName | 必需|指定要在其中创建负载均衡器的资源组的名称。 
VNetName | 必需|指定负载均衡器将关联到的 Azure 虚拟网络的名称。 
SubnetName | 必需|指定负载均衡器将关联到的 Azure 虚拟网络中的子网的名称。 
VNetResourceGroupName | 必需|指定负载均衡器将关联到的 Azure 虚拟网络的资源组的名称。 
位置 | 必需|指定应在其中创建负载均衡器的位置。 
LoadBalancerName | 必需|指定要创建的负载均衡器的名称。 


```powershell
./Create-ClusterLoadBalancer.ps1 -ConfigFilePath ./clsuterinfo.csv -ResourceGroupName $resoucegroupname -VNetName $vnetname -subnetName $subnetname -VnetResourceGroupName $vnetresourcegroupname -Location "eastus" -LoadBalancerName $loadbalancername
```

## <a name="replicate-machines"></a>复制计算机

现在，选择要迁移的 计算机。 最多可以同时复制 10 台计算机。 如果需要复制更多，则可以每批 10 台同时复制它们。

若要复制计算机，请执行以下步骤： 

1. 在 Azure Migrate 项目中选择“服务器”，在“Azure Migrate: 服务器迁移”中，选择“复制”。

    ![“Azure Migrate - 服务器”屏幕的屏幕截图，显示在“迁移工具”下的 Azure Migrate:服务器迁移”中选择了“复制”按钮](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

1. 在“复制”>“源设置” > “计算机是否已虚拟化?”中，选择“物理或其他(AWS、GCP 和 Xen 等)”。
1. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称。
1. 在“进程服务器”中，选择复制设备的名称。
1. 在“来宾凭据”中，选择先前在本文的[复制安装程序设置](#download-replication-appliance-installer)过程中创建的虚拟帐户。 然后选择“下一步: 虚拟机”。   

    ![“复制”屏幕中“源设置”选项卡的屏幕截图，其中突出显示了“来宾凭据”字段。](../../../migrate/media/tutorial-migrate-physical-virtual-machines/source-settings.png)

1. 在“虚拟机”中的“从评估中导入迁移设置?”内，保留默认设置“否，我将手动指定迁移设置”。  
1. 检查要迁移的每个 VM。 然后选择“下一步: 目标设置”。

    ![选择 VM](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-vms.png)


1. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。
1. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。
1. 在“可用性选项”中，选择：
    -  可用性区域，将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 如果选择此选项，则需要在“计算”选项卡中指定用于每个选定计算机的可用性区域。仅当为迁移选择的目标区域支持可用性区域时，此选项才可用。
    -  可用性集，将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。
    - 如果不需要为迁移的计算机使用其中任何可用性配置，则选择“无需基础结构冗余”选项。
    
1. 在“磁盘加密类型”中，选择以下类型：
    - 使用平台管理的密钥进行静态加密
    - 使用客户管理的密钥进行静态加密
    - 通过平台管理的密钥和客户管理的密钥进行双重加密

    > [!NOTE]
    > 若要使用 CMK 复制 VM，需要在目标资源组下[创建磁盘加密集](../../../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)。 磁盘加密集对象将托管磁盘映射到包含 CMK 的密钥保管库，以用于 SSE。
  
1. 在“Azure 混合权益”中：

    - 如果你不想要应用 Azure 混合权益，请选择“否”。 然后，选择“下一步”。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。 然后，选择“下一步”。

    :::image type="content" source="../../../migrate/media/tutorial-migrate-vmware/target-settings.png" alt-text="目标设置":::

1. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性配置（如果在上一步中选定）。 VM 必须符合 [Azure 要求](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表会显示建议大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。
    - **可用性区域**：指定要使用的可用性区域。
    - **可用性集**：指定要使用的可用性集。

   ![计算设置](../../../migrate/media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

1. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。 然后，选择“下一步”。  

    ![磁盘设置](../../../migrate/media/tutorial-migrate-physical-virtual-machines/disks.png)

1. 在“检查并开始复制”中检查设置，然后选择“复制”启动服务器的初始复制。

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。 开始复制后无法更改设置。

## <a name="track-and-monitor"></a>跟踪和监视

按以下顺序执行复制： 

- 选择“复制”会启动一个“开始复制”作业。 
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。


可以在门户通知中跟踪作业状态。

可以通过选择“Azure Migrate: 服务器迁移”中的“复制服务器”来监视复制状态。
![监视复制](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)


## <a name="migrate-vms"></a>迁移 VM

复制计算机后，即可进行迁移。 若要迁移服务器，请执行以下步骤： 


1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate: 服务器迁移”，然后选择“复制服务器”。

    ![复制服务器](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. 若要确保已迁移的服务器与源服务器同步，请停止可用性组中每个副本上的 SQL Server 服务，从次要副本开始（在“SQL Server 配置管理器” > “服务”中），同时确保托管 SQL 数据的磁盘处于联机状态。   
3. 在“复制计算机”中，选择“服务器名称”>“概述”，确保上次同步的时间戳是在停止服务器上要迁移的 SQL Server 服务之后，再继续执行下一步。 此过程应该只需要几分钟的时间。 
2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“否” > “确定”。
   
    > [!NOTE]
    > 对于物理服务器迁移，不会自动支持关闭源计算机。 建议在迁移时段中将应用程序关闭（不要让应用程序接受任何连接），然后启动迁移（服务器需要保持运行，这样，剩余的更改可以在迁移完成前同步）。

4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。

## <a name="reconfigure-cluster"></a>重新配置群集 

迁移 VM 后，重新配置群集。 请按照以下步骤操作： 

1. 关闭 Azure 中已迁移的服务器。
1.  将已迁移的计算机添加到负载均衡器的后端池。 导航到“负载均衡器” > “后端池”，选择“后端池”>“添加已迁移的计算机”。 3. 在 Azure 中启动已迁移的服务器并登录到任何节点。 
1. 复制 `ClusterConfig.csv` 文件并运行将 CSV 作为参数传递的 `Update-ClusterConfig.ps1` 脚本。 这将确保使用新配置更新群集资源，使群集在 Azure 中正常工作。 

   ```powershell
   ./Update-ClusterConfig.ps1 -ConfigFilePath $filepath
   ```

Always On 可用性组处于就绪状态。 

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。 这样会执行以下操作：
    - 停止本地计算机的复制。
    - 从 Azure Migrate 的“复制服务器”计数中删除该计算机：服务器迁移。
    - 清除计算机的复制状态信息。
2. 在已迁移的计算机上安装 Azure VM [Windows](../../../virtual-machines/extensions/agent-windows.md) 代理。
3. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
4. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
5. 将流量交接到已迁移的 Azure VM 实例。
6. 从本地 VM 清单中删除本地 VM。
7. 从本地备份中删除本地 VM。
8. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。 

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 对于 SQL Server：
    -  安装 [SQL Server IaaS 代理扩展](../../virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)，以自动执行管理任务。 
    - [优化](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md) Azure VM 上的 SQL Server 性能。 
    - 了解 Azure 上的 SQL Server [定价](../../virtual-machines/windows/pricing-guidance.md#free-licensed-sql-server-editions)。 
- 为提高恢复能力，请执行以下操作：
    - 使用 [Azure 备份服务](../../../backup/quick-backup-vm-portal.md)备份 Azure VM 以保证数据安全。 
    - 使用 [Site Recovery](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md) 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](../../../security-center/security-center-just-in-time.md)锁定和限制入站流量访问。
    - 使用[网络安全组](../../../virtual-network/network-security-groups-overview.md)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
    - 考虑部署[Azure 成本管理](../../../cost-management-billing/cost-management-billing-overview.md)以监视资源使用率和支出。


## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。