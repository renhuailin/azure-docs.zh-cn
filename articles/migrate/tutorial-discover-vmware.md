---
title: '使用“Azure Migrate: 服务器评估”发现 VMware 环境中运行的服务器'
description: 了解如何使用 Azure Migrate 服务器评估工具发现本地 VMware VM
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 4d2b0fbb377beacdb75a1a5552855936bee2b205
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041305"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-server-assessment"></a>教程：使用“Azure Migrate: 服务器评估”发现 VMware 环境中运行的服务器

在迁移到 Azure 的过程中，你将发现本地库存和工作负载。

本教程介绍如何使用一个轻型 Azure Migrate 设备通过“Azure Migrate: 服务器评估”工具发现 VMware 环境中运行的服务器。 你要将该设备部署为 vCenter Server 中运行的服务器，以持续发现服务器及其性能元数据、服务器上运行的应用程序、服务器依赖项，以及 SQL Server 实例和数据库。

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
**vCenter Server/ESXi 主机** | 需要运行版本 5.5、6.0、6.5 或 6.7 的 vCenter Server。<br/><br/> 服务器必须托管在运行 5.5 或更高版本的 ESXi 主机上。<br/><br/> 在 vCenter 服务器上，允许 TCP 端口 443 上的入站连接，以便设备可以收集配置和性能元数据。<br/><br/> 默认情况下，设备通过端口 443 连接到 vCenter Server。 如果 vCenter 服务器侦听其他端口，则可以在设备配置管理器上提供 vCenter 服务器详细信息时修改端口。<br/><br/> 在 ESXi 主机上，请确保允许通过 TCP 端口 443 进行入站访问，以便能够在服务器上执行已安装应用程序的发现和无代理依赖项分析。
**设备** | vCenter Server 需要资源来为 Azure Migrate 设备分配服务器：<br/><br/> - 32 GB RAM、8 个 vCPU 以及约 80 GB 的磁盘存储。<br/><br/> - 一台外部虚拟交换机，可在设备服务器上直接或通过代理进行 Internet 访问。
**服务器** | 所有 Windows 和 Linux OS 版本均受支持，可用于发现配置和性能元数据。 <br/><br/> 为了在服务器上执行应用程序发现，所有 Windows 和 Linux OS 版本均受支持。 查看[此处](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)，获取支持无代理依赖项分析的 OS 版本。<br/><br/> 若要执行已安装应用程序的发现和无代理依赖项分析，必须在服务器上安装并运行 VMware 工具（版本高于 10.2.0）。 在 Windows 服务器上必须安装 PowerShell 2.0 或更高版本。<br/><br/> 若要发现 SQL Server 实例和数据库，请查看[此文](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases)了解支持的 SQL Server 版本、支持的 Windows OS 版本和身份验证机制。

