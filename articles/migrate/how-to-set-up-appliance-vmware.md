---
title: 设置 Azure Migrate 设备用于在 VMware 环境中评估服务器
description: 了解如何设置 Azure Migrate 设备，以便在 VMware 环境中评估和迁移服务器。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: b2bdd5ee189d4fe350171d37e51d0f88b3e3ac20
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747193"
---
# <a name="set-up-an-appliance-for-servers-in-a-vmware-environment"></a>为 VMware 环境中的服务器设置设备

本文介绍如何使用 [Azure Migrate: 发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)工具设置 Azure Migrate 设备以供评估。

[Azure Migrate 设备](migrate-appliance.md)是“Azure Migrate: 发现和评估”工具使用的一种轻型设备，可发现在 vCenter Server 中运行的服务器，并将服务器配置和性能元数据发送到 Azure。

## <a name="set-up-the-appliance"></a>设置设备

可以使用几种方法来部署 Azure Migration 设备：

- 使用已下载的 OVA 模板在 vCenter Server VM 上创建服务器。 本文对此方法进行了说明。
- 使用 PowerShell 安装程序脚本在现有服务器上设置设备。 如果你无法使用 OVA 模板，或者你使用的是 Azure 政府，则应[运行 PowerShell 脚本](deploy-appliance-script.md)。

创建设备后，请检查设备是否可以连接到“Azure Migrate: 发现和评估”，然后将设备注册到项目中，并配置设备以启动发现。

### <a name="deploy-by-using-an-ova-template"></a>使用 OVA 模板进行部署

若要使用 OVA 模板设置设备，需要完成以下步骤，本部分将更详细地介绍这些步骤：

1. 提供设备名称，并在门户中生成项目密钥。
1. 下载 OVA 模板文件，然后将其导入 vCenter Server。 验证 OVA 是否安全。
1. 从 OVA 文件中创建设备。 确认此设备可以连接到 Azure Migrate。
1. 首次配置设备。 
1. 使用项目密钥将设备注册到项目。

#### <a name="generate-the-project-key"></a>生成项目密钥

1. 在“迁移目标”中，选择“服务器” > “Azure Migrate: 发现和评估” > “发现”。
1. 在“发现服务器”中，选择“服务器是否虚已拟化?” > “是，使用 VMware vSphere 虚拟机监控程序”。
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

1. 验证 Azure 公有云的最新设备版本和哈希值：
    
    **算法** | **下载** | **SHA256**
    --- | --- | ---
    VMware (11.9 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

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

<a name="4-configure-the-appliance"></a>

### <a name="configure-the-appliance"></a>配置设备

首次设置设备的步骤：

> [!NOTE]
> 如果使用 [PowerShell 脚本](deploy-appliance-script.md)而不是下载的 OVA 模板设置设备，则可以跳过前两个步骤。

1. 在 vSphere 客户端中，右键单击该服务器，然后选择“打开控制台”。
1. 选择或输入设备的语言、时区和密码。
1. 在可以连接到设备服务器的任何一台服务器上打开一个浏览器。 然后打开设备配置管理器的 URL：`https://appliance name or IP address: 44368`。

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

1. 粘贴从门户复制的项目密钥。 如果没有该密钥，请转到“发现和评估” > “发现” > 管理现有设备”。 选择生成项目密钥时提供的设备名称，然后复制显示的密钥。
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
1. 在“步骤 3: 提供服务器凭据以执行软件清单、无代理依赖项分析、SQL Server 实例与数据库的发现和 ASP.NET Web 应用的发现”中，查看帐户的[所需权限](add-server-credentials.md#required-permissions)。
1. 若要一次添加多个凭据，请选择“添加更多”，以保存和添加更多凭据。
    选择“保存”或“添加更多”时，设备将使用域的 Active Directory 实例验证域凭据以进行身份验证。 每次添加后都会进行验证，以避免在设备循环访问以将凭据映射到相应服务器时帐户锁定。

检查域凭据的验证：

在配置管理器的凭据表中，请参阅域凭据的“验证状态”。 只验证域凭据。

如果验证失败，可以选择“失败”状态以查看验证错误。 解决此问题，然后选择“重新验证凭据”以重新尝试凭据验证。

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="此屏幕截图显示提供和验证多个凭据。":::

### <a name="start-discovery"></a>启动发现

要启动 vCenter Server 发现，在“步骤 3: 提供服务器凭据以在 VMware 环境中执行软件清单、无代理依赖项分析、SQL Server 实例与数据库的发现和 ASP.NET Web 应用的发现”中，选择“启动发现”。 成功启动发现后，可以通过查看源表中的 vCenter Server IP 地址或 FQDN 检查发现状态。

## <a name="how-discovery-works"></a>发现的工作原理

* 在 Azure 门户中，大约需要 15 分钟才能显示发现服务器的库存。
* 如果已提供服务器凭据，则在发现运行 vCenter Server 的服务器完成后，会自动启动软件盘存（发现已安装的应用程序）。 软件盘存每 12 小时发生一次。
* [软件盘存](how-to-discover-applications.md)会标识在服务器上运行的 SQL Server 实例。 设备利用该功能收集的信息，尝试通过 Windows 身份验证凭据或设备上提供的 SQL Server 身份验证凭据连接到 SQL Server 实例。 然后，它会收集有关 SQL Server 数据库及其属性的数据。 SQL Server 发现每 24 小时执行一次。
* [软件清单](how-to-discover-applications.md)标识服务器上的 Web 服务器角色。 设备利用该功能收集的信息，尝试通过设备上提供的 Windows 身份验证凭据连接到 IIS Web 服务器。 然后，它收集有关 Web 应用的数据。 Web 应用发现每 24 小时执行一次。
* 对安装的应用程序的发现过程可能需要超过 15 分钟。 具体时间取决于发现的服务器数量。 如果有 500 个服务器，门户中的 Azure Migrate 项目大约需要一小时才会显示发现的库存。
* 在软件盘存过程中，针对服务器循环访问已添加的服务器凭据，并对这些凭据进行验证，以便进行无代理依赖项分析。 完成服务器发现后，可以在门户中对服务器启用无代理依赖项分析。 只能选择验证成功的服务器来启用无代理依赖项分析。
* 在启动发现后的 24 小时内，SQL Server 实例及数据库数据和 Web 应用数据将开始在门户中显示。

## <a name="next-steps"></a>后续步骤

学习 [VMware 评估教程](./tutorial-assess-vmware-azure-vm.md)和[无代理迁移教程](tutorial-migrate-vmware.md)。
