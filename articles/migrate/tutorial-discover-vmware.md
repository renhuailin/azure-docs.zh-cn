---
title: 通过“Azure Migrate 发现和评估”发现 VMware 环境中运行的服务器
description: 了解如何通过使用“Azure Migrate 发现和评估”工具发现在 VMware 环境中运行的本地服务器、应用程序和依赖项。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 07/28/2021
ms.custom: mvc
ms.openlocfilehash: 3d3fc18ebceb18e7411a68c19e069a0db4889854
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797108"
---
# <a name="tutorial-discover-servers-running-in-a-vmware-environment-with-azure-migrate"></a>教程：使用 Azure Migrate 发现 VMware 环境中运行的服务器

在迁移到 Azure 的过程中，你将发现本地库存和工作负载。

本教程介绍如何使用一个轻型 Azure Migrate 设备通过“Azure Migrate：发现和评估”工具发现 VMware 环境中运行的服务器。 你要将该设备部署为 vCenter Server 实例中运行的服务器，以持续发现服务器及其性能元数据、服务器上运行的应用程序、服务器依赖项、ASP.NET Web 应用，以及 SQL Server 实例和数据库。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置 Azure 帐户。
> * 准备好用于发现的 VMware 环境。
> * 创建项目。
> * 设置 Azure Migrate 设备。
> * 启动持续发现。

> [!NOTE]
> 教程介绍了尝试方案的最快路径。 这些教程会在任何可行的情况下使用默认选项。  

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，请先检查是否已满足这些先决条件：