> [!Note]
> 目前，对 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估功能以预览版提供。 若要试用此功能，请使用[此链接](https://aka.ms/AzureMigrate/SQL)在澳大利亚东部区域创建一个项目 。 如果在澳大利亚东部已有项目，并且想要尝试此功能，请确保已在门户上完成这些[先决条件](how-to-discover-sql-existing-project.md)。

## <a name="prepare-an-azure-user-account"></a>准备 Azure 用户帐户

若要创建 Azure Migrate 项目并注册 Azure Migrate 设备，需要一个具有以下权限的帐户：
- Azure 订阅的参与者或所有者权限
- 用于注册 Azure Active Directory (AAD) 应用的权限
- Azure 订阅上用于创建密钥保管库的“所有者”或“参与者”以及“用户访问管理员”权限，在无代理服务器迁移期间使用

如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。 如果你不是订阅所有者，请让所有者分配权限，如下所示：

1. 在 Azure 门户中，搜索“订阅”，然后在“服务”下选择“订阅” 。

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="用于搜索 Azure 订阅的搜索框":::


2. 在“订阅”页上，选择要在其中创建 Azure Migrate 项目的订阅。
3. 在“订阅”中，选择“访问控制 (IAM)” > “检查访问权限” 。
4. 在“检查访问权限”中，搜索相关的用户帐户。
5. 在“添加角色分配”中，单击“添加” 。
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="搜索用户帐户，检查访问权限，并分配角色":::
    
6. 在“添加角色分配”中，选择“参与者”或“所有者”角色，然后选择帐户（在我们的示例中为 azmigrateuser）。 然后单击“保存”  。

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="打开“添加角色分配”页，将角色分配给帐户":::

1. 若要注册设备，你的 Azure 帐户需要具有注册 AAD 应用的权限。
1. 在 Azure 门户中，导航到“Azure Active Directory” > “用户” > “用户设置”  。
1. 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="在用户设置中，验证用户是否可以注册 Active Directory 应用":::

9. 如果“应用注册”设置设置为“否”，请请求租户/全局管理员分配所需的权限。 或者，租户/全局管理员可将“应用程序开发人员”角色分配给帐户，以允许注册 AAD 应用。 [了解详细信息](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>准备 VMware

在 vCenter Server 上，检查帐户是否有权使用 OVA 文件创建 VM。 使用 OVA 文件将 Azure Migrate 设备部署为 VMware VM 时，则需要这一权限。

Azure Migrate 需使用一个 vCenter Server 只读帐户来发现和评估 VMware 环境中运行的服务器。 如果你还要执行已安装应用程序的发现和无代理依赖项分析，则需要为该帐户启用“虚拟机”>“来宾操作”特权。

### <a name="create-an-account-to-access-vcenter"></a>创建帐户以访问 vCenter

在 vSphere Web 客户端中，按如下方式设置帐户：

1. 使用具有管理员权限的帐户，在 vSphere Web 客户端中 > 选择“管理”。
2. 在“访问”中，选择“SSO 用户和组” 。
3. 在“用户”中，添加新用户。
4. 在“新用户”中，键入帐户详细信息。 。
5. 在“全局权限”中，选择用户帐户，并将“只读”角色分配给帐户 。 。
6.  如果你还要执行已安装应用程序的发现和无代理依赖项分析，请转到“角色”，选择“只读”角色，然后在“特权”中选择“来宾操作”   。 通过选中“传播到子对象”复选框，可以将权限传播到 vCenter 服务器下的所有对象。

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="用于允许对只读角色进行来宾操作的复选框":::


> [!NOTE]
> 可以通过限定 vCenter Server 帐户的范围，将发现范围限制为特定的 vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个服务器。 [**详细了解**](set-discovery-scope.md)如何限定 vCenter Server 用户帐户的范围。


### <a name="create-an-account-to-access-servers"></a>创建用于访问服务器的帐户

需要一个在服务器上拥有所需特权的用户帐户，才能执行已安装应用程序的发现、无代理依赖项分析和 SQL Server 实例与数据库的发现。 你可以在设备配置管理器上提供用户帐户。 设备不会在服务器上安装任何代理。

1. 对于 Windows 服务器，请在服务器上创建拥有管理权限的帐户（本地或域）。 若要发现 SQL Server 实例和数据库，该 Windows 或 SQL Server 帐户需是 sysadmin 服务器角色的成员。 [详细了解](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)如何向用户帐户分配所需的角色。
2. 对于 Linux 服务器，请创建拥有 Root 特权的帐户。 或者，可以在 /bin/netstat 和 /bin/ls 文件上创建具有以下权限的帐户：CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。

> [!NOTE]
> 现在可以在配置管理器上添加多个服务器凭据，以执行已安装应用程序的发现、无代理依赖项分析和 SQL Server 实例与数据库的发现。可以添加多个域/Windows（非域）/Linux（非域）和/或 SQL Server 身份验证凭据。 [**了解详细信息**](add-server-credentials.md)

## <a name="set-up-a-project"></a>设置项目

设置新的 Azure Migrate 项目。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概述”中，选择“创建项目” 。
5. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="用于项目名称和区域的框":::

7. 选择“创建”。
8. 等待几分钟，让 Azure Migrate 项目部署完成。“Azure Migrate:服务器评估”工具添加到新项目。

> [!NOTE]
> 如果你已创建一个项目，可以使用同一个项目注册其他设备，以发现和评估更多的 服务器。[**了解详细信息**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>设置设备

“Azure Migrate:服务器评估使用轻型 Azure Migrate 设备。 设备执行服务器发现并将服务器配置和性能元数据发送到 Azure Migrate。 可通过部署一个 OVA 模板来设置设备，该模板可从项目下载。

> [!NOTE]
> 如果由于某种原因而无法使用模板设置设备，则可以在现有的 Windows Server 2016 服务器上使用 PowerShell 脚本进行设置。 [**了解详细信息**](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。

### <a name="deploy-with-ova"></a>使用 OVA 进行部署

若要使用 OVA 模板设置设备，请执行以下操作：
1. 提供设备名称，并在门户中生成 Azure Migrate 项目密钥。
1. 下载 OVA 模板文件，并将其导入 vCenter Server。 验证 OVA 是否安全。
1. 基于 OVA 文件创建设备 VM，并检查它是否可以连接到 Azure Migrate。
1. 完成设备的首次配置，并使用 Azure Migrate 项目密钥将其注册到项目。

### <a name="1-generate-the-azure-migrate-project-key"></a>1.生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”  。
3. 在“1: 生成 Azure Migrate 项目密钥”中，为你要设置（用于发现 VMware 环境中的服务器）的 Azure Migrate 设备提供一个名称。该名称应为不超过 14 个字符的字母数字。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现”页。
1. 成功创建 Azure 资源后，会生成一个 Azure Migrate 项目密钥。
1. 复制密钥，因为配置设备时需要输入该密钥才能完成设备注册。

### <a name="2-download-the-ova-template"></a>2.下载 OVA 模板

在“2:下载 Azure Migrate 设备”中，选择 .OVA 文件并单击“下载”。

### <a name="verify-security"></a>验证安全性

在部署 OVA 文件之前检查其安全性：

1. 在下载文件的服务器上，打开管理员命令窗口。
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

### <a name="3-create-the-appliance-server"></a>3.创建设备服务器

导入下载的文件，在 VMware 环境中创建服务器

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。
2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为服务器指定一个易记名称。  选择要在其中托管该服务器的库存对象。
5. 在“主机/群集”中，指定要在其上运行该服务器的主机或群集。
6. 在“存储”中，指定该服务器的存储目标。
7. 在“磁盘格式”中，指定磁盘类型和大小。
8. 在“网络映射”中，指定该服务器要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure Migrate 发送元数据。
9. 检查并确认设置，然后单击“完成”。


### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备服务器可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


### <a name="4-configure-the-appliance"></a>4.配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [**PowerShell 脚本**](deploy-appliance-script.md)而不是下载的 OVA 设置设备，则此过程中的前两个步骤不相关。

1. 在 vSphere 客户端控制台中右键单击该服务器，然后选择“打开控制台”。
2. 提供设备的语言、时区和密码。
3. 在可以连接到设备服务器的任何计算机上打开一个浏览器，然后打开设备配置管理器的 URL：`https://appliance name or IP address: 44368`。

   或者，可以通过选择配置管理器的快捷方式，从设备服务器桌面打开配置管理器。
1. 接受许可条款，并阅读第三方信息。
1. 在配置管理器 >“设置先决条件”中执行以下操作：
   - **连接**：设备将检查服务器是否可访问 Internet。 如果服务器使用代理：
     - 单击“设置代理”，指定代理地址 `http://ProxyIPAddress` 或 `http://ProxyFQDN` 以及侦听端口。
     - 如果代理需要身份验证，请指定凭据。
     - 仅支持 HTTP 代理。
     - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击“查看设备服务”查看设备服务器上运行的服务的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

     Azure Migrate 服务器迁移在迁移到 Azure 期间使用 VDDK 来复制服务器。 
1. 如果需要，可以在设备配置期间随时重新运行先决条件，以检查设备是否满足所有先决条件。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="设备配置管理器上的面板 1":::


### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请转到“服务器评估”>“发现”>“管理现有设备”，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
1. 需要使用设备代码对 Azure 进行身份验证。 单击“登录”将打开包含设备代码的模式，如下所示。

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="显示设备代码的模式":::

1. 单击“复制代码并登录”以复制设备代码，并在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，粘贴设备代码并使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 如果在未登录的情况下意外关闭了登录选项卡，则需要刷新设备配置管理器的浏览器选项卡以再次启用“登录”按钮。
1. 成功登录后，使用设备配置管理器返回到上一个选项卡。
1. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的权限，会启动设备注册。
1. 成功注册设备后，可以通过单击“查看详细信息”来查看注册详细信息。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="设备配置管理器上的面板 2":::

## <a name="start-continuous-discovery"></a>启动持续发现

### <a name="provide-vcenter-server-details"></a>提供 vCenter Server 详细信息

设备需连接到 vCenter Server 才能发现服务器的配置和性能数据。

1. 在“步骤 1: 提供 vCenter Server 凭据”中，单击“添加凭据”以指定凭据的易记名称，为设备要用于发现 vCenter Server 上运行的服务器的 vCenter Server 帐户添加“用户名”和“密码”。   
    - 你应已按照本文前面所述设置了一个拥有所需权限的帐户。
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM），请参阅[此文](set-discovery-scope.md)中的说明，以限制 Azure Migrate 所使用的帐户。
1. 在“步骤 2: 提供 vCenter Server 详细信息”中单击“添加发现源”，从下拉列表中选择凭据的易记名称，并指定 vCenter Server 的“IP 地址/FQDN”。   可以保留“端口”的默认值 443，或指定 vCenter Server 侦听的自定义端口，然后单击“保存” 。
1. 单击“保存”后，设备将尝试通过提供的凭据验证与 vCenter Server 的连接，并根据 vCenter Server IP 地址/FQDN 在表中显示“验证状态” 。
1. 在开始发现之前，可以随时重新验证与 vCenter Server 之间的连接。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="设备配置管理器上的面板 3，用于显示 vCenter Server 详细信息":::

### <a name="provide-server-credentials"></a>提供服务器凭据

在“步骤 3: 提供服务器凭据以执行软件盘点、无代理依赖项分析以及 SQL Server 实例和数据库的发现”中，可以选择提供多个服务器凭据，或者，如果你不想要利用这些功能，可以选择跳过该步骤并继续执行 vCenter Server 发现。 以后你随时可以改变想法。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="设备配置管理器上的面板 3，用于显示服务器详细信息":::

> [!Note]
> 目前，对 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估功能以预览版提供。 若要试用此功能，请使用[此链接](https://aka.ms/AzureMigrate/SQL)在澳大利亚东部区域创建一个项目 。 如果在澳大利亚东部已有项目，并且想要尝试此功能，请确保已在门户上完成这些[先决条件](how-to-discover-sql-existing-project.md)。

若要利用这些功能，可遵循以下步骤提供服务器凭据。设备会尝试自动将凭据映射到服务器以执行发现功能。

- 可以单击“添加凭据”按钮来添加服务器凭据。这会打开一个模式对话框，在其中可以从下拉列表选择“凭据类型”。 
- 可以提供域/Windows（非域）/Linux（非域）/SQL Server 身份验证凭据。 [详细了解](add-server-credentials.md)如何提供凭据以及我们如何处理凭据。
- 对于每种类型的凭据，需要指定凭据的易记名称，添加 **用户名** 和 **密码**，然后单击“保存”。
- 如果选择域凭据，则还需要指定域的 FQDN。必须使用 FQDN 才能在该域的 Active Directory 中验证凭据的真实性。
- 检查用于发现已安装的应用程序、执行无代理依赖项分析或发现 SQL Server 实例和数据库的帐户的[所需权限](add-server-credentials.md#required-permissions)。
- 如果要一次添加多个凭据，请单击“添加更多”，以保存和添加更多凭据。
- 单击“保存”或“添加更多”时，设备将在域的 Active Directory 中验证域凭据的真实性。  这样可以避免在设备执行多次迭代以将凭据映射到相应服务器时出现任何帐户锁定情况。
- 可以在凭据表中查看所有域凭据的 **验证状态**。 只会验证域凭据。
- 如果验证失败，可以单击“失败”状态查看遇到的错误，并在修复问题后单击“重新验证凭据”以再次验证失败的域凭据。 


### <a name="start-discovery"></a>启动发现

1. 单击“开始发现”以启动 vCenter Server 发现。 成功启动发现后，可以针对源表中的 vCenter Server IP 地址/FQDN 检查发现状态。
1. 如果已提供服务器凭据，在 vCenter Server 发现完成后，会自动启动软件盘点（发现已安装的应用程序）。 软件盘点每 12 小时执行一次。
1. [软件盘点](how-to-discover-applications.md)将识别服务器上运行的 SQL Server 实例，设备将使用这些信息尝试通过 Windows 身份验证或设备上提供的 SQL Server 身份验证凭据连接到实例，并收集有关 SQL Server 数据库及其属性的数据。 SQL 发现每 24 小时执行一次。
1. 在软件盘点过程中，将针对服务器循环访问已添加的服务器凭据，并会对这些凭据进行验证，以便进行无代理依赖项分析。可以在门户中为服务器启用无代理依赖项分析。 只能选择验证成功的服务器来启用无代理依赖项分析。

> [!Note]
>Azure Migrate 将加密 Azure Migrate 设备与源 SQL Server 实例之间的通信（“加密连接”属性设置为 TRUE）。 这些连接是使用 [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate)（设置为 TRUE）加密的；传输层将使用 SSL 来加密通道，并绕过证书链来验证信任。 必须将设备服务器设置为 [**信任证书的根颁发机构**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。<br/>
如果服务器在启动时未预配有任何证书，SQL Server 将生成可用于加密登录数据包的自签名证书。 [**了解详细信息**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。

发现的工作原理如下：
- 大约 15 分钟后，已发现的服务器库存数据即会显示在门户中。
- 发现已安装的应用程序可能需要一段时间。 具体时间取决于发现的服务器数量。 如果有 500 个服务器，Azure Migrate 门户大约需要一小时才会显示发现的库存。
- 完成服务器发现后，可以在门户中对服务器启用无代理依赖项分析。
- 从启动发现起的 24 小时内，SQL Server 实例和数据库数据将开始出现在门户中。

## <a name="next-steps"></a>后续步骤

- [评估服务器](./tutorial-assess-vmware-azure-vm.md)以迁移到 Azure VM。
- [评估 SQL Server](./tutorial-assess-sql.md) 以迁移到 Azure SQL。
- [查看设备在发现期间收集的数据](migrate-appliance.md#collected-data---vmware)。