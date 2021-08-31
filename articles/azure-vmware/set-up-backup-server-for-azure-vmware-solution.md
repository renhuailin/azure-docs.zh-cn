---
title: 为 Azure VMware 解决方案设置 Azure 备份服务器
description: 使用 Azure 备份服务器设置 Azure VMware 解决方案环境以备份虚拟机。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: fc82e10093402ffd4a026b0c9bef0d8e5a221647
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432133"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>为 Azure VMware 解决方案设置 Azure 备份服务器

Azure 备份服务器有助于实现业务连续性和灾难恢复 (BCDR) 策略。 有了 Azure VMware 解决方案后，只能用 Azure 备份服务器配置虚拟机 (VM) 级的备份。 

Azure 备份服务器可以将备份数据存储到：

- “磁盘”：针对短期存储，Azure 备份服务器会将数据备份到磁盘池。
- “Azure”：针对短期和长期外部存储，会使用 Azure 备份将存储在磁盘池中的 Azure 备份服务器数据备份到 Microsoft Azure 云。

使用 Azure 备份服务器将数据还原到源或备用位置。 这样，如果原始数据由于计划或意外问题不可用，则可以轻松将数据还原到备用位置。

本文将帮助你准备 Azure VMware 解决方案环境，以便使用 Azure 备份服务器来备份 VM。 我们将引导你完成以下步骤： 

> [!div class="checklist"]
> * 确定要使用的建议 VM 磁盘类型和大小。
> * 创建存储恢复点的恢复服务保管库。
> * 设置恢复服务保管库的存储复制。
> * 将存储添加到 Azure 备份服务器。

## <a name="supported-vmware-features"></a>支持的 VMware 功能

- “无代理备份”：Azure 备份服务器不需要在 vCenter 或 ESXi 服务器上安装代理即可备份虚拟机。 而是会提供 IP 地址或完全限定的域名 (FQDN)，以及登录凭据，用于在 Azure 备份服务器中对 VMware 服务器进行身份验证。
- “云集成的备份”：Azure 备份服务器保护到磁盘和云的工作负荷。 Azure 备份服务器的备份和恢复工作流有助于管理长期保留和异地备份。
- “检测并保护 vCenter 管理的 VM”：Azure 备份服务器检测并保护 vCenter 或 ESXi 服务器上部署的 VM。 Azure 备份服务器还会检测 vCenter 管理的 VM，以便可以保护大型部署。
- “文件夹级自动保护”：vCenter 允许组织 VM 文件夹中的 VM。 Azure 备份服务器检测这些文件夹。 可以使用 Azure 备份服务器来保护文件夹级别，包括所有子文件夹的 VM。 保护文件夹时，Azure 备份服务器保护该文件夹中的 VM及后续添加的 VM。 Azure 备份服务器每天检测新 VM，并自动进行保护。 在递归文件夹中整理 VM 时，Azure 备份服务器会自动检测并保护递归文件夹中部署的新 VM。
- “Azure 备份服务器继续保护群集中的 Vmotion VM”： 当 VM 在群集内被 VMotion 以进行负载均衡时，Azure 备份服务器会自动检测并继续提供 VM 保护。
- “更快恢复所需的文件”： Azure 备份服务器可以从 Windows VM 恢复文件或文件夹，而无需恢复整个 VM。

## <a name="limitations"></a>限制

- 必须安装 Azure 备份服务器 v3 的更新汇总 1。
- 无法在第一次 Azure 备份服务器备份前备份用户快照。 Azure 备份服务器完成首次备份后，可以备份用户快照。
- Azure 备份服务器无法通过直通磁盘和物理原始设备映射 (pRDMs) 保护 VMware VM。
- Azure 备份服务器无法检测或保护 VMware vApps。

若要设置 Azure VMware 解决方案的 Azure 备份服务器，必须完成以下步骤：

- 设置先决条件和环境。
- 创建恢复服务保管库。
- 下载并安装 Azure 备份服务器。
- 将存储添加到 Azure 备份服务器。

### <a name="deployment-architecture"></a>部署体系结构

Azure 备份服务器部署为 Azure 基础结构即服务 (IaaS) VM 来保护 Azure VMware 解决方案 VM。

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="图中显示了 Azure 备份服务器部署为 Azure 基础结构即服务 (IaaS) VM 来保护 Azure VMware 解决方案 VM。" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure 备份服务器环境的先决条件