要求 | 详细信息
--- | ---
**vCenter Server/ESXi 主机** | 需要一台运行 vCenter Server 6.7、6.5、6.0 或 5.5 版本的服务器。<br /><br /> 服务器必须托管在运行 5.5 或更高版本的 ESXi 主机上。<br /><br /> 在 vCenter Server 上，允许 TCP 端口 443 上的入站连接，以便设备可以收集配置和性能元数据。<br /><br /> 默认情况下，设备通过端口 443 连接到 vCenter Server。 如果运行 vCenter Server 的服务器侦听其他端口，则可以在设备配置管理器上提供 vCenter Server 详细信息时修改端口。<br /><br /> 在 ESXi 主机上，请确保允许通过 TCP 端口 443 进行入站访问，以便能够在服务器上发现已安装应用程序和无代理依赖项分析。
**Azure Migrate 设备** | vCenter Server 必须具有以下要分配给托管 Azure Migrate 设备的服务器的资源：<br /><br /> - 32 GB RAM、8 个 vCPU 以及约 80 GB 的磁盘存储。<br /><br /> - 一台外部虚拟交换机，可在设备服务器上直接或通过代理进行 Internet 访问。
**服务器** | 所有 Windows 和 Linux OS 版本均受支持，可用于发现配置和性能元数据。 <br /><br /> 对于服务器上的应用程序发现，所有 Windows 和 Linux OS 版本均受支持。 查看[支持无代理依赖项分析的 OS 版本](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)。<br /><br /> 为了发现已安装的应用程序和进行无代理依赖性分析，必须在服务器上安装并运行 VMware 工具（版本 10.2.1 或更高版本）。 在 Windows 服务器上必须安装 PowerShell 2.0 或更高版本。<br /><br /> 若要发现 SQL Server 实例和数据库，请查看[支持的 SQL Server 和 Windows OS 版本](migrate-support-matrix-vmware.md#sql-server-instance-and-database-discovery-requirements)身份验证机制。<br /><br /> 若要发现在 IIS Web 服务器上运行的 ASP.NET Web 应用，请查看[支持的 Windows OS 和 IIS 版本](migrate-support-matrix-vmware.md#aspnet-web-apps-discovery-requirements)。

## <a name="prepare-an-azure-user-account"></a>准备 Azure 用户帐户

若要创建项目并注册 Azure Migrate 设备，必须拥有具有以下权限的 Azure 帐户：

- Azure 订阅的参与者或所有者权限。
- 用于注册 Azure Active Directory (Azure AD) 应用的权限。
- Azure 订阅上用于创建 Azure 密钥保管库实例的“所有者”或“参与者”以及“用户访问管理员”权限，在无代理服务器迁移期间使用。

如果你创建了免费的 Azure 帐户，则默认情况下，你是 Azure 订阅的所有者。 如果你不是订阅所有者，请让所有者分配权限。

若要设置 Azure 订阅的参与者或所有者权限：

1. 在 Azure 门户中搜索“订阅”。 在搜索结果中的“服务”下，选择“订阅” 。

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="显示如何在搜索框中搜索 Azure 订阅的屏幕截图。":::

1. 在“订阅”中，选择要在其中创建项目的订阅。
1. 在左侧菜单中，选择“访问控制(IAM)”。
1. 在“检查访问权限”下的“检查访问权限”选项卡上 ，搜索想要使用的用户帐户。
1. 在“添加角色分配”窗格中，选择“添加” 。

    :::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="显示如何搜索用户帐户以检查访问权限并添加角色分配的屏幕截图。":::
    
1. 在“添加角色分配”中，选择“参与者”或“所有者”角色，然后选择帐户。 选择“保存”。

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="显示添加角色分配页面以向帐户分配角色的屏幕截图。":::

若要向帐户授予注册 Azure AD 应用所需的权限：

1. 在 Azure 门户中，转到“Azure Active Directory” > “用户” > “用户设置”  。
1. 在“用户设置”中，验证 Azure AD 用户是否可以注册应用程序（默认情况下设置为“是”） 。

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="显示验证用户设置以注册应用的屏幕截图。":::

9. 如果“应用注册”设置为“否” ，请请求租户或全局管理员以分配所需的权限。 或者，租户或全局管理员可以将应用程序开发人员角色分配给帐户，以允许用户注册 Azure AD 应用。 [了解详细信息](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>准备 VMware

在 vCenter Server 上，检查帐户是否有权使用 VMware Open Virtualization Appliance (OVA) 虚拟机 (VM) 安装文件创建 VM。 使用 OVA 文件将 Azure Migrate 设备部署为 VMware VM 时，必须拥有这些权限。

Azure Migrate 必须具有 vCenter Server 只读帐户才能发现和评估在 VMware 环境中运行的服务器。 如果还想运行对安装的应用程序的发现和无代理依赖项分析，该帐户必须在 VMware 中为 VM 来宾操作启用权限。

### <a name="create-an-account-to-access-vcenter-server"></a>创建帐户以访问 vCenter Server

在 VMware vSphere Web 客户端中，设置一个只读帐户以用于 vCenter Server：

1. 在具有管理员权限的帐户的 vSphere Web 客户端中，在“主页”菜单上，选择“管理” 。
1. 在“单一登录”下选择“用户和组” 。
1. 在“用户”中，选择“新建用户” 。
1. 输入账户详细信息，然后选择“确定”。
1. 在“管理”下菜单中的“访问控制”下，选择“全局权限”  。
1. 选择用户帐户，然后选择“只读”以将角色分配给该帐户。 选择“确定”。
1. 若要开始发现已安装的应用程序和进行无代理依赖项分析，请在“访问控制”下的菜单中选择“角色” 。 在“角色”窗格下的“角色”位置，选择“只读”  。 在“权限”下，选择“来宾操作” 。 若要将权限传播到 vCenter Server 实例中的所有对象，请选中“传播到子级”复选框。

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="显示 v sphere Web 客户端以及如何创建新帐户并选择用户角色和特权的屏幕截图。":::

> [!NOTE]
> 可以通过限定 vCenter Server 帐户的范围，将发现范围限制为特定的 vCenter Server 数据中心、群集、主机、群集文件夹、主机文件夹或单个服务器。 了解如何[限定 vCenter Server 用户帐户的范围](set-discovery-scope.md)。

> [!NOTE]
> 如果 vCenter 资产通过链接模式连接到指定供发现的 vCenter 服务器，则 Azure Migrate 将不会发现这些资产。 应为希望发现的每个 vCenter 环境部署一台 Azure Migrate 设备。

### <a name="create-an-account-to-access-servers"></a>创建用于访问服务器的帐户

服务器上用户帐户必须具有所需的权限，才能启动对安装的应用程序的发现、无代理依赖项分析，以及对 Web 应用及 SQL Server 实例和数据库的发现。 你可以在设备配置管理器上提供用户帐户信息。 设备不会在服务器上安装代理。

* 对于 Windows 服务器和 Web 应用发现，请在服务器上创建拥有管理员权限的帐户（本地或域）。 若要发现 SQL Server 实例和数据库，该 Windows 或 SQL Server 帐户必须是 sysadmin 服务器角色的成员。 了解如何[向用户帐户分配所需的角色](/sql/relational-databases/security/authentication-access/server-level-roles)。
* 对于 Linux 服务器，请提供根用户帐户的详细信息，或者创建一个对 /bin/netstat 和 /bin/ls 文件具有 CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE 权限的帐户。

> [!NOTE]
> 可以在 Azure Migrate 设备配置管理器上添加多个服务器凭据，以启动对安装的应用程序的发现、无代理依赖项分析，以及对 Web 应用及 SQL Server 实例和数据库的发现。 可以添加多个域，Windows（非域）、Linux（非域）或 SQL Server 身份验证凭据。 了解如何[添加服务器凭据](add-server-credentials.md)。

## <a name="set-up-a-project"></a>设置项目

若要设置新项目：

1. 在“Azure 门户”中选择“所有服务”，然后搜索“Azure Migrate” 。
1. 在“服务”下选择“Azure Migrate”。 
1. 在“概述”中，根据迁移目标选择以下一种选项：“服务器、数据库和 Web 应用”、“SQL Server (仅)”或“浏览更多方案”   。
1. 选择“创建项目”。 
1. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
1. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。 查看[公有云支持的地理位置](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云支持的地理位置](migrate-support-matrix.md#supported-geographies-azure-government)。

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="显示如何为新建 Azure Migrate 项目添加项目详细信息的屏幕截图。":::

1. 选择“创建”。
1. 等待几分钟，让项目完成部署。 默认会将“Azure Migrate：发现和评估”工具添加到新项目。

> [!NOTE]
> 如果你已创建一个项目，可以使用该项目注册更多设备，以发现和评估更多的服务器。 了解如何[管理项目](create-manage-projects.md#find-a-project)。

## <a name="set-up-the-appliance"></a>设置设备

“Azure Migrate：发现和评估工具”使用轻型 Azure Migrate 设备。 设备完成服务器发现并将服务器配置和性能元数据发送到 Azure Migrate。 可通过部署一个 OVA 模板来设置设备，该模板可从项目下载。

> [!NOTE]
> 如果无法使用 OVA 模板设置设备，则可以在现有运行的 Windows Server 2016 服务器上使用 PowerShell 脚本进行设置。 了解如何[使用 PowerShell 设置 Azure Migrate 设备](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。 <br/>
> Azure 政府云不支持“使用 OVA 模板部署设备”的选项。 [详细了解](./deploy-appliance-script-government.md)如何为 Azure 政府云部署设备。

### <a name="deploy-by-using-an-ova-template"></a>使用 OVA 模板进行部署

若要使用 OVA 模板设置设备，需要完成以下步骤，本部分将更详细地介绍这些步骤：

1. 提供设备名称，并在门户中生成项目密钥。
1. 下载 OVA 模板文件，然后将其导入 vCenter Server。 验证 OVA 是否安全。
1. 从 OVA 文件中创建设备。 确认此设备可以连接到 Azure Migrate。
1. 首次配置设备。 
1. 使用项目密钥将设备注册到项目。

#### <a name="generate-the-project-key"></a>生成项目密钥

1. 在“迁移目标”中，选择“服务器、数据库和 Web 应用” > “Azure Migrate: 发现和评估” > “发现”   。
1. 在“发现服务器”中，选择“服务器是否虚已拟化？”  > “是，使用 VMware vSphere 虚拟机监控程序”。
1. 在“1: 生成项目密钥”中，为在 VMware 环境中发现服务器而设置的 Azure Migrate 设备提供名称。 该名称应是字母数字，且长度不得超过 14 个字符。
1. 若要开始创建所需的 Azure 资源，请选择“生成密钥”。 创建资源时，请勿关闭“发现”窗格。
1. 成功创建 Azure 资源后，会生成一个“项目密钥”。
1. 复制密钥。 配置设备时，将使用密钥完成设备的注册。

#### <a name="download-the-ova-template"></a>下载 OVA 模板

在“2: 下载 Azure Migrate 设备”中，选择 OVA 文件然后选择“下载”。

##### <a name="verify-security"></a>验证安全性

在部署 OVA 文件之前，请验证该文件是否安全：

1. 在下载文件的服务器上，使用“以管理员身份运行”选项打开命令提示符窗口。
1. 运行以下命令，生成 OVA 文件的哈希：
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    示例： `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. 验证最新的设备版本和哈希值：

    - 对于 Azure 公有云：
    
        **算法** | **下载** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - 对于 Azure 政府：
    
        **算法** | **下载** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

#### <a name="create-the-appliance-server"></a>创建设备服务器

导入下载的文件，然后在 VMware 环境中创建服务器：

1. 在 vSphere 客户端控制台中，选择“文件” > “部署 OVF 模板”。
1. 在“部署 OVF 模板向导”中，选择“源”，然后输入 OVA 文件的位置。
1. 在“名称”中，输入服务器的名称。 在“位置”中，选择要在其中托管该服务器的库存对象。
1. 在“主机/群集”中，选择要在其上运行该服务器的主机或群集。
1. 在“存储”中，选择该服务器的存储目标。
1. 在“磁盘格式”中，选择磁盘类型和大小。
1. 在“网络映射”中，选择该服务器要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure Migrate 发送元数据。
1. 检查并确认设置，然后选择“完成”。

#### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备服务器可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。

### <a name="configure-the-appliance"></a>配置设备

首次设置设备的步骤：

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 OVA 模板设置设备，则可以跳过前两个步骤。

1. 在 vSphere 客户端中，右键单击该服务器，然后选择“打开控制台”。
1. 选择或输入设备的语言、时区和密码。
1. 在可以连接到设备的任何一个计算机上打开一个浏览器。 然后打开设备配置管理器的 URL：`https://appliance name or IP address: 44368`。

     或者，可以通过选择配置管理器的快捷方式，从设备服务器桌面打开配置管理器。
1. 接受许可条款，并阅读第三方信息。
1. 在配置管理器中，选择“设置先决条件”，然后完成以下步骤：
    1. **连接**：设备将检查服务器是否可访问 Internet。 如果服务器使用代理：
        1. 选择“设置代理”以指定代理地址（以 `http://ProxyIPAddress` 或 `http://ProxyFQDN` 形式，其中“FQDN”指“完全限定的域名”）和侦听端口。
        1.  如果代理需要身份验证，请输入凭据。
        1. 如果已添加代理详细信息或已禁用代理或身份验证，请选择“保存”，再次触发连接并进行连接性检查。

            仅支持 HTTP 代理。
    1. 时间同步：检查设备上的时间是否与 Internet 时间同步，以便正常运行发现。
    1. **安装更新**：设备可确保安装最新的更新。 检查完成后，可以选择“查看设备服务”查看设备服务器上运行的服务的状态和版本。
    1. 安装 VDDK：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 如果未安装 VDDK，请从 VMware 下载 VDDK 6.7。 按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

        Azure Migrate 服务器迁移在迁移到 Azure 期间使用 VDDK 来复制服务器。 
1. 可以在设备配置期间随时“重新运行先决条件”，以检查设备是否满足所有先决条件：

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="此屏幕截图显示在设备配置管理器中设置先决条件。":::

#### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的项目密钥。 如果没有该密钥，请转到“Azure Migrate：发现和评估” > “发现” > 管理现有设备”  。 选择生成项目密钥时提供的设备名称，然后复制显示的密钥。
1. 必须使用设备代码向 Azure 进行身份验证。 选择“登录名”。 在“继续使用 Azure 登录”选择“复制代码并登录”来复制设备代码，并在新的浏览器标签页中打开 Azure 登录提示。请确保已禁用浏览器中的弹出窗口阻止程序以查看提示。

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="此屏幕截图显示复制设备代码和登录位置。":::

1. 在浏览器的新选项卡中，粘贴设备代码并使用 Azure 用户名和密码登录。 不支持使用 PIN 登录。

    如果在未登录的情况下意外关闭了登录选项卡，请刷新设备配置管理器的浏览器选项卡以显示设备代码和“复制代码和登录”按钮。
1. 成功登录后，返回到显示设备配置管理器的浏览器选项卡。 如果用于登录的 Azure 用户帐户对密钥生成期间创建的 Azure 资源具有所需的权限，则会开始设备注册。
1. 成功注册设备后，若要查看注册详细信息，请选择“查看详细信息”。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="“注册到 Azure Migrate”窗格的屏幕截图，显示设备已成功注册。":::

## <a name="start-continuous-discovery"></a>启动持续发现

完成设备配置管理器中的设置步骤，以准备和启动发现。

### <a name="provide-vcenter-server-details"></a>提供 vCenter Server 详细信息

设备必须连接到 vCenter Server 才能发现服务器的配置和性能数据：

1. 在“步骤 1: 提供 vCenter Server 凭据”中，选择“添加凭据”以输入凭据的名称。 添加 vCenter Server 帐户的用户名和密码，设备将使用该帐户发现 vCenter Server 上运行的服务器。
    - 你应已按照本文前面所述设置了一个拥有所需权限的帐户。
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、主机、群集文件夹、主机文件夹或单个服务器），请参阅[设置发现范围](set-discovery-scope.md)以限制 Azure Migrate 所用帐户的相关说明。
1. 在“步骤 2: 提供 vCenter Server 详细信息”中，选择“添加发现源”，从下拉列表中选择凭据的名称。 选择 vCenter Server 的 IP 地址或 FQDN。 可以将端口保留为默认值 (443)，或指定 vCenter Server 侦听的自定义端口。 选择“保存”。
1. 设备尝试使用凭据验证与运行 vCenter Server 的服务器之间的连接。 将显示凭据表中 vCenter Server IP 地址或 FQDN 的验证状态。
1. 在开始发现之前，可以随时“重新验证”与 vCenter Server 之间的连接。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="此屏幕截图显示在设备配置管理器中管理 vCenter Server 凭据和发现源。":::

### <a name="provide-server-credentials"></a>提供服务器凭据

在“步骤 3: 提供服务器凭据以在 VMware 环境中执行软件清单、无代理依赖项分析、SQL Server 实例与数据库的发现和 ASP.NET Web 应用的发现”中，可提供多个服务器凭据。 如果不想使用上述任何设备功能，可以跳过此步骤，开始执行 vCenter Server 发现。 随时可以更改此选项。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="此屏幕截图显示为软件盘存、依赖项分析和 SQL Server 发现提供凭据。":::

若要使用这些功能，可通过完成以下步骤提供服务器凭据。 设备会尝试自动将凭据映射到服务器以执行发现功能。

添加服务器凭据的步骤：

1. 选择“添加凭据”。 
1. 在下拉菜单中，选择“凭据类型”。
    
    可以提供域/、Windows（非域）/、Linux（非域）/ 和 SQL Server 身份验证凭据。 了解如何[提供凭据](add-server-credentials.md)以及我们如何处理凭据。
1. 对于每种类型的凭据，请输入：
    * 一个易记名称。
    * 一个用户名。
    * 一个密码。
    选择“保存”。

    如果选择使用域凭据，还需要输入域的 FQDN。 必须使用 FQDN 才能在该域的 Active Directory 实例中验证凭据的真实性。
1. 检查用于发现已安装的应用程序、执行无代理依赖项分析，以及发现 Web 应用及 SQL Server 实例和数据库的帐户的[所需权限](add-server-credentials.md#required-permissions)。
1. 若要一次添加多个凭据，请选择“添加更多”，以保存和添加更多凭据。
    选择“保存”或“添加更多”时，设备将使用域的 Active Directory 实例验证域凭据以进行身份验证。 每次添加后都会进行验证，以避免在设备循环访问以将凭据映射到相应服务器时帐户锁定。

检查域凭据的验证：

在配置管理器的凭据表中，请参阅域凭据的“验证状态”。 只验证域凭据。

如果验证失败，可以选择“失败”状态以查看验证错误。 解决此问题，然后选择“重新验证凭据”以重新尝试凭据验证。

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="此屏幕截图显示提供和验证多个凭据。":::

### <a name="start-discovery"></a>启动发现

若要启动 vCenter Server 发现，请选择“开始发现”。 成功启动发现后，可以通过查看源表中的 vCenter Server IP 地址或 FQDN 检查发现状态。

## <a name="how-discovery-works"></a>发现的工作原理

* 在 Azure 门户中，大约需要 15 分钟才能显示发现服务器的库存。
* 如果已提供服务器凭据，则在发现运行 vCenter Server 的服务器完成后，会自动启动软件盘存（发现已安装的应用程序）。 软件盘存每 12 小时发生一次。
* [软件盘存](how-to-discover-applications.md)会标识在服务器上运行的 SQL Server 实例。 设备利用该功能收集的信息，尝试通过 Windows 身份验证凭据或设备上提供的 SQL Server 身份验证凭据连接到 SQL Server 实例。 然后，它会收集有关 SQL Server 数据库及其属性的数据。 SQL Server 发现每 24 小时执行一次。
* 设备只能连接到与其建立了网络连接的 SQL Server 实例，而软件盘存本身无需网络连接。
* 对安装的应用程序的发现过程可能需要超过 15 分钟。 具体时间取决于发现的服务器数量。 如果有 500 个服务器，门户中的 Azure Migrate 项目大约需要一小时才会显示发现的库存。
* [软件清单](how-to-discover-applications.md)识别发现的服务器上存在的 Web 服务器角色。 如果发现服务器已启用 Web 服务器角色，Azure Migrate 将在服务器上执行 Web 应用发现。 Web 应用配置数据每 24 小时更新一次。
* 在软件盘存过程中，针对服务器循环访问已添加的服务器凭据，并对这些凭据进行验证，以便进行无代理依赖项分析。 完成服务器发现后，可以在门户中对服务器启用无代理依赖项分析。 只能选择验证成功的服务器来启用[无代理依赖项分析](how-to-create-group-machine-dependencies-agentless.md)。
* 在启动发现后的 24 小时内，ASP.NET Web 应用及 SQL Server 实例和数据库数据将开始在门户中显示。
* 默认情况下，Azure Migrate 使用最安全的方法连接到 SQL 实例，即 Azure Migrate 通过将 TrustServerCertificate 属性设置为 `true`，对 Azure Migrate 设备和源 SQL Server 实例之间的通信进行加密。 此外，传输层使用 SSL 加密通道并绕过证书链来验证信任。 因此，必须将设备服务器设置为信任证书的根颁发机构。 但是，可以通过在设备上选择“编辑 SQL Server 连接属性”来修改连接设置。[了解详细信息](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)以了解要选择的内容。

    :::image type="content" source="./media/tutorial-discover-vmware/sql-connection-properties.png" alt-text="显示如何编辑 SQL Server 连接属性的屏幕截图。":::

若要启动 vCenter Server 发现，请选择“开始发现”。 成功启动发现后，可以通过查看源表中的 vCenter Server IP 地址或 FQDN 检查发现状态。

### <a name="view-discovered-data"></a>查看发现的数据

1. 返回到 Azure Migrate 门户。
1. 单击下面屏幕截图中标记的“刷新”，查看发现的数据。
    :::image type="content" source="./media/tutorial-discover-vmware/discovery-assessment-tile.png" alt-text="屏幕截图显示了如何刷新发现和评估磁贴中的数据。":::

## <a name="next-steps"></a>后续步骤

- 了解如何[评估要迁移到 Azure VM 的服务器](./tutorial-assess-vmware-azure-vm.md)。
- 了解如何[评估要迁移到 Azure SQL 的运行 SQL Server 的服务器](./tutorial-assess-sql.md)。
- 了解如何[评估 Web 应用以迁移到 Azure 应用服务](./tutorial-assess-webapps.md)。
- 查看[Azure Migrate 设备在发现期间收集的数据](migrate-appliance.md#collected-data---vmware)。