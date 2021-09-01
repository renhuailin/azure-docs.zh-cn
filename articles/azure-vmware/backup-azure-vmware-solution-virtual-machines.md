---
title: 使用 Azure 备份服务器备份 Azure VMware 解决方案 VM
description: 使用 Azure 备份服务器配置 Azure VMware 解决方案环境以备份虚拟机。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: c632acdf6f97a906fec5ba42faad0b56db89c328
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323973"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>使用 Azure 备份服务器备份 Azure VMware 解决方案 VM

本文演示如何使用 Azure 备份服务器备份在 Azure VMware 解决方案上运行的 VMware 虚拟机 (VM)。  首先，请全面了解如何[设置适用于 Azure VMware 解决方案的 Microsoft Azure 备份服务器](set-up-backup-server-for-azure-vmware-solution.md)。

然后，我们会演练所有必需过程，以便：

> [!div class="checklist"] 
> * 设置一个安全通道，使 Azure 备份服务器能够通过 HTTPS 来与 VMware 服务器通信。 
> * 将帐户凭据添加到 Azure 备份服务器。 
> * 将 vCenter 添加到 Azure 备份服务器。 
> * 设置一个包含要备份的 VMware VM 的保护组，指定备份设置，并计划备份。

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>与 vCenter 服务器建立安全连接

默认情况下，Azure 备份服务器通过 HTTPS 来与 VMware 服务器通信。 若要设置 HTTPS 连接，请下载 VMware 证书颁发机构 (CA) 证书，并将其导入到 Azure 备份服务器。

### <a name="set-up-the-certificate"></a>设置证书

1. 在 Azure 备份服务器计算机上的浏览器中，输入 vSphere Web 客户端 URL。

   > [!NOTE] 
   > 如果未显示 VMware 入门页，请验证连接和浏览器代理设置，然后重试。

1. 在 VMware 入门页上，选择“下载受信任的根 CA 证书”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="显示用于远程访问 vSphere 的 vSphere Web 客户端入门窗口的屏幕截图。":::

1. 将 download.zip 文件保存到 Azure 备份服务器计算机，然后将其内容提取到 certs 文件夹，该文件夹中包含以下文件：

   - 扩展名以类似 .0 和 .1 的编号顺序开头的根证书文件。
   - 扩展名以类似 .r0 或 .r1 的序列开头的 CRL 文件。

1. 在 certs 文件夹中，右键单击根证书文件，然后选择“重命名”，以将扩展名更改为 .crt。

   文件图标将更改为表示根证书的图标。

1. 右键单击根证书，并选择“安装证书”。

1. 在“证书导入向导”中，选择“本地计算机”作为证书的目标，然后选择“下一步”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png" alt-text="显示“证书导入向导”对话框的屏幕截图，其中选择了“本地计算机”。":::

   > [!NOTE] 
   > 如果系统询问是否要允许对计算机所做的更改，请确认。

1. 选择“将所有证书放入下列存储”，然后选择“浏览”以选择证书存储。 

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png" alt-text="显示“证书存储”对话框的屏幕截图，其中选择了“将所有的证书都放入下列存储”选项。":::

1. 选择“受信任的根证书颁发机构”作为目标文件夹，并选择“确定”。 

1. 查看设置，然后选择“完成”以开始导入证书。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png" alt-text="显示“证书导入”向导的屏幕截图。":::

1. 确认导入证书后，登录到 vCenter 服务器以确认连接安全。

### <a name="enable-tls-12-on-azure-backup-server"></a>在 Azure 备份服务器上启用 TLS 1.2

VMware 6.7 及更高版本已启用 TLS 作为通信协议。 

1. 复制以下注册表设置，并将其粘贴到记事本中。 然后将文件另存为不带 .txt 扩展名的 TLS.REG。

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. 右键单击 TLS.REG 文件，然后选择“合并”或“打开”以将设置添加到注册表中 。


## <a name="add-the-account-on-azure-backup-server"></a>在 Azure 备份服务器上添加帐户