在 Azure 环境中安装 Azure 备份服务器时，请考虑本部分中的建议。

### <a name="azure-virtual-network"></a>Azure 虚拟网络

请确保[在 Azure 中为 VMware 私有云配置网络](tutorial-configure-networking.md)。

### <a name="determine-the-size-of-the-vm"></a>确定 VM 的大小

按照[在 Azure 门户中创建第一个 Windows VM](../virtual-machines/windows/quick-create-portal.md) 教程中的说明进行操作。  你将在上一步创建的虚拟网络中创建 VM。 从 Windows Server 2019 Datacenter 的库映像开始运行 Azure 备份服务器。 

下表概述了每个 Azure 备份服务器 VM 大小的受保护工作负荷的最大数量。 该信息基于内部性能以及使用工作负荷大小和改动的规范值进行的规模测试。 实际工作负荷大小可以更大，但应适合连接到 Azure 备份服务器 VM 的磁盘。

| 最大受保护的工作负荷 | 平均工作负荷大小 | 平均工作负荷改动（每天） | 最小存储 IOPs | 建议的磁盘类型/大小      | 建议的 VM 大小 |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | 净改动 5%                   | 2,000             | 标准 HDD（8 TB 或更大）  | A4V2       |
| 40                      | 150 GB                | 净改动 10%                  | 4,500             | 高级 SSD*（每个磁盘大小为 1 TB 或以上） | DS3_V2     |
| 60                      | 200 GB                | 净改动 10%                  | 10,500            | 高级 SSD*（每个磁盘大小为 8 TB 或以上） | DS3_V2     |

*若要获取所需的 IOP，请使用最小建议大小或更大的磁盘。 大小较小的磁盘提供较低的 IOPs。

> [!NOTE]
> Azure 备份服务器设计为在专用的单一用途服务器上运行。 不能在以下计算机上安装 Azure 备份服务器：
> * 作为域控制器运行。
> * 安装了应用程序服务器角色。
> * 是 System Center Operations Manager 管理服务器。
> * 运行 Exchange 服务器。
> * 是群集的节点。

### <a name="disks-and-storage"></a>磁盘和存储

Azure 备份服务器需要磁盘以进行安装。 

| 要求                      | 建议大小  |
|----------------------------------|-------------------------|
| Azure 备份服务器安装                | 安装位置：3 GB<br />数据库文件驱动器：900 MB<br />系统驱动器：1 GB 用于 SQL Server 安装<br /><br />还需要一些空间，以便 Azure 备份服务器在存档时将文件目录复制到临时安装位置。      |
| 用于存储池的磁盘<br />（使用基本卷，不能位于动态磁盘上） | 受保护数据的大小的两到三倍。<br />有关详细的存储计算，请参阅 [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)。   |

若要了解如何将新的托管数据磁盘附加到现有的 Azure VM，请参阅[使用 Azure 门户将托管数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-managed-disk-portal.md)。

> [!NOTE]
> 单个 Azure 备份服务器的存储池的软限制为 120 TB。

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>将备份数据存储在本地磁盘和 Azure 中

在 Azure 中存储备份数据会缩减 Azure 备份服务器 VM 上的备份基础结构。 为进行操作恢复（备份），Azure 备份服务器将备份数据存储在附加到 VM 上的 Azure 磁盘上。 将磁盘和存储空间附加到 VM 后，Azure 备份服务器将为你管理存储。 存储量取决于附加到每个 Azure VM 的磁盘的数量和大小。 Azure VM 的每个大小都有可附加的最大磁盘数。 例如，A2 为四个磁盘，A3 为八个磁盘，A4 为十六个磁盘。 同样，磁盘的大小和数量决定了备份存储池的总容量。

> [!IMPORTANT]
> 不应在 Azure 备份服务器附加的磁盘上保留操作恢复数据超过 5 天。 如果数据超过五天，请将数据存储在恢复服务保管库中。

