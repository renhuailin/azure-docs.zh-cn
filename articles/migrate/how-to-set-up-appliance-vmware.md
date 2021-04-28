---
title: 为 VMware 设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备，以便在 VMware 环境中评估和迁移服务器。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 64be28838abb5d5021f0a8cefc0eed2c2516498b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865224"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>为 VMware 环境中的服务器设置设备

按照本文的要求设置 Azure Migrate 设备，以便使用 [Azure Migrate: 发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)工具进行评估，使用 [Azure Migrate: 服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具进行无代理迁移。

[Azure Migrate 设备](migrate-appliance.md)是“Azure Migrate: 发现和评估”以及“Azure Migrate: 服务器迁移”使用的轻型设备，用于发现 vCenter Server 中运行的服务器，将服务器配置和性能元数据发送到 Azure，以及使用无代理迁移来复制服务器。

可以使用几种方法来部署设备：

- 使用下载的 OVA 模板在 vCenter Server 上创建服务器。 这是本文所述的方法。
- 使用 PowerShell 安装程序脚本在现有服务器上设置设备。 如果无法使用 OVA 模板，或者使用的是 Azure 政府，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到“Azure Migrate: 发现和评估”，然后将其注册到项目中，并配置设备以启动发现。

## <a name="deploy-with-ova"></a>使用 OVA 进行部署

若要使用 OVA 模板设置设备，请执行以下操作：
1. 提供设备名称，在门户中生成项目密钥。
1. 下载 OVA 模板文件，并将其导入 vCenter Server。 验证 OVA 是否安全。
1. 基于 OVA 文件创建设备 VM，并检查它是否可以连接到 Azure Migrate。
1. 完成设备的首次配置，并使用项目密钥将其注册到项目。

### <a name="1-generate-the-project-key"></a>1.生成项目密钥

1. 在“迁移目标” > “服务器” > “Azure Migrate: 发现和评估”中，选择“发现”。
2. 在“发现服务器” > “服务器是否已虚拟化?”中，选择“是，使用 VMware vSphere 虚拟机监控程序”。
3. 在“1: 生成项目密钥”中，为你要设置的 Azure Migrate 设备（用于发现 VMware 环境中的服务器）提供一个名称。该名称应该是不超过 14 个字符的字母数字。
1. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，请不要关闭“发现”页。
1. 成功创建 Azure 资源后，会生成一个项目密钥。
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
3. 在可以连接到设备服务器的任何服务器上打开一个浏览器，然后打开设备配置管理器的 URL：`https://appliance name or IP address: 44368`。

   或者，可以通过选择配置管理器的快捷方式，从设备服务器桌面打开配置管理器。
1. 接受许可条款，并阅读第三方信息。
1. 在配置管理器 >“设置先决条件”中执行以下操作：
   - **连接**：设备将检查服务器是否可访问 Internet。 如果服务器使用代理：
     - 单击“设置代理”以指定代理地址（格式为 `http://ProxyIPAddress` 或 `http://ProxyFQDN`）和侦听端口。
     - 如果代理需要身份验证，请指定凭据。
     - 仅支持 HTTP 代理。
     - 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击“查看设备服务”查看设备服务器上运行的服务的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

     Azure Migrate 服务器迁移在迁移到 Azure 期间使用 VDDK 来复制服务器。 
1. 如果需要，可以在设备配置期间随时重新运行先决条件，以检查设备是否满足所有先决条件。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="设备配置管理器上的面板 1":::


## <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的项目密钥。 如果没有密钥，请转到“发现和评估”>“发现”>“管理现有设备”，选择在生成密钥时提供的设备名称，然后复制相应的密钥。
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
    - 如果要将发现范围限定于特定的 VMware 对象（vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个服务器），请参阅[此文](set-discovery-scope.md)中的说明，以限制 Azure Migrate 所使用的帐户。
1. 在“步骤 2: 提供 vCenter Server 详细信息”中单击“添加发现源”，从下拉列表中选择凭据的易记名称，并指定 vCenter Server 的“IP 地址/FQDN”。   可以保留“端口”的默认值 443，或指定 vCenter Server 侦听的自定义端口，然后单击“保存” 。
1. 单击“保存”后，设备将尝试通过提供的凭据验证与 vCenter Server 的连接，并根据 vCenter Server IP 地址/FQDN 在表中显示“验证状态” 。
1. 在开始发现之前，可以随时重新验证与 vCenter Server 之间的连接。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="设备配置管理器上的面板 3，用于显示 vCenter Server 详细信息":::

### <a name="provide-server-credentials"></a>提供服务器凭据

在“步骤 3: 提供服务器凭据以执行软件盘点、无代理依赖项分析以及 SQL Server 实例和数据库的发现”中，可以选择提供多个服务器凭据，或者，如果你不想要利用这些功能，可以选择跳过该步骤并继续执行 vCenter Server 发现。 以后你随时可以改变想法。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="设备配置管理器上的面板 3，用于显示服务器详细信息":::


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

发现的工作原理如下：
- 大约 15 分钟后，已发现的服务器库存数据即会显示在门户中。
- 发现已安装的应用程序可能需要一段时间。 具体时间取决于发现的服务器数量。 如果有 500 个服务器，Azure Migrate 门户大约需要一小时才会显示发现的库存。
- 完成服务器发现后，可以在门户中对服务器启用无代理依赖项分析。
- 从启动发现起的 24 小时内，SQL Server 实例和数据库数据将开始出现在门户中。

## <a name="next-steps"></a>后续步骤

查看有关 [VMware 评估](./tutorial-assess-vmware-azure-vm.md)和[无代理迁移](tutorial-migrate-vmware.md)的教程。