1. 打开 Azure 备份服务器，然后在 Azure 备份服务器控制台中，选择“管理” > “生产服务器” > “管理 VMware”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png" alt-text="显示 Microsoft Azure 备份控制台的屏幕截图。":::

1. 在“管理证书”对话框中，选择“添加” 。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png" alt-text="显示 Azure 备份服务器中的“管理凭据”的屏幕截图。":::

1. 在“添加凭据”对话框中，输入新凭据的名称和说明。 指定你在 VMware 服务器上定义的用户名和密码。

   > [!NOTE] 
   > 如果 VMware 服务器和 Azure 备份服务器不在同一个域中，请在“用户名”框中指定域。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png" alt-text="显示 Azure 备份服务器中的凭据详细信息的屏幕截图。":::

1. 选择“添加”以添加新凭据。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png" alt-text="显示 Azure 备份服务器“管理凭据”对话框的屏幕截图，其中显示了新凭据。":::

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>将 vCenter 服务器添加到 Azure 备份服务器

1. 在 Azure 备份服务器控制台中，选择“管理” > “生产服务器” > “添加”  。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png" alt-text="显示 Microsoft Azure 备份控制台的屏幕截图，其中选择了“添加”按钮。":::

1. 选择“VMware 服务器”，然后选择“下一步” 。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png" alt-text="显示“生产服务器添加向导”的屏幕截图，其中显示选择了“VMware 服务器”选项。":::

1. 指定 vCenter 的 IP 地址。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png" alt-text="显示“生产服务器添加向导”的屏幕截图，其中显示如何添加 VMware vCenter 或 ESXi 主机服务器及其凭据。":::

1. 在“SSL 端口”框中，输入用来与 vCenter 通信的端口。

   > [!TIP] 
   > 端口 443 是默认端口，但如果 vCenter 在不同的端口上侦听，则可以更改端口。

1. 在“指定凭据”框中，选择在上一部分中创建的凭据。

1. 选择“添加”将 vCenter 添加到服务器列表，然后选择“下一步” 。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png" alt-text="显示“生产服务器添加向导”的屏幕截图，其中显示定义了 VMware 服务器和凭据。":::

1. 在“摘要”页中选择“添加” ，将 vCenter 添加到 Azure 备份服务器。

   新服务器会立即添加。 vCenter 不需要代理。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/tasks-screen.png" alt-text="显示“生产服务器添加向导”的屏幕截图，其中显示已定义 VMware 服务器和凭据的摘要并且选择了“添加”按钮。":::

1. 在“完成”页中检查设置，然后选择“关闭”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/summary-screen.png" alt-text="显示“生产服务器添加向导”的屏幕截图，其中显示已添加 VMware 服务器和凭据的摘要。":::

   你将看到“生产服务器”下列出的 vCenter 服务器，其中：
   - 类型为“VMware 服务器” 
   - 代理状态为“正常” 
   
      如果“代理状态”显示为“未知”，请选择“刷新”。

## <a name="configure-a-protection-group"></a>配置保护组

保护组收集多个 VM，并将相同的数据保留和备份设置应用到组中的所有 VM。

1. 在 Azure 备份服务器控制台中，选择“保护” > “新建”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png" alt-text="显示 Microsoft Azure 备份控制台的屏幕截图，其中选择了“新建”按钮以创建新的保护组。":::

1. 在“新建保护组”向导的欢迎页中，选择“下一步”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/protection-wizard.png" alt-text="显示保护组向导的屏幕截图。":::

1. 在“选择保护组类型”页上选择“服务器”，然后选择“下一步”。   将出现“选择组成员”页面。

1. 在“选择组成员”页上，选择要备份的 VM（或 VM 文件夹），然后选择“下一步”。

   > [!NOTE]
   > 选择某个文件夹或 VM 时，也会选择该文件夹中的子文件夹进行备份。 可以取消选中不想要备份的文件夹或 VM。 如果 VM 或文件夹已备份，则无法选择它，这可确保不会为 VM 创建重复的恢复点。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png" alt-text="显示创建新保护组向导的屏幕截图，其中选择组成员。":::

