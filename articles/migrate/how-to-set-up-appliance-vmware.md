---
title: 为 VMware 设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备来评估和迁移 VMware Vm。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: bac82b2939e5b6a674c75be2cd330dd0fa4b8487
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035740"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>为 VMware Vm 设置设备

按照本文所述，使用 [Azure Migrate： Server 评估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具设置 Azure Migrate 设备进行评估，并使用 [Azure Migrate： server 迁移](migrate-services-overview.md#azure-migrate-server-migration-tool) 工具进行无代理迁移。

[Azure Migrate 设备](migrate-appliance.md)是一种轻型设备，由 Azure Migrate：服务器评估和服务器迁移，用于发现在 vCenter Server 中运行的服务器、将服务器配置和性能元数据发送到 Azure，以及使用无代理迁移来复制服务器。

可以使用几种方法来部署设备：

- 使用下载的 .OVA 模板在 vCenter Server 上创建服务器。 这是本文中所述的方法。
- 使用 PowerShell 安装程序脚本在现有服务器上设置设备。 如果无法使用 .OVA 模板，或者在 Azure 政府版中，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到 Azure Migrate：服务器评估，将其注册到 Azure Migrate 项目，并将设备配置为启动发现。

## <a name="deploy-with-ova"></a>使用 OVA 进行部署

若要使用 OVA 模板设置设备，请执行以下操作：
1. 提供设备名称，并在门户中生成 Azure Migrate 项目密钥。
1. 下载 OVA 模板文件，并将其导入 vCenter Server。 验证 OVA 是否安全。
1. 从 .OVA 文件创建设备 VM，并检查它是否可以连接到 Azure Migrate。
1. 第一次配置设备，并使用 Azure Migrate 项目密钥将其注册到项目。

### <a name="1-generate-the-azure-migrate-project-key"></a>1.生成 Azure Migrate 项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，选择“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”  。
3. 在 **1：生成 Azure Migrate 项目密钥** 中，为你将在 VMware 环境中为服务器发现设置的 Azure Migrate 设备提供名称。该名称的字母数字应为14个字符或更少。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 请不要在创建资源的过程中关闭 "发现" 页。
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


### <a name="3-create-the-appliance-server"></a>3. 创建设备服务器

导入已下载的文件，并在 VMware 环境中创建服务器

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。
2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在 " **名称** 和 **位置**" 中，指定服务器的友好名称。 选择将在其中托管服务器的清单对象。
5. 在 " **主机/群集**" 中，指定将在其上运行服务器的主机或群集。
6. 在 " **存储**" 中，指定服务器的存储目标。
7. 在“磁盘格式”中，指定磁盘类型和大小。
8. 在 " **网络映射**" 中，指定服务器将连接到的网络。 网络需要 internet 连接才能将元数据发送到 Azure Migrate。
9. 检查并确认设置，然后单击“完成”。


### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备服务器可以连接到 [公共](migrate-appliance.md#public-cloud-urls) 和 [政府](migrate-appliance.md#government-cloud-urls) 云的 Azure url。


### <a name="4-configure-the-appliance"></a>4.配置设备

首次设置设备。

> [!NOTE]
> 如果使用 [**PowerShell 脚本**](deploy-appliance-script.md) 而不是下载的 .ova 设置设备，则此过程中的前两个步骤不相关。

1. 在 vSphere 客户端控制台中，右键单击服务器，然后选择 " **打开控制台**"。
2. 提供设备的语言、时区和密码。
3. 在可连接到设备服务器的任意计算机上打开浏览器，并打开设备配置管理器的 URL： `https://appliance name or IP address: 44368` 。

   或者，您可以通过选择配置管理器的快捷方式，从设备服务器桌面打开配置管理器。
1. 接受许可条款，并阅读第三方信息。
1. 在配置管理器中 > **设置必备组件**，请执行以下操作：
   - **连接**：设备将检查服务器是否具有 internet 访问权限。 如果服务器使用代理：
     - 单击 " **设置代理** " 以在窗体 `http://ProxyIPAddress` 或侦听端口中指定代理地址 `http://ProxyFQDN` 。
     - 如果代理需要身份验证，请指定凭据。
     - 仅支持 HTTP 代理。
     - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击 " **查看设备服务** " 查看设备服务器上运行的服务的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

     Azure Migrate Server 迁移使用 VDDK 在迁移到 Azure 期间复制服务器。 
1. 如果需要，可以在设备配置期间随时重新运行先决条件，以检查设备是否满足所有先决条件。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="设备配置管理器上的面板1":::


## <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

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

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="设备 configuration manager 上的面板2":::

## <a name="start-continuous-discovery"></a>启动持续发现

### <a name="provide-vcenter-server-details"></a>提供 vCenter Server 详细信息

设备需要连接到 vCenter Server，才能发现服务器的配置和性能数据。

1. 在 **步骤1：提供 vCenter Server 凭据** 中，单击 " **添加凭据** " 为凭据指定友好名称，为设备将用于发现在 vCenter Server 上运行的服务器的 vCenter Server 帐户添加 **用户名** 和 **密码** 。
    - 你应该已设置具有所需权限的帐户，如上文所述。
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM），请参阅[此文](set-discovery-scope.md)中的说明，以限制 Azure Migrate 所使用的帐户。
1. 在 **步骤2：提供 vCenter Server 详细信息**，单击 " **添加发现源** " 以从下拉选择 "凭据的友好名称"，指定 vCenter Server 的 **IP 地址/FQDN** 。 可以保留“端口”的默认值 443，或指定 vCenter Server 侦听的自定义端口，然后单击“保存” 。
1. 单击 " **保存**" 后，设备将尝试通过提供的凭据验证与 vCenter Server 的连接，并在表中根据 vCenter Server IP 地址/FQDN 显示 **验证状态** 。
1. 在开始发现之前，可以随时重新验证与 vCenter Server 之间的连接。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="设备 configuration manager 上的面板3，用于 vCenter Server 详细信息":::

### <a name="provide-server-credentials"></a>提供服务器凭据

在 **步骤3：提供服务器凭据以执行软件清单、无代理依赖项分析和 SQL Server 实例和数据库的发现**，你可以选择提供多个服务器凭据，或者，如果你不想使用这些功能，则可以选择跳过此步骤并继续 vCenter Server 发现。 您可以在以后随时更改意向。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="设备上的面板3详细信息配置管理器":::

> [!Note]
> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。

如果要利用这些功能，可以通过执行以下步骤来提供服务器凭据。设备将尝试自动将凭据映射到服务器来执行发现功能。

- 可以通过单击 " **添加凭据** " 按钮来添加服务器凭据。这会打开一个模式，你可以在其中从下拉端选择 **凭据类型** 。
- 你可以提供域/Windows (非域) /Linux (非域) /SQL Server 身份验证凭据。 [了解](add-server-credentials.md) 有关如何提供凭据的详细信息以及如何处理凭据的详细信息。
- 对于每种类型的凭据，需要指定凭据的友好名称、添加 **用户名** 和 **密码** ，并单击 " **保存**"。
- 如果选择域凭据，则还需要指定域的 FQDN。需要 FQDN 来验证凭据的真实性和该域的 Active Directory。
- 查看帐户的 [所需权限](add-server-credentials.md#required-permissions) ，以发现已安装的应用程序、无代理依赖项分析或 SQL Server 实例和数据库发现。
- 如果要一次添加多个凭据，请单击“添加更多”，以保存和添加更多凭据。
- 当你单击 " **保存** " 或 " **添加更多**" 时，设备将验证域凭据是否具有域的 Active Directory 以获得其真实性。 这样做是为了避免在设备执行多个迭代时进行任何帐户锁定，以便将凭据映射到各个服务器。
- 你可以在凭据表中查看所有域凭据的 **验证状态** 。 仅会验证域凭据。
- 如果验证失败，你可以单击 " **失败** 状态" 以查看遇到的错误，并单击 "修复问题后重新验证 **凭据** " 以再次验证失败的域凭据。


### <a name="start-discovery"></a>启动发现

1. 单击 " **启动发现" 开始** vCenter Server 发现。 成功启动发现后，你可以根据源表中 vCenter Server 的 IP 地址/FQDN 来检查发现状态。
1. 如果您提供了服务器凭据，软件清单 (发现已安装的应用程序) 在 vCenter Server 的发现完成后将自动启动。 软件清单每12小时执行一次。
1. [软件清单](how-to-discover-applications.md) 标识服务器上运行的 SQL Server 实例，并使用这些信息，设备会尝试通过 Windows 身份验证或设备上提供 SQL Server 身份验证凭据连接到实例，并将数据收集到 SQL Server 数据库及其属性。 SQL 发现每24小时执行一次。
1. 在软件清单过程中，将根据服务器循环访问已添加的服务器凭据，并验证是否有无代理依赖项分析。可以从门户为服务器启用无代理依赖项分析。 只能选择成功进行验证的服务器来启用无代理依赖项分析。

发现的工作原理如下：
- 在门户中显示发现的服务器清单需要大约15分钟。
- 发现已安装的应用程序可能需要一段时间。 持续时间取决于发现的服务器的数量。 对于500服务器，发现的清单在 Azure Migrate 门户中出现大约1小时。
- 服务器发现完成后，可以从门户启用服务器上的无代理依赖项分析。
- 从发现启动开始，在24小时内，将开始在门户中显示 SQL Server 实例和数据库数据。

## <a name="next-steps"></a>后续步骤

查看 [VMware 评估](./tutorial-assess-vmware-azure-vm.md) 和 [无代理迁移](tutorial-migrate-vmware.md)教程。