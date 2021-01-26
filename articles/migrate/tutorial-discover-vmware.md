---
title: 使用 Azure Migrate 服务器评估工具发现 VMware VM
description: 了解如何使用 Azure Migrate 服务器评估工具发现本地 VMware VM
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 0e06d82c30743a4084cfc5ff856b4a9c8d548146
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566942"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>教程：使用服务器评估发现 VMware VM

在迁移到 Azure 的过程中，你将发现本地库存和工作负载。

本教程演示如何使用 Azure Migrate 发现本地 VMware 虚拟机 (VM)：服务器评估工具，使用轻型 Azure Migrate 设备。 将设备部署为 VMware VM，以持续发现 VM 及其性能元数据、VM 上运行的应用程序以及 VM 依赖项。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置 Azure 帐户。
> * 准备好用于发现的 VMware 环境。
> * 创建 Azure Migrate 项目。
> * 设置 Azure Migrate 设备。
> * 启动持续发现。

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。  

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

在开始本教程之前，请先检查是否已满足这些先决条件。


**要求** | **详细信息**
--- | ---
**vCenter Server/ESXi 主机** | 需要运行版本 5.5、6.0、6.5 或 6.7 的 vCenter Server。<br/><br/> VM 必须托管在 5.5 或更高版本的 ESXi 主机上。<br/><br/> 在 vCenter 服务器上，允许 TCP 端口 443 上的入站连接，以便设备可以收集配置和性能元数据。<br/><br/> 默认情况下，设备在端口 443 上连接到 vCenter。 如果 vCenter 服务器侦听其他端口，则可以在设备配置管理器上提供 vCenter 服务器详细信息时修改端口。<br/><br/> 在承载 VM 的 ESXi 服务器上，确保允许在 TCP 端口 443 上进行入站访问，以发现安装在 VM 上的应用程序和 VM 依赖项。
**设备** | vCenter Server 需要资源来为 Azure Migrate 设备分配 VM：<br/><br/> - 32 GB RAM、8 个 vCPU 以及约 80 GB 的磁盘存储。<br/><br/> - 外部虚拟交换机，以及设备 VM 上的的直接或通过代理实现的 Internet 访问。
**VM** | 所有 Windows 和 Linux OS 版本都支持发现配置和性能元数据，以及发现安装在 VM 上的应用程序。 <br/><br/> 查看[此处](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)，获取支持无代理依赖项分析的 OS 版本。<br/><br/> 若要发现已安装的应用程序和 VM 依赖项，必须安装并在 VM 上运行 VMware 工具（10.2.0 之后的版本），并且 Windows VM 必须安装 PowerShell 2.0 或更高版本。


## <a name="prepare-an-azure-user-account"></a>准备 Azure 用户帐户

若要创建 Azure Migrate 项目并注册 Azure Migrate 设备，需要一个具有以下权限的帐户：
- Azure 订阅的参与者或所有者权限
- 用于注册 Azure Active Directory (AAD) 应用的权限
- Azure 订阅上用于创建 Key Vault 的所有者或参与者以及用户访问管理员权限，在无代理 VMware 迁移期间使用

如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。 如果你不是订阅所有者，请让所有者分配权限，如下所示：

1. 在 Azure 门户中，搜索“订阅”，然后在“服务”下选择“订阅” 。

    ![用于搜索 Azure 订阅的搜索框](./media/tutorial-discover-vmware/search-subscription.png)

2. 在“订阅”页上，选择要在其中创建 Azure Migrate 项目的订阅。 
3. 在“订阅”中，选择“访问控制 (IAM)” > “检查访问权限” 。
4. 在“检查访问权限”中，搜索相关的用户帐户。
5. 在“添加角色分配”中，单击“添加” 。

    ![搜索用户帐户，检查访问权限，并分配角色](./media/tutorial-discover-vmware/azure-account-access.png)

6. 在“添加角色分配”中，选择“参与者”或“所有者”角色，然后选择帐户（在我们的示例中为 azmigrateuser）。 然后单击“保存”  。

    ![打开“添加角色分配”页，将角色分配给帐户](./media/tutorial-discover-vmware/assign-role.png)

1. 若要注册设备，你的 Azure 帐户需要具有注册 AAD 应用的权限。
1. 在 Azure 门户中，导航到“Azure Active Directory” > “用户” > “用户设置”  。
1. 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

    ![在用户设置中，验证用户是否可以注册 Active Directory 应用](./media/tutorial-discover-vmware/register-apps.png)