1. 在“选择数据保护方法”页中，输入保护组的名称和保护设置。 

1. 将短期保护设置为“磁盘”，启用在线保护，然后选择“下一步”。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/name-protection-group.png" alt-text="显示创建新保护组向导的屏幕截图，其中选择数据保护方法。":::

1. 指定要在磁盘中备份数据多长时间。

   - 保持期：保留磁盘恢复点的天数。
   - 快速完整备份：创建磁盘恢复点的频率。 要更改执行短期备份的时间或日期，请选择“修改”。

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="显示用于为基于磁盘的保护指定短期恢复目标的保留期的屏幕截图。":::

1. 在“检查磁盘存储分配”页中，检查为 VM 备份提供的磁盘空间。

   - 建议的磁盘分配基于指定的保留期、工作负荷类型，以及受保护数据的大小。 做出所需的任何更改，然后选择“下一步”。
   - **数据大小：** 保护组中数据的大小。
   - 磁盘空间：为保护组建议的磁盘空间量。 若要修改此设置，选择比每个数据源预计增长量略大的空间。
   - 存储池详细信息：显示存储池的状态，包括总磁盘大小和剩余磁盘大小。

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="显示用于查看为每个数据源分配的目标存储的“查看磁盘存储分配”对话框的屏幕截图。":::

   > [!NOTE]
   > 在某些情况下，报告的数据大小高于实际 VM 大小。 我们注意到该问题，当前正在调查此问题。

1. 在“选择副本创建方法”页中指明如何创建初始备份，然后选择“下一步”。

   - 默认设置为“自动通过网络”和“立即”。  若使用默认设置，请指定非高峰时间。 如果选择“稍后”，请指定日期和时间。
   - 如果数据量很大或者网络状态欠佳，请考虑使用可移动介质脱机复制数据。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/name-protection-group.png" alt-text="显示创建新保护组向导的屏幕截图，其中选择副本创建方法。":::

1. 在“一致性检查选项”中，选择如何以及何时自动执行一致性检查，然后选择“下一步”。 

   - 当副本数据变得不一致时，可以运行一致性检查；也可以根据设置的计划运行该检查。
   - 如果不想配置自动一致性检查，可以运行手动检查，方法是：右键单击保护组“执行一致性检查”。

1. 在“指定在线保护数据”页中，选择要备份的 VM 或 VM 文件夹，然后选择“下一步”。  

   > [!TIP]
   > 可以选择单个成员，或者选择“全选”选择所有成员。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png" alt-text="显示创建新保护组向导的屏幕截图，其中指定希望 DPM 帮助在线保护的数据。":::

1. 在“指定在线备份计划”页中，指定将数据从本地存储备份到 Azure 的频率。 

   - 将根据计划生成数据的云恢复点。 
   - 生成恢复点后，该恢复点将传输到 Azure 中的恢复服务保管库。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png" alt-text="显示创建新保护组向导的屏幕截图，其中指定 DPN 用于生成保护计划的在线备份计划。":::

1. 在“指定在线保留策略”页中，指明要在 Azure 中将通过备份创建的恢复点保留多长时间。

   - 在 Azure 中保留数据的时间长短没有限制。
   - 唯一的限制是每个受保护实例的恢复点不可超过 9999 个。 在本示例中，受保护的实例是 VMware 服务器。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/retention-policy.png" alt-text="显示创建新保护组向导的屏幕截图，其中指定在线保留策略。":::

1. 在“摘要”页中检查设置，然后选择“创建组” 。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png" alt-text="显示创建新保护组向导的屏幕截图，其中包含摘要。":::

## <a name="monitor-with-the-azure-backup-server-console"></a>使用 Azure 备份服务器控制台进行监视

配置保护组以备份 Azure VMware 解决方案 VM 后，可以使用 Azure 备份服务器控制台监视备份作业状态和警报。 以下是可以监视的项目。

- 在“监视”任务区域：
   - 在“警报”下，可以监视错误、警告和一般信息。  可以查看活动和非活动警报，并设置电子邮件通知。
   - 在“作业”下，可以查看 Azure 备份服务器为特定受保护数据源或保护组启动的作业。 可以跟踪作业进度或检查作业使用的资源。
