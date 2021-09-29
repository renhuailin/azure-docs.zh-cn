---
title: 使用 Azure Migrate 发现和评估发现 Hyper-V 上的服务器
description: 了解如何使用 Azure Migrate 发现和评估工具发现 Hyper-V 上的本地服务器。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 8ee94cf4c02c5535a85b75eec5a60522143a16ce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748713"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>教程：使用 Azure Migrate：发现和评估发现 Hyper-V 上运行的服务器

在迁移到 Azure 的过程中，你将发现本地库存和工作负载。

本教程演示如何采用轻型 Azure Migrate 设备来使用 Azure Migrate：发现和评估工具发现 Hyper-V 主机上的本地服务器。 将设备部署为 Hyper-V 主机上的服务器，以持续发现计算机和性能元数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置 Azure 帐户
> * 准备 Hyper-V 环境以进行发现。
> * 创建项目。
> * 设置 Azure Migrate 设备。
> * 启动持续发现。

> [!NOTE]
> 教程显示尝试方案的最快路径，并使用默认选项。  

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，请先检查是否已满足这些先决条件。

**要求** | **详细信息**
--- | ---
**Hyper-V 主机** | 服务器所在的 Hyper-V 主机可以是独立的，也可以在群集中。<br/><br/> 主机必须运行 Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/><br/> 验证 WinRM 端口 5985 (HTTP) 上是否允许入站连接，使设备可以使用通用信息模型（CIM）会话连接到拉取服务器元数据和性能数据。
**设备部署** | Hyper-V 主机需要资源来为设备分配服务器：<br/><br/> - 16 GB RAM、8 个 vCPU 以及约 80 GB 的磁盘存储。<br/><br/> - 外部虚拟交换机，以及设备上的直接或通过代理实现的 Internet 访问。
**服务器** | 服务器可以运行任何 Windows 或 Linux 操作系统。

## <a name="prepare-an-azure-user-account"></a>准备 Azure 用户帐户

若要创建项目并注册 Azure Migrate 设备，需要一个具有以下权限的帐户：
- Azure 订阅的参与者或所有者权限。
- 用于注册 Azure Active Directory (AAD) 应用的权限。

如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。 如果你不是订阅所有者，请让所有者分配权限，如下所示：

1. 在 Azure 门户中，搜索“订阅”，然后在“服务”下选择“订阅” 。

    ![用于搜索 Azure 订阅的搜索框](./media/tutorial-discover-hyper-v/search-subscription.png)