9. 如果“应用注册”设置设置为“否”，请请求租户/全局管理员分配所需的权限。 或者，租户/全局管理员可将“应用程序开发人员”角色分配给帐户，以允许注册 AAD 应用。 [了解详细信息](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>准备 VMware

在 vCenter Server 上，检查帐户是否有权使用 OVA 文件创建 VM。 使用 OVA 文件将 Azure Migrate 设备部署为 VMware VM 时，则需要这一权限。

服务器评估需要一个 vCenter Server 只读帐户来发现和评估 VMware 虚拟机。 如果还想要发现已安装的应用程序和 VM 依赖项，则该帐户需要为“虚拟机”>“来宾操作”启用权限。

### <a name="create-an-account-to-access-vcenter"></a>创建帐户以访问 vCenter

在 vSphere Web 客户端中，按如下方式设置帐户：

1. 使用具有管理员权限的帐户，在 vSphere Web 客户端中 > 选择“管理”。
2. 在“访问”中，选择“SSO 用户和组” 。
3. 在“用户”中，添加新用户。
4. 在“新用户”中，键入帐户详细信息。 。
5. 在“全局权限”中，选择用户帐户，并将“只读”角色分配给帐户 。 。
6. 如果还想发现已安装的应用程序和 VM 依赖项，请转到“角色”，选择“只读”角色，并在“权限”中选择“来宾操作”   。 通过选中“传播到子对象”复选框，可以将权限传播到 vCenter 服务器下的所有对象。
 
    ![用于允许对只读角色进行来宾操作的复选框](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>创建帐户以访问 VM

需要一个在 VM 上具有所需权限的用户帐户来发现已安装的应用程序和 VM 依赖项。 你可以在设备配置管理器上提供用户帐户。 设备不会在 VM 上安装任何代理。

1. 对于 Windows VM，请在虚拟机上创建具有管理权限的帐户（本地或域）。
2. 对于 Linux VM，请创建具有 Root 权限的帐户。 或者，可以在 /bin/netstat 和 /bin/ls 文件上创建具有以下权限的帐户：CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。

> [!NOTE]
> 目前，Azure Migrate 支持一个用于 Windows VM 的用户帐户和一个用于 Linux VM 的用户帐户，可以在设备上提供这些帐户来发现已安装的应用程序和 VM 依赖项。


## <a name="set-up-a-project"></a>设置项目

设置新的 Azure Migrate 项目。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概述”中，选择“创建项目” 。
5. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

   ![用于项目名称和区域的框](./media/tutorial-discover-vmware/new-project.png)

7. 选择“创建”。
8. 等待几分钟，让 Azure Migrate 项目部署完成。“Azure Migrate:服务器评估”工具添加到新项目。

![显示默认情况下已添加的服务器评估工具的页面](./media/tutorial-discover-vmware/added-tool.png)

> [!NOTE]
> 如果你已经创建了一个项目，则可以使用同一个项目注册其他设备，以发现和评估更多 VM。[了解更多](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>设置设备

“Azure Migrate:服务器评估使用轻型 Azure Migrate 设备。 此设备执行 VM 发现并将 VM 配置和性能元数据发送到 Azure Migrate。 可以通过部署 OVA 模板来设置设备，该文件可从 Azure Migrate 项目下载。

> [!NOTE]
> 如果由于某种原因而无法使用模板设置设备，则可以在现有的 Windows Server 2016 服务器上使用 PowerShell 脚本进行设置。 [了解详细信息](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。


### <a name="deploy-with-ova"></a>使用 OVA 进行部署

若要使用 OVA 模板设置设备，请执行以下操作：
1. 提供设备名称，并在门户中生成 Azure Migrate 项目密钥
1. 下载 OVA 模板文件，并将其导入 vCenter Server。 验证 OVA 是否安全。
1. 创建设备，并检查它是否可以连接到 Azure Migrate 服务器评估。
1. 完成设备的首次配置，并使用 Azure Migrate 项目密钥将其注册到 Azure Migrate 项目。

### <a name="1-generate-the-azure-migrate-project-key"></a>1.生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”  。
3. 在“1: 生成 Azure Migrate 项目密钥”中，提供将为发现 VMware VM 而设置的 Azure Migrate 设备的名称。该名称应为不超过 14 个字符的字母数字。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现计算机”页。
1. 成功创建 Azure 资源后，会生成一个 Azure Migrate 项目密钥。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="2-download-the-ova-template"></a>2.下载 OVA 模板

在“2:下载 Azure Migrate 设备”中，选择 .OVA 文件并单击“下载”。

### <a name="verify-security"></a>验证安全性

在部署 OVA 文件之前检查其安全性：

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，生成 OVA 文件的哈希：
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   用法示例：```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. 验证最新的设备版本和哈希值：

    - 对于 Azure 公有云：
    
        **算法** | **下载** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - 对于 Azure 政府：
    
        **算法** | **下载** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-vm"></a>3.创建设备 VM

导入下载的文件，然后创建 VM。

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。
2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为 VM 指定一个易记名称。 选择要在其中托管 VM 的库存对象。
5. 在“主机/群集”中，指定要在其上运行 VM 的主机或群集。
6. 在“存储”中，指定 VM 的存储目标。
7. 在“磁盘格式”中，指定磁盘类型和大小。
8. 在“网络映射”中，指定 VM 要连接到的网络。 该网络需要与 Internet 建立连接，这样才能向 Azure Migrate 服务器评估发送元数据。
9. 检查并确认设置，然后单击“完成”。


### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


### <a name="4-configure-the-appliance"></a>4.配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 OVA 设置设备，则此过程中的前两个步骤不相关。

1. 在 vSphere 客户端控制台中右键单击“VM”，然后选择“打开控制台”。
2. 提供设备的语言、时区和密码。
3. 在可连接到该 VM 的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL：**https://*设备名称或 IP 地址*:** 44368。

   或者，可以在设备桌面上选择应用快捷方式打开该应用。
1. 接受许可条款，并阅读第三方信息。
1. 在 Web 应用 >“设置必备组件”中执行以下操作：
   - **连接**：应用将检查 VM 是否可访问 Internet。 如果 VM 使用代理：
     - 单击“设置代理”，指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）以及侦听端口。
     - 如果代理需要身份验证，请指定凭据。
     - 仅支持 HTTP 代理。
     - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击“查看设备服务”查看设备上运行的组件的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

     Azure Migrate 服务器迁移在迁移到 Azure 期间使用 VDDK 复制计算机。 
1. 如果需要，可以在设备配置期间随时重新运行先决条件，以检查设备是否满足所有先决条件。

### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请转到“服务器评估”>“发现”>“管理现有设备”，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
1. 需要使用设备代码对 Azure 进行身份验证。 单击“登录”将打开包含设备代码的模式，如下所示。

    ![显示设备代码的模式](./media/tutorial-discover-vmware/device-code.png)

1. 单击“复制代码并登录”以复制设备代码，并在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，粘贴设备代码并使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 如果在未登录的情况下意外关闭了登录选项卡，则需要刷新设备配置管理器的浏览器选项卡以再次启用“登录”按钮。
1. 成功登录后，使用设备配置管理器返回到上一个选项卡。
1. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的权限，会启动设备注册。
1. 成功注册设备后，可以通过单击“查看详细信息”来查看注册详细信息。



## <a name="start-continuous-discovery"></a>启动持续发现

设备需连接 vCenter Server，以发现 VM 的配置和性能数据。

1. 在 **步骤 1：提供 vCenter Server 凭据** 中，单击“添加凭据”以指定凭据的易记名称，为设备将用于在 vCenter Server 实例上发现 VM 的 vCenter Server 帐户添加“用户名”和“密码”  。
    - 你应该已在上一教程中设置具有所需权限的帐户。
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM），请参阅[此文](set-discovery-scope.md)中的说明，以限制 Azure Migrate 所使用的帐户。
1. 在 **步骤 2：提供 vCenter Server 详细信息** 中，单击“添加发现源”，从下拉列表中选择凭据的易记名称，指定 vCenter Server 实例的 IP 地址/FQDN 。 可以保留“端口”的默认值 443，或指定 vCenter Server 侦听的自定义端口，然后单击“保存” 。
1. 单击“保存”后，设备将尝试通过提供的凭据验证与 vCenter Server 的连接，并根据 vCenter Server IP 地址/FQDN 在表中显示验证状态。
1. 在开始发现之前，可以随时重新验证与 vCenter Server 之间的连接。
1. 在 **步骤 3：提供 VM 凭据来发现已安装的应用程序并执行无代理依赖项映射** 中，单击“添加凭据”，并指定为其提供凭据的操作系统、凭据的易记名称以及“用户名”和“密码”  。 然后单击“保存”。

    - 如果已创建用于[应用程序发现](how-to-discover-applications.md)或[无代理依赖项分析](how-to-create-group-machine-dependencies-agentless.md)的帐户，则可选择在此处添加凭据。
    - 如果不想使用这些功能，请通过单击滑块跳过该步骤。 你以后可以随时恢复使用这些功能。
    - 查看[应用程序发现](migrate-support-matrix-vmware.md#application-discovery-requirements)或[无代理依赖项分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)所需的帐户权限。

5. 单击“开始发现”以启动 VM 发现。 成功启动发现后，可以针对表中的 vCenter Server IP 地址/FQDN 检查发现状态。

发现的工作原理如下：
- 大约 15 分钟后，已发现 VM 的元数据会显示在门户中。
- 发现已安装的应用程序、角色和功能需要一些时间， 具体时间取决于待发现的 VM 数量。 如果是 500 个 VM，Azure Migrate 门户大约需要一小时才会显示应用程序清单。
- 完成 VM 发现后，可以从门户对所需的虚拟机启用无代理依赖项分析。


## <a name="next-steps"></a>后续步骤

- [评估 VMware VM](./tutorial-assess-vmware-azure-vm.md) 以便迁移到 Azure VM。
- [查看设备在发现期间收集的数据](migrate-appliance.md#collected-data---vmware)。