- 在“保护”任务区域中，可以检查保护组中卷和共享的状态。 还可以检查恢复设置、磁盘分配和备份计划等配置设置。
- 在“管理”任务区域中，可以查看“磁盘”、“在线”和“代理”选项卡，以检查存储池中磁盘的状态、Azure 注册情况和部署的 DPM 代理状态。  

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="显示 Azure 备份服务器中的备份作业的屏幕截图。":::

## <a name="restore-vmware-virtual-machines"></a>还原 VMware 虚拟机

在 Azure 备份服务器管理员控制台中，有两种方法可以查找可恢复数据。 你可以搜索或浏览。 恢复数据时，你不一定想要将数据或 VM 还原到相同的位置。 出于此原因，Azure 备份服务器支持 VMware VM 备份的三个恢复选项：

- 原始位置恢复 (OLR)：使用 OLR 可将受保护的 VM 还原到其原始位置。 仅当自备份以来未添加或删除过磁盘时，才可以将 VM 还原到其原始位置。 如果曾添加或删除过磁盘，则必须使用备用位置恢复。
- 备用位置恢复 (ALR)：原始 VM 缺失或者不想要扰乱原始 VM 时使用该选项。 提供 ESXi 主机、资源池、文件夹以及存储数据存储和路径的位置。 为了帮助区分已还原的 VM 与原始 VM，Azure 备份服务器会将“-Recovered”追加到 VM 名称中。
- 单个文件位置恢复 (ILR)：如果受保护的 VM 是 Windows Server VM，则可以使用 Azure 备份服务器的 ILR 功能恢复 VM 中的单个文件或文件夹。 若要恢复单个文件，请参阅本文稍后所述的过程。 从 VM 还原单个文件仅适用于 Windows VM 和磁盘恢复点。

### <a name="restore-a-recovery-point"></a>还原恢复点

1. 在 Azure 备份服务器管理员控制台中，选择“恢复”视图。 

1. 使用“浏览”窗格，浏览或筛选以查找要恢复的 VM。 选择 VM 或文件夹后，“以下对象的恢复点”窗格将显示可用的恢复点。

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/recovery-points.png" alt-text="显示 VMware 服务器的可用恢复点的屏幕截图。":::

1. 在“以下对象的恢复点”窗格中，选择创建恢复点的日期。 例如，以粗体显示的日历日期具有可用恢复点。 或者，可以右键单击 VM 并选择“显示所有恢复点”，然后从列表中选择恢复点。

   > [!NOTE] 
   > 对于短期保护，选择基于磁盘的恢复点以实现更快的恢复。 短期恢复点过期后，只能看到用于恢复的“在线”恢复点。

1. 从在线恢复点恢复之前，请确保暂存位置包含足够的可用空间，以容纳要恢复的 VM 的完整未压缩大小。 可以通过运行“配置订阅设置向导”来查看或更改暂存位置。

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="显示恢复文件夹位置的屏幕截图。":::

1. 选择“恢复”以打开“恢复向导” 。

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png" alt-text="显示恢复向导审阅对话框的屏幕截图。":::

1. 选择“下一步”，转到“指定恢复选项”屏幕 。 再次选择“下一步”，转到“选择恢复类型”屏幕 。 

   > [!NOTE]
   > VMware 工作负载不支持启用网络带宽限制。

1. 在“选择恢复类型”屏幕上，恢复到原始实例或新位置。

   - 如果选择“恢复到原始实例”，则无需在向导中做出任何更多选择。 会使用原始实例的数据。
   - 如果选择“在任何主机上作为虚拟机恢复”，则在“指定目标”屏幕上，为“ESXi 主机”、“资源池”、“文件夹”和“路径”提供相关信息。

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/recovery-type.png" alt-text="显示恢复向导的屏幕截图，其中选择恢复类型。":::

1. 在“摘要”页上检查设置，然后选择“恢复”来启动恢复过程 。 

   “恢复状态”屏幕显示恢复操作的进度。

