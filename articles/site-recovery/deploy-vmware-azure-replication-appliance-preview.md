---
title: 部署 Azure Site Recovery 复制设备（预览版）
description: 本文介绍了为了使用 Azure Site Recovery 执行 VMware 到 Azure 的灾难恢复而部署复制设备时的支持和要求
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 940cfb52985e956a283e8278c572569e4f350f55
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084182"
---
# <a name="deploy-azure-site-recovery-replication-appliance---preview"></a>部署 Azure Site Recovery 复制设备（预览版）

>[!NOTE]
> 本文中的信息适用于 Azure Site Recovery（预览版）。 有关经典版本中的配置服务器要求的信息，[请参阅本文](vmware-azure-configuration-server-requirements.md)。

>[!NOTE]
> 请确保创建新的恢复服务保管库以设置预览版设备。 请勿使用现有保管库。

>[!NOTE]
> 此预览版不支持为物理计算机启用复制。 

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 到 Azure 的灾难恢复时，需要部署本地复制设备。

- 复制设备会协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。
- [了解](vmware-azure-architecture-preview.md)有关 Azure Site Recovery 复制设备组件和过程的详细信息。

## <a name="hardware-requirements"></a>硬件要求

**组件** | **要求**
--- | ---
CPU 核心数 | 8
RAM | 32 GB
磁盘数目 | 3，其中包括操作系统磁盘 (80 GB)、数据磁盘 1 (620 GB) 和数据磁盘 2 (620 GB)

## <a name="software-requirements"></a>软件要求