2. 在“订阅”页上，选择要在其中创建项目的订阅。
3. 在“订阅”中，选择“访问控制 (IAM)” > “检查访问权限” 。
4. 在“检查访问权限”中，搜索相关的用户帐户。
5. 在“添加角色分配”中，单击“添加” 。

    ![搜索用户帐户，检查访问权限，并分配角色](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. 在“添加角色分配”中，选择“参与者”或“所有者”角色，然后选择帐户（在我们的示例中为 azmigrateuser）。 然后单击“保存”  。

    ![打开“添加角色分配”页，将角色分配给帐户](./media/tutorial-discover-hyper-v/assign-role.png)

1. 若要注册设备，你的 Azure 帐户需要具有注册 AAD 应用的权限。
1. 在 Azure 门户中，导航到“Azure Active Directory” > “用户” > “用户设置”  。
1. 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

    ![在用户设置中，验证用户是否可以注册 Active Directory 应用](./media/tutorial-discover-hyper-v/register-apps.png)

9. 如果“应用注册”设置设置为“否”，请请求租户/全局管理员分配所需的权限。 或者，租户/全局管理员可将“应用程序开发人员”角色分配给帐户，以允许注册 AAD 应用。 [了解详细信息](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-hyper-v-hosts"></a>准备 Hyper-V 主机

可以手动或使用脚本来准备 Hyper-V 主机。 准备步骤汇总在下表中。 脚本会自动进行这些准备。

**步骤** | **脚本** | **手动**
--- | --- | ---
验证主机要求 | 检查主机是否正在运行受支持的 Hyper-V 版本，并检查 Hyper-V 角色。<br/><br/>启用 WinRM 服务，并在主机上打开端口 5985 (HTTP) 和 5986 (HTTPS)（收集元数据时需要使用这些端口）。 | 主机必须运行 Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/><br/> 验证 WinRM 端口 5985 (HTTP) 上是否允许入站连接，使设备可以使用通用信息模型（CIM）会话连接到拉取服务器元数据和性能数据。<br/><br/> 具有非英语区域设置的主机目前不支持该脚本。  
验证 PowerShell 版本 | 检查你是否在受支持的 PowerShell 版本中运行该脚本。 | 检查是否正在 Hyper-V 主机上运行 PowerShell 版本 4.0 或更高版本。
创建帐户 | 验证你在 Hyper-V 主机上是否具有正确的权限。<br/><br/> 使你可以创建具有正确权限的本地用户帐户。 | 选项 1：准备一个具有对 Hyper-V 主机的管理员访问权限的帐户。<br/><br/> 选项 2：准备一个本地管理员帐户或域管理员帐户，并将该帐户添加到这些组中：远程管理用户、Hyper-V 管理员和性能监视器用户。
启用 PowerShell 远程处理 | 在每台主机上启用 PowerShell 远程控制，使 Azure Migrate 设备能够通过 WinRM 连接在主机上运行 PowerShell 命令。 | 若要设置，请在每台主机上，以管理员身份打开 PowerShell 控制台，然后运行以下命令：``` powershell Enable-PSRemoting -force ```
设置 Hyper-V 集成服务 | 检查主机管理的所有服务器上是否已启用 Hyper-V Integration Services。 | 在每个服务器上[启用 Hyper-V 集成服务](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。<br/><br/> 如果运行的是 Windows Server 2003，请[遵循这些说明](prepare-windows-server-2003-migration.md)。
如果服务器磁盘位于远程 SMB 共享上，则委派凭据 | 委托凭据 | 运行以下命令，以使 CredSSP 可以在运行服务器且具有 SMB 共享上的磁盘的主机上委派凭据：```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> 可在所有 Hyper-v 主机上远程运行此命令。<br/><br/> 如果在群集中添加了新的主机节点，则会自动添加这些节点以供发现，但需要手动启用 CredSSP。<br/><br/> 设置设备时，[在设备上启用 CredSSP](#delegate-credentials-for-smb-vhds) 即可完成 CredSSP 的设置。 

### <a name="run-the-script"></a>运行脚本

1. 从 [Microsoft 下载中心](https://aka.ms/migrate/script/hyperv)下载脚本。 该脚本已由 Microsoft 加密签名。
2. 使用 MD5 或 SHA256 哈希文件验证脚本完整性。 井号标签值如下。 运行以下命令生成脚本的哈希：

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    用法示例：

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. 验证脚本完整性后，在每台 Hyper-V 主机上使用以下提升了权限的 PowerShell 命令运行该脚本：

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
哈希值为:

**哈希** |  **值**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>设置项目

设置新项目。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概述”中，选择“创建项目” 。
5. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

   ![用于项目名称和区域的框](./media/tutorial-discover-hyper-v/new-project.png)

7. 选择“创建”。
8. 等待几分钟，让项目完成部署。 默认会将“Azure Migrate：发现和评估”工具添加到新项目。

![显示 Azure Migrate：默认添加的发现和评估工具的页面](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> 如果你已经创建了一个项目，则可以使用同一个项目注册其他设备，以发现和评估更多服务器。[了解更多](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>设置设备

Azure Migrate 使用轻型 Azure Migrate 设备。 设备执行服务器发现并将服务器配置和性能元数据发送到 Azure Migrate。 可以通过部署 VHD 文件来设置设备，该文件可从项目下载。

> [!NOTE]
> 如果由于某种原因而无法使用模板设置设备，则可以在现有的 Windows Server 2016 服务器上使用 PowerShell 脚本进行设置。 [了解详细信息](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)。<br/>
> Azure 政府云不支持使用 VHD 模板部署设备的选项。 [详细了解](./deploy-appliance-script-government.md)如何为 Azure 政府云部署设备。

本教程在 Hyper-V 环境中运行的服务器上设置设备，如下所示：

1. 提供设备名称，并在门户中生成项目密钥。
1. 从 Azure 门户下载压缩的 Hyper-V VHD。
1. 创建设备，并检查它是否可以连接到 Azure Migrate：发现和评估。
1. 完成设备的首次配置，并使用项目密钥将其注册到项目。

### <a name="1-generate-the-project-key"></a>1.生成项目密钥

1. 在 **迁移目标**  >  **服务器**  >  **Azure Migrate：发现和评估** 中，选择 **发现**。
2. 在“发现服务器” > “服务器是否已虚拟化?”中，选择“是，使用 Hyper-V”  。
3. 在“1：生成项目密钥”中，提供将为发现服务器而设置的 Azure Migrate 设备的名称。 该名称应是字母数字，长度为 14 个或更少的字符。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭发现服务器页。
1. 成功创建 Azure 资源后，会生成一个 **项目密钥**。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="2-download-the-vhd"></a>2.下载 VHD

在“2: 下载 Azure Migrate 设备”中，选择 .VHD 文件，然后单击“下载”。

### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。

2. 运行以下 PowerShell 命令以生成 ZIP 文件的哈希
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 用法示例：```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  验证最新的设备版本和哈希值：

    - 对于 Azure 公有云：

        **方案** | **下载** | **SHA256**
        --- | --- | ---
        Hyper-V (8.91 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - 对于 Azure 政府：

        方案_ | _下载* | **SHA256**
        --- | --- | ---
        Hyper-V (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3.创建设备

导入下载的文件，然后创建设备。

1. 将压缩的 VHD 文件解压缩到托管设备的 Hyper-V 主机上的某个文件夹中。 将解压缩三个文件夹。
2. 打开 Hyper-V 管理器。 在“操作”中，单击“导入虚拟机”。 
2. 在“导入虚拟机向导”>“开始之前”中，单击“下一步”。 
3. 在“查找文件夹”中，指定包含已解压缩的 VHD 的文件夹。 然后单击“下一步”。
1. 在“选择虚拟机”中，单击“下一步”。 
2. 在“选择导入类型”中，单击“复制虚拟机(创建新的唯一 ID)”。  然后单击“下一步”。
3. 在“选择目标”中保留默认设置。 单击“下一步”。
4. 在“存储文件夹”中保留默认设置。 单击“下一步”。
5. 在“选择网络”中，指定设备要使用的虚拟交换机。 该交换机需要与 Internet 建立连接才能向 Azure 发送数据。
6. 在“摘要”中检查设置。 然后单击“完成”。
7. 在 Hyper-V 管理器 >“虚拟机”中启动设备。

### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

### <a name="4-configure-the-appliance"></a>4.配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 VHD 设置设备，则此过程中的前两个步骤不相关。

1. 在Hyper-V 管理器 >“虚拟机”中，右键单击该设备 >“连接”。 
2. 提供设备的语言、时区和密码。
3. 在可连接到该设备的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL： https://设备名称或 IP 地址:44368。

   或者，可以在设备桌面上单击应用快捷方式打开该应用。
1. 接受许可条款，并阅读第三方信息。
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **连接**：应用将检查设备是否可访问 Internet。 如果设备使用代理：
      - 单击“设置代理”，并指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）和侦听端口。
      - 如果代理需要身份验证，请指定凭据。
      - 仅支持 HTTP 代理。
      - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
    - **时间同步**：将验证时间。 设备上的时间应与 Internet 时间同步，这样才能正常发现服务器。
    - **安装更新**：Azure Migrate：发现和评估将检查设备上是否安装了最新更新。 检查完成后，可以单击“查看设备服务”查看设备上运行的组件的状态和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 **项目密钥**。 如果没有密钥，请转到 **Azure Migrate：发现和评估 > 发现 > 管理现有设备**，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
1. 需要使用设备代码对 Azure 进行身份验证。 单击“登录”将打开包含设备代码的模式，如下所示。

    ![显示设备代码的模式](./media/tutorial-discover-vmware/device-code.png)

1. 单击“复制代码并登录”以复制设备代码，并在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，粘贴设备代码并使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 如果在未登录的情况下意外关闭了登录选项卡，则需要刷新设备配置管理器的浏览器选项卡以再次启用“登录”按钮。
1. 成功登录后，使用设备配置管理器返回到上一个选项卡。
4. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的权限，会启动设备注册。
1. 成功注册设备后，可以通过单击“查看详细信息”来查看注册详细信息。

### <a name="delegate-credentials-for-smb-vhds"></a>为 SMB VHD 委托凭据

如果在 SMB 上运行 VHD，必须启用从设备到 Hyper-V 主机的凭据委托。 若要从设备执行此操作：

1. 在设备上，运行此命令。 HyperVHost1/HyperVHost2 是示例主机名。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. 或者，在设备上的本地组策略编辑器中执行此操作：
    - 在“本地计算机策略” > “计算机配置”中，单击“管理模板” > “系统” > “凭据委托”。    
    - 双击“允许委托新凭据”，并选择“已启用”。 
    - 在“选项”中单击“显示”，将要发现的每台 Hyper-V 主机添加到列表中，并使用 wsman/ 作为前缀。  
    - 在“凭据委托”中，双击“允许允许新凭据并使用仅限 NTLM 的服务器身份验证”。 再次将要发现的每台 Hyper-V 主机添加到列表中，并使用 **wsman/** 作为前缀。

## <a name="start-continuous-discovery"></a>启动持续发现

从设备连接到 Hyper-V 主机或群集，并启动服务器发现。

1. 在 **步骤 1：提供 Hyper-V 主机凭据** 中，单击“添加凭据”以指定凭据的易记名称，为设备将用于发现服务器的 Hyper-V 主机/群集添加“用户名”和“密码”。   单击“保存” 。
1. 如果要一次添加多个凭据，请单击“添加更多”，以保存和添加更多凭据。 在 Hyper-V 环境中发现服务器支持多个凭据。
1. 在 **步骤 2：提供 Hyper-V 主机/群集详细信息** 中，单击“添加发现源”，以指定 Hyper-V 主机/群集 IP 地址/FQDN 以及用于连接到主机/群集的凭据的易记名称。
1. 可以一次“添加单个项目”，也可以一次“添加多个项目” 。 还有一个选项是通过“导入 CSV”提供 Hyper-V 主机/群集详细信息。

    - 如果选择“添加单个项目”，则需要指定凭据的易记名称和 Hyper-V 主机/群集 IP 地址/FQDN，然后单击“保存”。
    - 如果选择 **添加多个项目** _（默认情况下处于选中状态）_ ，则可以通过在文本框中指定 Hyper-V 主机/群集 **IP 地址/FQDN** 和凭据的易记名称来一次添加多个记录。 **验证****已添加的记录，然后单击 **保存**。
    - 如果选择“导入 CSV”，可以下载 CSV 模板文件，并使用 Hyper-V 主机/群集 IP 地址/FQDN 和凭据的易记名称填充文件。 然后，将该文件导入设备，验证文件中的记录，然后单击“保存” 。

1. 单击“保存”后，设备将尝试验证与已添加 Hyper-V 主机/群集的连接，并在表中显示每个主机/群集的“验证状态”。
    - 对于验证成功的主机/群集，可以通过单击其 IP 地址/FQDN 来查看更多详细信息。
    - 如果主机验证失败，请通过单击表中“状态”列的“验证失败”来查看错误。 解决此问题，然后再次验证。
    - 若要删除主机或群集，请单击“删除”。
    - 无法删除群集中的特定主机。 只能删除整个群集。
    - 即使群集中的特定主机出现问题，你也可以添加群集。
1. 在开始发现之前，可以随时 **重新验证** 与主机/群集之间的连接。
1. 单击“启动发现”，从已成功验证的主机/群集开始发现服务器。 成功启动发现后，你可以针对表中的每个主机/群集检查发现状态。

随即会启动发现。 在 Azure 门户中，每台主机大约需要 2 分钟才能显示发现的服务器的元数据。

## <a name="verify-servers-in-the-portal"></a>验证门户中的服务器

发现完成后，可以验证服务器是否出现在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在 **Azure Migrate - 服务器**  >  **Azure Migrate：发现和评估** 页中，单击显示了 **已发现服务器** 计数的图标。

## <a name="next-steps"></a>后续步骤

- [评估 Hyper-V 环境上的服务器](tutorial-assess-hyper-v.md) 以迁移到 Azure VM。
- [查看设备在发现期间收集的数据](migrate-appliance.md#collected-data---hyper-v)。