### <a name="restore-an-individual-file-from-a-vm"></a>从 VM 中还原单个文件

可以从受保护的 VM 恢复点还原单个文件。 此功能仅适用于 Windows Server VM。 还原单个文件与恢复整个 VM 类似，除了在启动恢复过程之前需要浏览到 VMDK 并找到所需的文件。 

> [!NOTE]
> 从 VM 还原单个文件仅适用于 Windows VM 和磁盘恢复点。

1. 在 Azure 备份服务器管理员控制台中，选择“恢复”视图。

1. 使用“浏览”窗格，浏览或筛选以查找要恢复的 VM。 选择 VM 或文件夹后，“以下对象的恢复点”窗格将显示可用的恢复点。

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png" alt-text="显示 VMware 服务器的恢复点的屏幕截图。":::

1. 在“以下对象的恢复点”窗格中，使用日历选择所需恢复点的日期。 根据备份策略的配置方式，日期可以包含多个恢复点。 

1. 选择恢复点的创建日期后，请确保选择正确的“恢复时间”。 

   > [!NOTE]
   > 如果所选日期具有多个恢复点，则在“恢复时间”下拉菜单中选择适当的恢复点。 

   选择恢复点后，可恢复项目列表会出现在“路径”窗格。

1. 若要查找想要恢复的文件，请在“路径”窗格中，双击“可恢复项目”列中的项目将其打开 。 然后，选择想要恢复的文件或文件夹。 若要选择多个项目，请在选择每个项目时按 Ctrl 键。 使用“路径”窗格搜索出现在“可恢复项目”列中的文件或文件夹列表 。
    
   > [!NOTE]
   > 使用“搜索以下列表”不会在子文件夹中搜索。 若要搜索子文件夹，请双击文件夹。 使用“向上”按钮将子文件夹移动到父文件夹。 你可以选择多个项目（文件和文件夹），但它们必须在同一个父文件夹中。 不能在同一个恢复作业中恢复多个文件夹中的项。

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png" alt-text="显示所选可用恢复点的日期和时间的屏幕截图。":::

1. 选择要恢复的项目后，在管理员控制台工具功能区中，选择“恢复”打开“恢复向导” 。 在“恢复向导”中，“复查恢复选择”屏幕显示要恢复的所选项目 。

1. 在“指定恢复选项”屏幕上，执行以下步骤之一：

   - 选择“修改”启用网络带宽限制。 在“限制”对话框中，选择“启用网络带宽使用限制”以启用网络带宽限制 。 启用后，配置“设置”和“工作计划”。
   - 选择“下一步”使网络带宽限制处于禁用状态。

1. 在“选择恢复类型”屏幕上，选择“下一步” 。 只能将文件或文件夹恢复到网络文件夹。

1. 在“指定目标”屏幕上，选择“浏览”，找到用于保存文件或文件夹的网络位置 。 Azure 备份服务器创建一个文件夹，在其中复制所有恢复的项目。 该文件夹名称的前缀为 MABS_day-month-year。 为恢复的文件或文件夹选择位置时，将提供该位置的详细信息。

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/specify-destination.png" alt-text="显示所选可用恢复点的日期和时间、目标和目标路径的屏幕截图。":::

1. 在“指定恢复选项”屏幕上，选择要应用的安全设置。 你可以选择修改网络带宽使用限制，但默认情况下限制处于禁用状态。 “SAN 恢复”和“通知”也未启用 。

1. 在“摘要”屏幕上检查设置，然后选择“恢复”来启动恢复过程 。 “恢复状态”屏幕显示恢复操作的进度。

## <a name="next-steps"></a>后续步骤

了解了如何使用 Azure 备份服务器备份 Azure VMware 解决方案 VM 后，你可能还对以下内容感兴趣： 

- [在 Azure 备份服务器中设置备份时的故障排除](../backup/backup-azure-mabs-troubleshoot.md)。
- [Azure VMware 解决方案 VM 的生命周期管理](./integrate-azure-native-services.md)。