若要将备份数据存储在 Azure 中，请创建或使用恢复服务保管库。 准备备份 Azure 备份服务器工作负荷时，请[配置恢复服务保管库](#create-a-recovery-services-vault)。 配置后，每次运行联机备份作业时，都会在保管库中创建恢复点。 每个恢复服务保管库最多可容纳 9999 个恢复点。 根据所创建的恢复点数量以及保留期限，可将备份数据保留多年。 例如，可以每月创建恢复点，并将其保留五年。

> [!IMPORTANT]
> 无论是将备份数据发送到 Azure 还是在本地保留，都必须将 Azure 备份服务器注册到恢复服务保管库。

### <a name="scale-deployment"></a>大规模部署

如果想要扩展部署，可以使用以下选项：

- “纵向扩展”：将 Azure 备份服务器 VM 的大小从 A 系列增加到 DS3 系列，并增加本地存储。
- “卸载数据”：将旧数据发送到 Azure，并仅保留附加到 Azure 备份服务器计算机的存储上的最新数据。
- “横向扩展”：添加更多 Azure 备份服务器计算机来保护工作负荷。

### <a name="net-framework"></a>.NET Framework

VM 必须安装 .NET Framework 3.5 SP1 或更高版本。

### <a name="join-a-domain"></a>加入域

Azure 备份服务器 VM 必须加入域。 拥有 VM 管理员特权的域用户必须安装 Azure 备份服务器。

在 Azure VM 中部署的 Azure 备份服务器可以在 Azure VMware 解决方案中备份 VM 上的工作负荷。 工作负荷应在同一个域中，以启用备份操作。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是用于存储在不同时间创建的恢复点的存储实体。 它还包含与受保护项关联的备份策略。

1. 登录到 [Azure 门户](https://portal.azure.com/)，在左侧菜单中，选择“所有服务”。

1. 在“所有服务”对话框中，输入“恢复服务” ，并从列表中选择“恢复服务保管库”。

   此时会显示订阅中的恢复服务保管库列表。

1. 在“恢复服务保管库”仪表板上，选择“添加” 。

   此时会打开“恢复服务保管库”对话框。

1. 输入值，然后选择“创建”。

   - **名称**：输入一个友好名称以标识此保管库。 名称对于 Azure 订阅必须是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。
   - **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
   - **资源组**：使用现有资源组，或创建一个新的资源组。 若要查看订阅中可用的资源组列表，请选择“使用现有资源”，然后从下拉列表中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。
   - **位置**：为保管库选择地理区域。 若要创建保管库来保护 Azure VMware 解决方案虚拟机，保管库必须与 Azure VMware 解决方案私有云位于同一区域。

   创建恢复服务保管库可能需要一段时间。 可在门户右上角“通知”区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。


## <a name="set-storage-replication"></a>设置存储复制

使用存储复制选项可在异地冗余存储（默认项）与本地冗余存储之间进行选择。 异地冗余存储将存储帐户中的数据复制到次要区域，从而使数据能够持久保存。 本地冗余存储是更便宜、但持久性不高的选项。 若要了解异地冗余和本地冗余存储选项的详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

> [!IMPORTANT]
> 在保管库中配置备份之前，必须先更改恢复服务保管库的“存储复制类型本地冗余/异地冗余”的设置。 配置备份后，对其进行修改的选项将被禁用，并且无法更改存储复制类型。

1. 在“恢复服务保管库”中，选择新保管库。 

1. 在“设置”下，选择“属性”   。 在“备份配置”下，选择“更新”。

1. 选择存储复制类型，然后选择“保存”。

## <a name="download-and-install-the-software-package"></a>下载和安装软件包

按照本部分中的步骤下载、提取和安装软件包。

### <a name="download-the-software-package"></a>下载软件包

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 如果已打开恢复服务保管库，请转到下一步。 

   >[!TIP]
   >如果没有打开的恢复服务保管库，并且是在 Azure 门户中，那么，请在资源列表中，进入“恢复服务” > “恢复服务保管库” .

1. 在恢复服务保管库列表中选择一个保管库。

   此时会打开选定的保管库仪表板。

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png" alt-text="显示保管库仪表板的屏幕截图。":::

   默认会打开“设置”选项。 如果该选项已关闭，请选择“设置”来将选项打开。

1. 选择“备份”以打开“入门”向导。 

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png" alt-text="屏幕截图显示了在“开始使用”向导下选择了“备份”选项。":::

1. 在打开的窗口中：

   1. 从“工作负荷的运行位置？”菜单中选择“本地” 。

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="屏幕截图显示了用于工作负载运行位置和备份内容的选项。":::

   1. 从“希望备份什么内容？”菜单中，选择想要使用 Azure 备份服务器保护的工作负荷。

   1. 选择“准备基础结构”，下载并安装 Azure 备份服务器和保管库凭据。

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="屏幕截图显示了用于准备基础结构的步骤。":::

1. 在打开的“准备基础结构”窗口中：

   1. 选择“下载”链接以安装 Azure 备份服务器。

   1. 选择“已下载或正使用最新 Azure 备份服务器安装”，然后选择“下载”以下载保管库凭据。 将 Azure 备份服务器注册到恢复服务保管库时，将使用这些凭据。 使用此链接会转到下载中心，可以从中下载软件包。

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure-2.png" alt-text="屏幕截图显示了为 Azure 备份服务器准备基础结构的步骤。":::

1. 在下载页上，选择所有文件，然后选择“下一步”。

   > [!NOTE]
   > 必须将所有文件下载到同一个文件夹。 由于所有文件的下载大小合计超过了 3G，可能需要 60 分钟才能完成下载。 

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png" alt-text="屏幕截图显示了要下载的 Microsoft Azure 备份文件。":::

### <a name="extract-the-software-package"></a>提取软件包

如果已将软件包下载到其他服务器，请将文件复制到创建的 VM 以部署 Azure 备份服务器。

> [!WARNING]
> 至少需要有 4GB 的可用空间才能解压缩安装程序文件。

1. 下载所有文件后，双击“MicrosoftAzureBackupInstaller.exe”以打开“Microsoft Azure 备份”安装向导"，然后选择“下一步”。

1. 选择文件提取后的目标位置，然后选择“下一步”。

1. 选择“提取”开始提取过程。

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/extract/03.png" alt-text="屏幕截图显示了可以提取的 Microsoft Azure 备份文件。":::

1. 提取完毕后，选择“执行 setup.exe”的选项，然后选择“完成” 。

> [!TIP]
> 还可以从提取软件包的文件夹中找到 setup.exe 文件。

### <a name="install-the-software-package"></a>安装软件包

1. 在“安装”下的安装程序窗口中，选择“Microsoft Azure 备份”以打开安装向导。

1. 在“欢迎”屏幕上，选择“下一步”，继续进入“先决条件检查”页。

1. 若要确定硬件和软件是否满足 Azure 备份服务器的先决条件，请选择“再次检查”。 如果成功满足，则选择“下一步”。

1. Azure 备份服务器安装包与所需的相应 SQL Server 二进制文件捆绑在一起。 在开始全新安装 Azure 备份服务器时，请选择“使用此安装程序安装新的 SQL Server 实例”选项。 然后选择“检查并安装”。

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/sql/01.png" alt-text="屏幕截图显示了“SQL 设置”对话框和安装 SQL Server 的新实例，其中选中了此安装选项。":::

   > [!NOTE]
   > 如果要使用自己的 SQL Server 实例，受支持的 SQL Server 版本包括 SQL Server 2014 SP1 或更高版本、2016 和 2017。 所有 SQL Server 版本都应当是 Standard 或 Enterprise 64 位。 Azure 备份服务器使用的实例必须是本地的，不能是远程的。 如果对 Azure 备份服务器使用现有 SQL Server 实例，则安装程序仅支持使用 SQL Server 的“命名实例”。

   如果发生故障并且系统建议重启计算机，请按建议操作，然后选择“再次检查”。 对于任何 SQL Server 配置问题，请根据 SQL Server 准则重新配置 SQL Server。 然后，使用 SQL Server 的现有实例重试安装或升级 Azure 备份服务器。

   **手动配置**

   使用自己的 SQL Server 实例时，请确保将 builtin\Administrators 添加到 master 数据库的 sysadmin 角色。

   “用 SQL Server 2017 配置 reporting services”

   如果使用 SQL Server 2017 的实例，则必须手动配置 SQL Server 2017 Reporting Services (SSRS)。 配置 SSRS 后，请确保将 SSRS 的“IsInitialized”属性设置为“True”。 如果此属性设置为 True，Azure 备份服务器将假设已配置 SSRS 并跳过 SSRS 配置。

   若要检查 SSRS 配置状态，请运行：

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   对 SSRS 配置使用以下值：

   * “服务帐户”：“使用内置帐户”应为“网络服务”  。
   * “Web 服务 URL”：“虚拟目录”应为“ReportServer_”  **\<SQLInstanceName>** 。
   * “数据库”：“DatabaseName”应为“ReportServer$”  **\<SQLInstanceName>** 。
   * “Web 门户 URL”：“虚拟目录”应为“Reports_”  **\<SQLInstanceName>** 。

   [详细了解](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) SSRS 配置。

   > [!NOTE]
   > [Microsoft Online Services 条款](https://www.microsoft.com/licensing/product-licensing/products) (OST) 管理用作 Azure 备份服务器数据库的 SQL Server 的许可。 根据 OST，只能使用与 Azure 备份服务器捆绑的 SQL Server 作为 Azure 备份服务器的数据库。

1. 安装成功后，选择“下一步”。

1. 提供 Microsoft Azure 备份服务器文件的安装位置，然后选择“下一步”。

   > [!NOTE]
   > 备份到 Azure 需要有暂存位置。 请确保暂存位置的空间至少为要备份到云的数据的 5%。 在磁盘保护方面，安装完成之后需要配置独立的磁盘。 有关存储池的详细信息，请参阅[配置存储池和磁盘存储](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))。

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/space-screen.png" alt-text="显示 SQL Server 设置的屏幕截图。":::

1. 为受限的本地用户帐户提供强密码，然后选择“下一步”。

1. 选择是否要使用 Microsoft 更新来检查更新，然后选择“下一步”。

   > [!NOTE]
   > 我们建议让 Windows 更新重定向到 Microsoft 更新，此网站为 Windows 和 Azure 备份服务器等其他产品提供了安全更新与重要更新。

1. 查看“设置摘要”，然后选择“安装”。 

   安装会分阶段进行。 
   - 第一阶段安装 Microsoft Azure 恢复服务代理。
   - 第二阶段检查是否有 Internet 连接。 如果有，可以继续安装。 如果没有，必需提供连接到 Internet 所需的代理详细信息。 
   - 最后一个阶段检查必备的软件。 如果未安装必备软件，任何缺少的软件都将与 Microsoft Azure 恢复服务代理一起安装。

1. 选择“浏览”来查找保管库凭据，将计算机注册到恢复服务保管库，然后选择“下一步”。

1. 选择通行短语来加密或解密 Azure 与本地之间发送的数据。

   > [!TIP]
   > 可以自动生成通行短语，或提供自己的通行短语（最少包含 16 个字符）。

1. 输入密码的保存位置，然后选择“下一步”以注册服务器。

   > [!IMPORTANT]
   > 将通行短语保存到本地服务器之外的安全位置。 强烈建议使用 Azure Key Vault 来存储密码。

   Microsoft Azure 恢复服务代理安装完成后，安装步骤会转到 SQL Server 和 Azure 备份服务器组件的安装和配置。

1. 安装步骤完成后，选择“关闭”。

### <a name="install-update-rollup-1"></a>安装更新汇总 1

必须先安装 Azure 备份服务器 v3 的更新汇总 1，然后才能保护工作负荷。  可以在[知识库文章](https://support.microsoft.com/en-us/help/4534062/)中找到 bug 修复和安装说明。

## <a name="add-storage-to-azure-backup-server"></a>将存储添加到 Azure 备份服务器

Azure 备份服务器 v3 支持新式备份存储，其：

-  存储节省 50%。
-  备份速度提高两倍。
-  更高效的存储。
-  可感知工作负荷的存储。

### <a name="volumes-in-azure-backup-server"></a>Azure 备份服务器中的卷

添加具有 Azure 备份服务器 VM 所需存储容量的数据磁盘（如果尚未添加）。

Azure 备份服务器 v3 仅接受存储卷。 添加卷时，Azure 备份服务器会将卷格式化为新式备份存储所需的复原文件系统 (ReFS)。

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>将卷添加到 Azure 备份服务器磁盘存储

1. 在“管理”窗格中，重新扫描存储，然后选择“添加”。 

1. 从可用卷中选择要添加到存储池的卷。 

1. 添加可用卷后，可以为它们提供易记名称以帮助对其进行管理。 

1. 请选择“确定”，将这些卷格式化为 ReFS 以便 Azure 备份服务器可以利用新式备份存储的优点。


## <a name="next-steps"></a>后续步骤

本文介绍了如何为 Azure 备份服务器设置 Azure VMware 解决方案，接下来，你可能想要了解：

- [为 Azure VMware 解决方案 VM 配置备份](backup-azure-vmware-solution-virtual-machines.md)。
- [用 Azure 安全中心集成保护 Azure VMware 解决方案 VM](azure-security-integration.md)。