**组件** | **要求**
--- | ---
操作系统 | Windows Server 2016
操作系统区域设置 | 英语 (en-*)
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - 启用 [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 设置
FIPS（联邦信息处理标准） | 不要启用 FIPS 模式|

## <a name="network-requirements"></a>网络要求

|**组件** | **要求**|
|--- | ---|
|完全限定的域名 (FQDN) | 静态|
|端口 | 443（控制通道协调）<br>9443（数据传输）|
|NIC 类型 | VMXNET3（如果设备是 VMware VM）|


### <a name="allow-urls"></a>支持的 URL

确保支持以下 URL，并可从 Azure Site Recovery 复制设备加以访问，以进行持续连接：

  | **URL**                  | **详细信息**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | 导航到 Azure 门户。              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | 若要登录到 Azure 订阅，请执行以下操作：  |
  |`*.microsoftonline.com `|为设备创建 Azure Active Directory (AD) 应用，以便与 Azure Site Recovery 通信。 |
  |management.azure.com |为设备创建 Azure AD 应用，以便与 Azure Site Recovery 服务通信。 |
  |`*.services.visualstudio.com `|上传用于内部监视的应用日志。 |
  |`*.vault.azure.net `|在 Azure Key Vault 中管理机密。 注意：请确保要复制的计算机有权访问它。 |
  |aka.ms |允许访问也称为链接。 适用于 Azure Site Recovery 设备更新。 |
  |download.microsoft.com/download |允许从 Microsoft 下载中进行下载。 |
  |`*.servicebus.windows.net `|设备与 Azure Site Recovery 服务之间的通信。 |
  |`*.discoverysrv.windowsazure.com `<br><br>`*.hypervrecoverymanager.windowsazure.com `<br><br> `*.backup.windowsazure.com ` |连接到 Azure Site Recovery 微服务 URL。
  |`*.blob.core.windows.net `|将数据上传到用于创建目标磁盘的 Azure 存储。 |


> [!NOTE]
> 预览版本不支持私有链接。

## <a name="folder-exclusions-from-antivirus-program"></a>防病毒程序中的文件夹排除

### <a name="if-antivirus-software-is-active-on-appliance"></a>如果防病毒软件在设备上处于活动状态

为了平稳进行复制并避免出现连接问题，请从防病毒软件中排除以下文件夹。

C:\ProgramData\Microsoft Azure <br>
C:\ProgramData\ASRLogs <br>
C:\Windows\Temp\MicrosoftAzure C:\Program Files\Microsoft Azure Appliance Auto Update <br>
C:\Program Files\Microsoft Azure Appliance Configuration Manager <br>
C:\Program Files\Microsoft Azure Push Install Agent <br>
C:\Program Files\Microsoft Azure RCM Proxy Agent <br>
C:\Program Files\Microsoft Azure Recovery Services Agent <br>
C:\Program Files\Microsoft Azure Server Discovery Service <br>
C:\Program Files\Microsoft Azure Site Recovery Process Server <br>
C:\Program Files\Microsoft Azure Site Recovery Provider <br>
C:\Program Files\Microsoft Azure to On-Premise Reprotect agent <br>
C:\Program Files\Microsoft Azure VMware Discovery Service <br>
C:\Program Files\Microsoft On-Premise to Azure Replication agent <br>
E:\ <br>

### <a name="if-antivirus-software-is-active-on-source-machine"></a>如果防病毒软件在源计算机上处于活动状态

如果源计算机具有处于活动状态的防病毒软件，则应排除安装文件夹。 因此，为了平稳进行复制，请排除文件夹 C:\ProgramData\ASR\agent。

## <a name="prepare-azure-account"></a>准备 Azure 帐户

若要创建并注册 Azure Site Recovery 复制设备，需要具有以下权限的帐户：

- Azure 订阅的参与者或所有者权限。
- 用于注册 Azure Active Directory (AAD) 应用的权限。
- 用于创建密钥保管库的所有者或参与者以及 Azure 订阅上的用户访问管理员权限，可在向 Azure 注册 Azure Site Recovery 复制设备时使用。

如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。 如果你不是订阅所有者，请与所有者合作，获取所需权限。

## <a name="prerequisites"></a>先决条件

必需的密钥保管库权限如下：

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

请按照以下步骤分配所需权限：

1. 在 Azure 门户中，搜索“订阅”，然后在“服务”下选择“订阅”搜索框，搜索 Azure 订阅  。

2. 在“订阅”页上，选择要在其中创建恢复服务保管库的订阅。

3. 在所选“订阅”中，选择“访问控制 (IAM)”>“检查访问权限”。  在“检查访问权限”中，搜索相关的用户帐户。

4. 在“添加角色分配”中，选择“添加”，再选择“参与者”或“所有者”角色，然后选择帐户 。 然后选择“保存”。

  若要注册设备，你的 Azure 帐户需要具有注册 AAD 应用的权限。

  按照以下步骤分配所需权限：

  - 在 Azure 门户中，导航到“Azure Active Directory” > “用户” > “用户设置”  。 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

  - 如果“应用注册”设置为“否”，则须请求租户/全局管理员分配所需权限。 或者，租户/全局管理员可将“应用程序开发人员”角色分配给帐户，以允许注册 AAD 应用。


## <a name="prepare-infrastructure"></a>准备基础结构

需要在本地环境中设置 Azure Site Recovery 复制设备，以便在本地计算机上启用恢复。 有关设备执行的操作详情，[请参阅此部分](vmware-azure-architecture-preview.md)

转到“恢复服务保管库” > “入门指南”。  在到 Azure 的 VMware 计算机中，选择“准备基础结构”，然后继续执行下面详述的部分：

![恢复服务保管库（预览版）](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

若要设置新设备，可以使用 OVF 模板（推荐）或 PowerShell。 确保满足所有[硬件](#hardware-requirements)和[软件要求](#software-requirements)，以及任何其他先决条件。

## <a name="create-azure-site-recovery-replication-appliance"></a>创建 Azure Site Recovery 复制设备

你可以使用 OVF 模板或通过 PowerShell 创建 Site Recovery 复制设备。

>[!NOTE]
> 需要按顺序执行设备设置。 无法执行多个设备的并行注册。


### <a name="create-replication-appliance-through-ovf-template"></a>通过 OVF 模板创建复制设备

建议采用此方法，因为 Azure Site Recovery 可以确保模板处理所有必要配置。
OVF 模板启动具有所需规范的虚拟机。

![准备基础结构以创建设备](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**执行以下步骤：**

1. 下载 OVF 模板，在本地环境中设置设备。
2. 部署完成后，打开设备 VM 以接收 Microsoft 评估许可证。
3. 在下一画面中，为管理员用户提供密码。
4. 选择“完成”，系统将重新启动，你可以使用管理员用户帐户登录。

### <a name="set-up-the-appliance-through-powershell"></a>通过 PowerShell 设置设备

>[!NOTE]
> 此预览版不支持为物理计算机启用复制。 

如果存在任何组织限制，可以通过 PowerShell 手动设置 Site Recovery 复制设备。 执行以下步骤：

1. 从此处下载[安装程序](https://aka.ms/V2ARcmApplianceCreationPowershellZip)，将此文件夹放到 Azure Site Recovery 复制设备中。
2. 成功复制 zip 文件夹后，解压缩文件夹并提取其中的组件。
3. 转到文件夹提取到的路径，以管理员角色执行以下 PowerShell 脚本：

    DRInstaller.ps1  

## <a name="register-appliance"></a>注册设备
  创建设备后，系统会自动启动 Microsoft Azure 设备配置管理器。 系统将验证以下先决条件，例如 Internet 连接、时间同步、系统配置和组策略。

  - CheckRegistryAccessPolicy - 阻止访问注册表编辑工具。
      - 密钥路径：HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - DisableRegistryTools 值不应等于 0。

  - CheckCommandPromptPolicy - 阻止访问命令提示符。

      - 密钥路径：HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - DisableCMD 值不应等于 0。

  - CheckTrustLogicAttachmentsPolicy - 文件附件的信任逻辑。

      - 密钥路径：HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - UseTrustedHandlers 值不应等于 3。

  - CheckPowershellExecutionPolicy - 启用脚本执行。

      - PowerShell 执行策略不应为 AllSigned 或 Restricted
      - 确保未将组策略“启用脚本执行附件管理器”设置为“已禁用”或“仅允许已签名的脚本”


  使用以下步骤注册设备：

1. 通过切换“使用代理连接到 Internet”选项来配置代理设置。

    所有 Azure Site Recovery 服务都将使用这些设置连接到 Internet。 仅支持 HTTP 代理。

2. 确保支持以下[所需 URL](#allow-urls)，并可从 Azure Site Recovery 复制设备加以访问，以进行持续连接。

3. 检查先决条件后，系统将在下一步中提取有关所有设备组件的信息。 查看所有组件的状态，然后单击“继续”。 保存详细信息后，继续选择设备连接。

4. 保存连接详情后，选择“继续”以继续使用 Microsoft Azure 注册。

5. 确保满足[先决条件](#prerequisites)，然后继续注册。

    ![注册设备](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - 设备的易记名称：在恢复服务保管库基础结构下，为设备提供易记名称，用以在 Azure 门户中进行跟踪。

  - Azure Site Recovery 复制设备密钥：导航到“恢复服务保管库” > “入门指南” > “VMware 到 Azure 准备基础架构”，复制门户中的密钥。   

  - 粘贴密钥后，选择“登录”。
    系统会将你重定向到新的身份验证选项卡。

      默认情况下，系统会在如下突出显示的身份验证管理器页中生成身份验证代码。 在“身份验证”选项卡中使用此代码。

  - 输入 Microsoft Azure 凭据以完成注册。

      成功注册后，即可关闭选项卡并移动到配置管理器以继续设置。

      ![验证码](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > 验证码会在生成后的 5 分钟内过期。 如果处于非活动状态的时间超过此持续时间，系统会提示你再次登录到 Azure。


6. 选择“登录”，重新连接会话。 有关验证码的相关信息，请在配置管理器中，参阅“摘要”或“使用 Azure 恢复服务保管库注册”部分 。

7. 成功登录后，系统即会显示订阅、资源组和恢复服务保管库的详细信息。 若要更改保管库，可以注销。 如果无需更改，选择“继续”以继续。

    ![已注册设备](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    注册成功后，继续配置 vCenter 详细信息。

    ![vCenter 的配置](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. 选择“添加 vCenter Server”，添加 vCenter 信息。 输入服务器名称或 vCenter 的 IP 地址和端口信息。 发布后，请提供用户名、密码和易记名称，以及用于获取[通过 vCenter 管理的虚拟机](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)的详细信息。 系统会加密用户帐户详细信息并将其存储在计算机本地。

>[!NOTE]
> 如果要向多个设备添加同一个 vCenter Server，请确保在设备中使用相同的易记名称。

9. 成功保存 vCenter 信息后，选择“添加虚拟机凭据”，提供通过 vCenter 发现的 VM 的用户详细信息。

   >[!NOTE]
   > - 对于 Linux OS，请务必提供根凭据；对于 Windows OS，应添加具有管理员权限的用户帐户，这些凭据将用于在启用复制操作期间将移动代理推送到源 VM。 在启用复制工作流期间，可以在 Azure 门户按照 VM 选择凭据。
   > - 访问设备配置器，编辑或添加用以访问计算机的凭证。

10. 成功添加详细信息后，选择“继续”，安装所有 Azure Site Recovery 复制设备组件并使用 Azure 服务注册。 此活动需要长达 30 分钟。

    确保不会在配置进行过程中关闭浏览器。

    >[!NOTE]
    > 此预览版不支持设备克隆。 如果尝试克隆，可能会中断恢复流。


## <a name="view-azure-site-recovery-replication-appliance-in-azure-portal"></a>查看 Azure 门户中的 Azure Site Recovery 复制设备

成功配置 Azure Site Recovery 复制设备后，导航到 Azure 门户中的“恢复服务保管库”。

选择“入门指南”下的“准备基础结构”，然后即可看到 Azure Site Recovery 复制设备已通过此保管库注册就绪 。 全部设置现已准备就绪！ 开始通过此复制设备保护源计算机。

单击“选择 1 设备后”，系统机会将你重定向到 Azure Site Recovery 复制设备视图，其中会显示已注册到此保管库的设备列表。

你还可在此查看“已发现项”的选项卡，其中列有所有已发现的 vCenter Server/vSphere 主机。

![复制设备（预览版）](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## <a name="sizing-and-capacity"></a>大小和容量
按照以下配置，使用内置进程服务器保护工作负荷的设备最多可以处理 200 台虚拟机：

  |CPU |    内存 |    缓存磁盘大小 |    数据更改率 |    受保护的计算机 |
  |---|-------|--------|------|-------|
  |16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5 GHz）    | 32 GB |    1 TB |    >1 TB 到 2 TB    | 用于复制 151 到 200 台计算机。|

- 你可以使用保管库中的任意复制设备，对 vCenter 服务器中的所有计算机执行发现操作。

- 如果选定的设备运行情况良好，则可以在同一保管库中的不同设备之间[切换受保护的计算机](switch-replication-appliance-preview.md)。

有关如何使用多个设备和利用复制设备转移故障的详细信息，请参阅[此文](switch-replication-appliance-preview.md)

## <a name="next-steps"></a>后续步骤
设置 [VMware VM](vmware-azure-set-up-replication-tutorial-preview.md) 到 Azure 的灾难恢复。
