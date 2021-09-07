---
title: 使用基于代理的 Azure Migrate 服务器迁移功能迁移 VMware VM
description: 了解如何使用 Azure Migrate 运行基于代理的 VMware VM 迁移。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: be43dda1e1fdf6d23031f2d2bf75a7f126ddef7a
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272828"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>将 VMware VM 迁移到 Azure（使用基于代理的方法）

本文介绍如何通过基于代理的迁移，使用 [Azure Migrate:服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具将本地 VMware VM 迁移到 Azure。  还可以使用无代理迁移来迁移 VMware VM。 [比较](server-migrate-overview.md#compare-migration-methods)方法。


 在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 准备 Azure 以便使用 Azure Migrate。
> * 为基于代理的迁移做好准备。 设置 VMware 帐户，使 Azure Migrate 可以发现要迁移的计算机。 设置一个帐户，使移动服务代理可以安装在要迁移的计算机上，并准备计算机以充当复制设备。
> * 添加 Azure Migrate:服务器迁移工具
> * 设置复制设备。
> * 复制 VM。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行到 Azure 的完整迁移。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

在开始本教程之前，[查看](./agent-based-migration-architecture.md)基于 VMware 代理的迁移体系结构。

## <a name="prepare-azure"></a>准备 Azure

完成表中的任务，使 Azure 准备好进行基于代理的迁移。

**任务** | **详细信息**
--- | ---
**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能创建项目。
**验证 Azure 帐户权限** | Azure 帐户需要有权创建 VM 并将数据写入 Azure 托管磁盘。
**设置 Azure 网络** | 设置 Azure VM 迁移后要加入的网络。

### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限
如果没有 Azure Migrate 项目，请验证权限以创建一个项目。


1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。
3. 验证你拥有“参与者”或“所有者”权限 。

    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-azure-account-permissions"></a>分配 Azure 帐户权限

为“虚拟机参与者”角色分配帐户，以便你有权执行以下操作：

- 在所选资源组中创建 VM。
- 在所选虚拟网络中创建 VM。
- 写入 Azure 托管磁盘。


### <a name="set-up-an-azure-network"></a>设置 Azure 网络

[设置 Azure 网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 本地计算机将复制到 Azure 托管磁盘。 在故障转移到 Azure 进行迁移时，将基于这些托管磁盘创建 Azure VM，并将其加入到你设置的 Azure 网络。

## <a name="prepare-for-migration"></a>准备迁移

验证支持要求和权限，并准备部署复制设备。

### <a name="prepare-an-account-to-discover-vms"></a>准备帐户以发现 VM

Azure Migrate 服务器迁移需要有权访问 VMware 服务器，以发现要迁移的 VM。 按如下所述创建此帐户：

1. 若要使用专用帐户，请 vCenter 级别创建一个角色。 为该角色指定一个名称，例如 Azure_Migrate。
2. 为该角色分配下表中汇总的权限。
3. 在 vCenter 服务器或 vSphere 主机上创建一个用户。 向该用户分配角色。

#### <a name="vmware-account-permissions"></a>VMware 帐户权限

**任务** | **角色/权限** | **详细信息**
--- | --- | ---
**VM 发现** | 至少一个只读用户<br/><br/> 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 若要限制访问权限，请使用“传播到子对象”将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。
**复制** |  创建一个拥有所需权限的角色 (Azure_Site_Recovery)，然后将该角色分配到 VMware 用户或组<br/><br/> 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 若要限制访问权限，请使用“传播到子对象”将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的计算机上安装移动服务。

- 为计算机启用了复制时，Azure Migrate 复制设备可以执行此服务的推送安装，你也可以手动安装或使用安装工具安装。
- 在本教程中，我们将使用推送安装来安装移动服务。
- 若要执行推送安装，需要准备一个供 Azure Migrate 服务器迁移用来访问 VM 的帐户。 如果不手动安装移动服务，则此帐户仅用于推送安装。

按如下所述准备此帐户：

1. 准备一个在 VM 上具有安装权限的域或本地帐户。
2. 对于 Windows VM，如果使用的不是域帐户，请在本地计算机上禁用远程用户访问控制：在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，添加 DWORD 项 **LocalAccountTokenFilterPolicy** 并将值设为 。
3. 对于 Linux VM，请在源 Linux 服务器上准备一个 root 帐户。


### <a name="prepare-a-machine-for-the-replication-appliance"></a>为复制设备准备计算机

设备用于将计算机复制到 Azure。 此设备是单个高度可用的本地 VMware VM，其中托管了以下组件：

- **配置服务器**：配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 中的缓存存储帐户。 进程服务器还会将移动服务代理安装在要复制的 VM 上，并在本地 VMware VM 上执行自动发现。

按以下所示准备设备：

- [查看设备要求](migrate-replication-appliance.md#appliance-requirements)。 通常，使用下载的 OVA 文件将复制设备设置为 VMware VM。 该模板创建符合所有要求的设备。
- MySQL 必须安装在设备上。 [查看](migrate-replication-appliance.md#mysql-installation)安装方法。
- 查看设备计算机需要访问的[公有云 URL](migrate-replication-appliance.md#url-access) 和 [Azure 政府 URL](migrate-replication-appliance.md#azure-government-url-access)。
- [查看复制设备计算机需要访问的端口](migrate-replication-appliance.md#port-access)。



### <a name="check-vmware-requirements"></a>检查 VMware 要求

确保 VMware 服务器和 VM 符合迁移到 Azure 的要求。

1. [验证](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) VMware 服务器要求。
2. [验证](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) VM 的迁移要求。
3. 验证 Azure 设置。 复制到 Azure 的本地 VM 必须符合 [Azure VM 要求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。
4. 在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。
    - 在开始迁移之前，必须做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。
    - 查看在 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 上需要进行的更改。

> [!NOTE]
> 使用 Azure Migrate 服务器迁移进行基于代理的迁移依赖于 Azure Site Recovery 服务的功能。 某些要求可能提供了 Site Recovery 文档的链接。

## <a name="set-up-the-replication-appliance"></a>设置复制设备

此过程介绍如何使用下载的开放式虚拟化应用程序 (OVA) 模板设置设备。 如果不能使用此方法，可以[使用脚本](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)设置设备。

### <a name="download-the-replication-appliance-template"></a>下载复制设备模板

按如下所述下载模板：

1. 在 Azure Migrate 项目中，单击“迁移目标”下的“服务器”。
2. 在“Azure Migrate - 服务器” > “Azure Migrate: 服务器迁移”中，单击“发现”。

    ![发现 VM](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“是，使用 VMware vSphere 虚拟机监控程序”  。
4. 在“想要如何迁移?”中，选择“使用基于代理的复制”。
5. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。
6. 选择“确认迁移的目标区域为 <区域名称>”。
7. 单击“创建资源”。 随即会在后台创建一个 Azure Site Recovery 保管库。 单击此按钮后，无法更改此项目的目标区域，并且所有后续迁移都指向此区域。

    ![创建恢复服务保管库](./media/tutorial-migrate-vmware-agent/create-resources.png)  

    > [!NOTE]
    > 如果在创建 Azure Migrate 项目时选择了专用终结点作为该项目的连接方法，则还将为专用终结点连接配置恢复服务保管库。 确保可从复制设备访问专用终结点：[了解详细信息](troubleshoot-network-connectivity.md)


8. 在“是否安装新的复制设备?”中，选择“安装复制设备”。 
9. 单击“下载”。 这将下载 OVF 模板。
    ![下载 OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. 记下资源组和恢复服务保管库的名称。 在部署设备期间需要用到这些名称。


### <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板

下载 OVF 模板后，将其导入 VMware，以便在运行 Windows Server 2016 的 VMware VM 上创建复制应用程序。

1. 使用 VMware vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导  。
3. 在“选择源”中，输入下载的 OVF 所在的位置。
4. 在“查看详细信息”中，选择“下一步”。
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置 。
6. 在“选择存储” > “选择虚拟磁盘格式”中，为获得最佳性能，请选择“Thick Provision Eager Zeroed”。
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”中，若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”  。

   > [!TIP]
   > 若要添加其他 NIC，请清除“部署后打开” > “完成”。 默认情况下，模板包含单个 NIC。 可以在部署后添加其他 NIC。

### <a name="start-appliance-setup"></a>启动设备设置

1. 在 VMware vSphere 客户端控制台中打开 VM。 VM 将启动并进入 Windows Server 2016 安装体验。
2. 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份使用管理员密码登录到 VM。 首次登录时，会在数秒内启动复制应用程序设置工具（Azure Site Recovery 配置工具）。
5. 输入一个名称，用于向服务器迁移注册设备。 然后单击“下一步”。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅  。
7. 等待该工具完成注册 Azure AD 应用以标识设备。 设备将重新启动。
1. 再次登录到计算机。 在数秒内，配置服务器管理向导会自动启动。

### <a name="register-the-replication-appliance"></a>注册复制设备

完成复制设备的设置和注册。

1. 在“设备设置”中，选择“设置连接”。
2. 选择复制设备用来发现 VM 的 NIC（默认只有一个 NIC），并在源计算机上执行移动服务的推送安装。
3. 选择复制设备用来与 Azure 建立连接的 NIC。 再选择“保存”。 配置后无法更改此设置。
4. 如果设备位于代理服务器后面，则你需要指定代理设置。
    - 将代理名称指定为 **http://ip-address** 或 **http://FQDN** 。 不支持 HTTPS 代理服务器。
5. 当系统提示你输入订阅、资源组和保管库详细信息时，请添加下载设备模板时记下的详细信息。
6. 在“安装第三方软件”中，接受许可协议。 选择“下载并安装”，安装 MySQL 服务器。
7. 选择“安装 VMware PowerCLI”。 执行此操作之前，请确保所有浏览器窗口已关闭。 然后选择“继续”。 
8. 在“验证设备配置”中验证先决条件，然后继续。
9. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
10. 输入[创建](#prepare-an-account-to-discover-vms)的用于发现 VMware 的帐户的凭据。 选择“添加” > “继续” 。
11. 在“配置虚拟机凭据”中，输入在为 VM 启用复制时，为了推送安装移动服务而[创建](#prepare-an-account-for-mobility-service-installation)的凭据。  
    - 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。
    - 对于 Linux，请提供根帐户的详细信息。
12. 选择“完成配置”以完成注册  。


注册复制设备后，Azure Migrate 服务器评估将使用指定的设置连接到 VMware 服务器，并发现 VM。 可以在“其他”选项卡上的“管理” > “已发现的项”中查看已发现的 VM。



## <a name="replicate-vms"></a>复制 VM

选择要迁移的 VM。

> [!NOTE]
> 在门户中，一次最多可以选择 10 台计算机进行复制。 如果需要复制更多计算机，请将它们分组为 10 个为一批。

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate: 服务器迁移”，然后单击“复制”。

    ![Azure Migrate 中“服务器”屏幕的屏幕截图。 在“Azure Migrate:服务器迁移”的“迁移工具”下选择了“复制”按钮。](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化”中，选择“是，使用 VMware vSphere”。   
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称。
4. 在“vCenter 服务器”中，指定用于管理 VM 的 vCenter 服务器的名称，或者 VM 所在的 vSphere 服务器的名称。
5. 在“进程服务器”中，选择复制设备的名称。
6. 在“来宾凭据”中，指定用于推送安装移动服务的 VM 管理员帐户。 然后单击“下一页:虚拟机”。

    ![“复制”屏幕中“源设置”选项卡的屏幕截图。 “来宾凭据”字段突出显示，值设置为“VM-admin-account”。](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. 在“虚拟机”中，选择要复制的计算机。

    - 如果已针对 VM 运行评估，则可以应用评估结果中提供的 VM 大小和磁盘类型（高级/标准）建议。 为此，请在“从 Azure Migrate 评估导入迁移设置?”中选择“是”选项。 
    - 如果你未运行评估，或者不想要使用评估设置，请选择“否”选项。
    - 如果你已选择使用评估，请选择 VM 组和评估名称。
8. 在“可用性选项”中，选择：
    -  可用性区域，将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 如果选择此选项，则需要在“计算”选项卡中指定用于每个选定计算机的可用性区域。仅当为迁移选择的目标区域支持可用性区域时，此选项才可用
    -  可用性集，将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。
    - 如果不需要为迁移的计算机使用其中任何可用性配置，则选择“无需基础结构冗余”选项。
9. 检查要迁移的每个 VM。 然后单击“下一页:目标设置”。
10. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。
11. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。  
12. 在“缓存存储帐户”中，保留默认选项，以使用为项目自动创建的缓存存储帐户。 如果要指定其他存储帐户用作复制的缓存存储帐户，请使用下拉列表。 <br/>
    > [!NOTE]
    >
    > - 如果选择了专用终结点作为 Azure Migrate 项目的连接方法，请向恢复服务保管库授予对缓存存储帐户的访问权限。 [**了解详细信息**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - 若要使用结合专用对等互连的 ExpressRoute 进行复制，请为缓存存储帐户创建专用终结点。 [**了解详细信息**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional)
13. 在“可用性选项”中，选择：
    -  可用性区域，将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 如果选择此选项，则需要在“计算”选项卡中指定用于每个选定计算机的可用性区域。仅当为迁移选择的目标区域支持可用性区域时，此选项才可用
    -  可用性集，将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。
    - 如果不需要为迁移的计算机使用其中任何可用性配置，则选择“无需基础结构冗余”选项。
14. 在“磁盘加密类型”中，选择以下类型：
    - 使用平台管理的密钥进行静态加密
    - 使用客户管理的密钥进行静态加密
    - 通过平台管理的密钥和客户管理的密钥进行双重加密

   > [!NOTE]
   > 若要使用 CMK 复制 VM，需要在目标资源组下[创建磁盘加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)。 磁盘加密集对象将托管磁盘映射到包含 CMK 的密钥保管库，以用于 SSE。

15. 在“Azure 混合权益”中：

    - 如果你不想要应用 Azure 混合权益，请选择“否”。 然后单击“下一步”。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。 然后单击“下一步”。

    ![目标设置](./media/tutorial-migrate-vmware/target-settings.png)

16. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性配置（如果在上一步中选定）。 VM 必须符合 [Azure 要求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。

   - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表会显示建议大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。
    - **可用性区域**：指定要使用的可用性区域。
    - **可用性集**：指定要使用的可用性集。

17. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。 然后单击“下一步”。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。

18. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。 

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。 开始复制后无法更改设置。


## <a name="track-and-monitor"></a>跟踪和监视

1. 在门户通知中跟踪作业状态。

    ![跟踪作业](./media/tutorial-migrate-vmware-agent/jobs.png)

2. 可以在“Azure Migrate:服务器迁移”中单击“复制服务器” **：** 来监视复制状态。

    ![监视复制](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

复制按如下方式进行：
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。


## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器迁移”中，单击“测试已迁移的服务器”。

     ![测试已迁移的服务器](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。

    ![测试迁移](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。 建议使用非生产 VNet。
4. “测试迁移”作业随即启动。 在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。 计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。

    ![清理迁移](./media/tutorial-migrate-vmware-agent/clean-up.png)
    > [!NOTE]
    > 现在可以使用 SQL VM RP 注册运行 SQL Server 的服务器，以利用自动修补、自动备份和使用 SQL IaaS 代理扩展简化的许可证管理。
    >- 选择“管理” > “复制服务器” > “包含 SQL Server 的计算机” > “计算和网络”，然后选择“是”，注册 SQL VM RP。
    >- 如果你的 SQL Server 实例享有有效软件保障或 SQL Server 订阅的权益，并且你需要将此权益应用到所要迁移的计算机，请选择“面向 SQL Server 的 Azure 混合权益”。

## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate: 服务器迁移”，然后单击“复制服务器”。

    ![复制服务器](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。   
    - 默认情况下，Azure Migrate 会关闭本地 VM，以确保最大限度地减少数据丢失。
    - 如果你不想要关闭 VM，请选择“否”
4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。 这样会执行以下操作：
    - 停止本地计算机的复制。
    - 从 Azure Migrate 的“复制服务器”计数中删除该计算机：服务器迁移。
    - 清除 VM 的复制状态信息。
1. 验证并[排查 Azure VM 上的所有 Windows 激活问题。](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
1. 执行任何迁移后的应用调整，例如主机名、更新数据库连接字符串和 Web 服务器配置。
1. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
1. 将流量交接到已迁移的 Azure VM 实例。
1. 从本地 VM 清单中删除本地 VM。
1. 从本地备份中删除本地 VM。
1. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 本地
    - 将应用流量转移到在已迁移的 Azure VM 实例上运行的应用。
    - 从本地 VM 清单中删除本地 VM。
    - 从本地备份中删除本地 VM。
    - 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。
- 迁移后调整 Azure VM 设置：
    - [Azure VM 代理](../virtual-machines/extensions/agent-windows.md)可管理 VM 与 Azure 结构控制器之间的交互。 某些 Azure 服务（例如 Azure 备份、Site Recovery 和 Azure 安全）需要 Azure VM 代理。 使用基于代理的迁移进行 VMare VM 迁移时，移动服务安装程序会在 Windows 计算机上安装 Azure VM 代理。 在 Linux VM 上，我们建议在迁移后安装代理。
    - 迁移后从 Azure VM 中手动卸载移动服务。
    - 迁移后手动卸载 VMware 工具。
- 在 Azure 中：
    - 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
    - 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
- 业务连续性/灾难恢复
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
