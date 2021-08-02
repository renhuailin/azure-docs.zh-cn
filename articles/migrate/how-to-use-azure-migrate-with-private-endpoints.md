---
title: 使用结合专用终结点的 Azure Migrate
description: 使用 Azure Migrate 专用链接支持，通过专用链接进行发现、评估和迁移。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: 85202474e8fd3c4340357b99ae91ee7d25d79ebf
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747178"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>使用结合专用终结点的 Azure Migrate  

本文介绍如何使用 Azure Migrate，通过 [Azure 专用链接](../private-link/private-endpoint-overview.md)在专用网络上发现、评估和迁移服务器。

你可以使用 [Azure Migrate: 发现和评估](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)以及 [Azure Migrate: 服务器迁移](./migrate-services-overview.md#azure-migrate-server-migration-tool)工具，通过 Azure 专用链接，经由 ExpressRoute 专用对等互连或站点到站点的 VPN 连接以私密且安全的方式连接到 Azure Migrate 服务。

如果组织需要在不遍历公用网络的情况下访问 Azure Migrate 服务和其他 Azure 资源，则建议使用专用终结点连接方法。 通过专用链接，你可以使用现有 ExpressRoute 专用对等互连线路，满足更高的带宽或延迟要求。

## <a name="support-requirements"></a>支持要求

### <a name="required-permissions"></a>所需的权限

订阅的“参与者 + 用户访问管理员”或“所有者”权限 。

### <a name="supported-scenarios-and-tools"></a>支持的场景和工具

**部署** | **详细信息** | **工具**
--- | --- | ---
发现和评估 | 对在任何平台上运行的服务器执行无代理的大规模发现和评估，这些平台包括 [VMware vSphere](./tutorial-discover-vmware.md) 或 [Microsoft Hyper-V](./tutorial-discover-hyper-v.md) 等虚拟机监控程序平台、[AWS](./tutorial-discover-aws.md) 或 [GCP](./tutorial-discover-gcp.md) 等公有云，或甚至是[裸机计算机服务器](./tutorial-discover-physical.md)。 | Azure Migrate: 发现和评估  <br/>
**软件清单** | 发现在 VMware VM 上运行的应用、角色和功能。 | Azure Migrate: 发现和评估  
依赖项可视化 | 使用依赖项分析功能识别和了解各服务器之间的依赖项。 <br/> Azure Migrate 专用链接支持在本机支持[无代理依赖项可视化](./how-to-create-group-machine-dependencies-agentless.md)。 <br/>[基于代理的依赖项可视化](./how-to-create-group-machine-dependencies.md)需要 Internet 连接。 [了解如何](../azure-monitor/logs/private-link-security.md)将专用终结点用于基于代理的依赖项可视化。 | Azure Migrate: 发现和评估 |
**迁移** | 执行[无代理 Hyper-V 迁移](./tutorial-migrate-hyper-v.md)，或使用基于代理的方法迁移 [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理服务器](./tutorial-migrate-physical-virtual-machines.md)、[在 AWS 上运行的 VM](./tutorial-migrate-aws-virtual-machines.md)、[在 GCP 上运行的 VM](./tutorial-migrate-gcp-virtual-machines.md) 或在其他虚拟化提供程序上运行的 VM。 | Azure Migrate:服务器迁移

>[!Note]
>
> [无代理 VMware 迁移](./tutorial-migrate-vmware.md)需要通过 ExpressRoute Microsoft 对等互连访问或连接 Internet。 <br/> [了解如何](./replicate-using-expressroute.md)使用专用终结点通过 ExpressRoute 专用对等互连或站点到站点 (S2S) VPN 连接来执行复制。  <br/><br/>

#### <a name="other-integrated-tools"></a>其他集成工具

如果禁用公用网络访问，其他迁移工具可能无法将使用情况数据上传到 Azure Migrate 项目。 应将 Azure Migrate 项目配置为允许来自所有网络的流量，以接收来自其他 Microsoft 或外部[独立软件供应商 (ISV)](./migrate-services-overview.md#isv-integration) 产品/服务的数据。


若要为 Azure Migrate 项目启用公用网络访问，请登录 Azure 门户，导航到 Azure 门户上的“Azure Migrate 属性”页，选择“否” > “保存”。

![显示如何更改网络访问模式的关系图。](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>其他注意事项   

**注意事项** | **详细信息**
--- | ---
**定价** | 有关定价信息，请参阅 [Azure blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)和 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。  
**虚拟网络要求** | ExpressRoute/VPN 网关应位于选中的虚拟网络或与其相连接的虚拟网络中。 在虚拟网络中，你可能需要大约 15 个 IP 地址。  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>创建一个具有专用终结点连接的项目

使用[本文](./create-manage-projects.md#create-a-project-for-the-first-time)设置新的 Azure Migrate 项目。

> [!Note]
> 你不能将现有 Azure Migrate 项目的连接方法更改为专用终结点连接。

在“高级”配置部分中，提供以下详细信息，为 Azure Migrate 项目创建专用终结点。
1. 在“连接方法”中，选择“专用终结点” 。
2. 在“禁用公共终结点访问”中，保留默认设置“否” 。 如果禁用公用网络访问，一些集成工具可能无法将使用情况数据上传到 Azure Migrate 项目。 [了解详细信息。](#other-integrated-tools)
3. 在“虚拟网络订阅”中，选择专用终结点虚拟网络的订阅。
4. 在“虚拟网络”中，选择专用终结点的虚拟网络。 Azure Migrate 设备或其他需要连接到 Azure Migrate 项目的软件组件必须位于此网络上或已连接的虚拟网络上。
5. 在“子网”中，选择专用终结点的子网。

   ![创建项目](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

6. 选择“创建”  。 创建一个 Migrate 项目并为其附加一个专用终结点。 等待几分钟，让 Azure Migrate 项目部署完成。 在创建项目过程中，请勿关闭此页。

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>使用 Azure 专用链接发现和评估要迁移的服务器

### <a name="set-up-the-azure-migrate-appliance"></a>设置 Azure Migrate 设备

1. 在“发现计算机” > “计算机是否已虚拟化?”中，选择服务器类型 。
2. 在“生成 Azure Migrate 项目密钥”中，提供 Azure Migrate 设备的名称。
3. 选择“生成密钥”，创建所需的 Azure 资源。

    > [!Important]
    > 在创建资源期间，请不要关闭“发现计算机”页。  
    - 在此步骤，Azure Migrate 创建密钥保管库、存储帐户、恢复服务保管库（仅用于无代理 VMware 迁移）和一些内部资源，并向每种资源附加一个专用终结点。 专用终结点在创建项目期间选择的虚拟网络中创建。  
    - 创建专用终结点后，Azure Migrate 资源的 DNS CNAME 资源记录更新为子域中前缀为 privatelink 的别名。 默认情况下，Azure Migrate 还创建一个与每种资源类型的 privatelink 子域相对应的专用 DNS 区域，并为相关专用终结点插入 DNS A 记录。 这使 Azure Migrate 设备和在源网络中驻留的其他软件组件能够访问专用 IP 地址上的 Azure Migrate 资源终结点。  
    - Azure Migrate 还为 Migrate 项目启用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)，并授予托管标识可以安全访问存储帐户的权限。  

4. 成功生成密钥后，复制密钥的详细信息以配置和注册设备。   

#### <a name="download-the-appliance-installer-file"></a>下载设备安装程序文件  

“Azure Migrate：发现和评估”使用轻型 Azure Migrate 设备。 设备执行服务器发现并将服务器配置和性能元数据发送到 Azure Migrate。  

> [!Note]
> 具有专用终结点连接的 Azure Migrate 项目不支持使用模板（即 OVA，适用于 VMware 环境和 VHD Hyper-V 环境中的服务器）部署设备的选项。

设置设备：
  1. 从门户下载包含安装程序脚本的 zip 文件。
  2. 将压缩文件复制到将要托管该设备的服务器上。   
  3. 下载 zip 文件后，验证文件安全性
  4. 运行安装程序脚本以部署设备。

下面是各个方案的下载链接：

方案 | 下载链接 | 哈希值
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | CBF8927AF137A106E2A34AC4F77CFFCB1CD96873C592E1DF37BC5606254989EC
物理 | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 1CB967D92096EB48E4C3C809097F52C8341FC7CA7607CF840C529E7A21B1A21D
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 0A4FCC4D1500442C5EB35E4095EF781CB17E8ECFE8E4F8C859E65231E00BB154
VMware 横向扩展 | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 2F035D34E982EE507EAEC59148FDA8327A45D2A845B4A95475EC6D2469D72D28

#### <a name="verify-security"></a>验证安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。
2. 运行以下命令以生成 zip 文件的哈希

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公有云的示例用法：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  比较上表中的哈希值，以验证设备的最新版本。

请确保服务器满足所选方案（VMware/Hyper-V/物理等）的[硬件要求](./migrate-appliance.md)，并可连接到[所需 URL](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity)。


#### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。
2. 以管理员（提升）权限在计算机上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示：

    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1
    ```

5. 脚本会在成功运行后启动设备配置管理器，以便你可以配置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>配置设备并启动持续发现

在可以连接到设备服务器的任何计算机上打开一个浏览器，然后打开设备配置管理器的 URL：`https://appliance name or IP address: 44368`。 或者，可以通过选择配置管理器的快捷方式，从设备服务器桌面打开配置管理器。

#### <a name="set-up-prerequisites"></a>设置先决条件

1. 阅读第三方信息，并接受许可条款。    

2. 在配置管理器 >“设置先决条件”中执行以下操作：
   - 连接：设备会检查对所需 URL 的访问权限。 如果服务器使用代理：
     - 选择“设置代理”，指定代理地址 `http://ProxyIPAddress` 或 `http://ProxyFQDN` 以及侦听端口。
     - 如果代理需要身份验证，请指定凭据。 仅支持 HTTP 代理。
     - 可以添加应绕过代理服务器的 URL/IP 地址的列表。
     - 如果已更新代理服务器详细信息，或已添加 URL/IP 地址用于绕过代理，则选择“保存”以注册配置。

        > [!Note]
        > 如果在连接性检查过程中 aka.ms/* 链接出错，而你不希望设备通过 Internet 访问此 URL，则需要按照 [**此处**](./migrate-appliance.md#turn-off-auto-update)的步骤在设备上禁用自动更新服务。 禁用自动更新后，将跳过 aka.ms/* URL 连接性检查。

   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以选择“查看设备服务”以查看设备服务器上所运行服务的状态和版本。
        > [!Note]
        > 如果已选择在设备上禁用自动更新服务，则可以按照[此处](./migrate-appliance.md#manually-update-an-older-version)的步骤手动更新设备服务以获取最新版本的服务。
   - 安装 VDDK：（仅 VMware 设备需要）设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 如果未安装，则从 VMware 下载 VDDK 6.7，并按照“安装说明”中提供的信息，将下载的 zip 内容提取到设备的指定位置。

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>注册设备并启动持续发现

完成先决条件检查后，按步骤注册设备并启动针对各自场景的连续发现：
- [VMware VM](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Hyper-V VM](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [物理服务器](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [AWS VM](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [GCP VM](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)


>[!Note]
> 如果在注册设备期间或启动发现时出现 DNS 解析问题，请确保可从托管 Azure Migrate 设备的本地服务器访问在门户上执行“生成密钥”步骤期间创建的 Azure Migrate 资源。 [详细了解如何验证网络连接性](./troubleshoot-network-connectivity.md)。

### <a name="assess-your-servers-for-migration-to-azure"></a>评估服务器以迁移到 Azure
完成发现后，使用“Azure Migrate: 发现和评估”工具评估服务器（[VMware VM](./tutorial-assess-vmware-azure-vm.md)、[Hyper-V VM](./tutorial-assess-hyper-v.md)、[物理服务器](./tutorial-assess-vmware-azure-vm.md)、[AWS VM](./tutorial-assess-aws.md) 和 [GCP VM](./tutorial-assess-gcp.md)）以迁移到 Azure VM 或 Azure VMware 解决方案 (AVS)。

还可使用导入的逗号分隔值 (CSV) 文件，通过“Azure Migrate: 发现和评估”工具[评估本地计算机](./tutorial-discover-import.md#prepare-the-csv)。   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>使用 Azure 专用链接将服务器迁移到 Azure

以下部分介绍通过 ExpressRoute 专用对等互连或 VPN 连接，使用结合[专用终结点](../private-link/private-endpoint-overview.md)的 Azure Migrate 进行迁移所需的步骤。  

本文介绍了基于代理的复制的概念证明部署路径，以使用 Azure 专用终结点迁移 [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理服务器](./tutorial-migrate-physical-virtual-machines.md)、[在 AWS 上运行的 VM](./tutorial-migrate-aws-virtual-machines.md)、[在 GCP 上运行的 VM](./tutorial-migrate-gcp-virtual-machines.md) 或在其他虚拟化提供程序上运行的 VM。 可使用类似的方法通过专用链接执行[无代理 Hyper-V 迁移](./tutorial-migrate-hyper-v.md)。

>[!Note]
>[无代理 VMware 迁移](./tutorial-assess-physical.md)需要通过 ExpressRoute Microsoft 对等互连访问或连接 Internet。

### <a name="set-up-a-replication-appliance-for-migration"></a>设置复制设备以进行迁移

下图介绍了使用“Azure Migrate: 服务器迁移”工具，通过专用终结点进行的基于代理的复制工作流。  

![复制体系结构](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

该工具使用复制设备将服务器复制到 Azure。 请参阅本文，了解如何[为复制设备准备和设置计算机](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance)。

设置复制设备后，使用以下说明创建执行迁移所需的资源。

1. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“未虚拟化/其他”。  
2. 在“目标区域”中，选择并确认要向其迁移计算机的 Azure 区域。
3. 选择“创建资源”，创建所需的 Azure 资源。 在创建资源期间，请勿关闭页面。   
    - 这会在后台创建一个恢复服务保管库，并为该保管库启用一个托管标识。 恢复服务保管库是一个实体，它包含服务器的复制信息并用于触发复制操作。  
    - 如果 Azure Migrate 项目具有专用终结点连接，则会为恢复服务保管库创建专用终结点。 这将向专用终结点添加五个完全限定的专用名称 (FQDN)，每个名称对应一种链接到恢复服务保管库的微服务。   
    - 这五个域名的格式为： <br/> {Vault-ID}-asr-pod01-{type}-.{target-geo-code}.privatelink.siterecovery.windowsazure.com  
    - 默认情况下，Azure Migrate 自动创建一个专用 DNS 区域，并为恢复服务保管库微服务添加 DNS A 记录。 专用 DNS 区域可链接到专用终结点虚拟网络，并可让本地复制设备将完全限定的域名解析为其专用 IP 地址。

4. 在注册复制设备之前，请确保可从托管复制设备的计算机访问保管库的专用链接 FQDN。 [详细了解如何验证网络连接性。](./troubleshoot-network-connectivity.md)

5. 验证连接性后，下载设备安装和密钥文件，运行安装过程，并将设备注册到 Azure Migrate。 请在[此处](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)查看详细步骤。 设置复制设备后，按照这些说明在要迁移到的计算机上[安装移动服务](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service)。

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>使用 Azure 专用链接将服务器复制到 Azure

按照[以下步骤](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)选择要复制的服务器。  

在“复制” > “目标设置” > “缓存/复制存储帐户”中，使用下拉菜单选择要通过专用链接复制的存储帐户  。  

如果 Azure Migrate 项目具有专用终结点连接，则必须[授予恢复服务保管库托管标识](#grant-access-permissions-to-the-recovery-services-vault)访问 Azure Migrate 所需的存储帐户的权限。   

此外，若要启用通过专用链接的复制，请[为存储帐户创建专用终结点。](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>向恢复服务保管库授予访问权限

必须向恢复服务保管库授予相应权限，才能对缓存/复制存储帐户进行经过身份验证的访问。

若要识别 Azure Migrate 创建的恢复服务保管库，并授予所需权限，请执行以下步骤：

识别恢复服务保管库和托管标识对象 ID

你可在“Azure Migrate: 服务器迁移”的属性页上找到恢复服务保管库的详细信息。

1. 转到“Azure Migrate 中心”，选择“Azure Migrate: 服务器迁移”磁贴上的“概述” 。

    ![Azure Migrate 中心上的概述页](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. 在左窗格上，选择“属性”。 记下恢复服务保管库名称和托管标识 ID。 保管库的“连接类型”为“专用终结点”，“复制类型”为“其他”。 向保管库提供访问权限时，需要此信息。

    ![“Azure Migrate: 服务器迁移”属性页](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_访问存储帐户的权限_**

 必须向保管库的托管标识授予复制所需的存储帐户的以下角色权限。  在这种情况下，必须提前创建存储帐户。

>[!Note]
> 要使用专用链接将 Hyper-V VM 迁移到 Azure，则必须同时授予对复制存储帐户和缓存存储帐户的访问权限。

资源管理器的角色权限根据存储帐户的类型而有所不同。

|存储帐户类型 | 角色权限|
|--- | ---|
|标准类型 | [参与者](../role-based-access-control/built-in-roles.md#contributor)<br>[存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|高级类型 | [参与者](../role-based-access-control/built-in-roles.md#contributor)<br>[存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. 转到选择要进行复制的复制/缓存存储帐户。 在左侧窗格中选择“访问控制(IAM)”。

1. 在“添加角色分配”部分，选择“添加” ：

   ![添加角色分配](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. 在“添加角色分配”页的“角色”字段中，从上述权限列表中选择相应的角色 。 输入之前记下的保管库的名称，然后选择“保存”。

    ![提供基于角色访问权限](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. 除了这些权限之外，你还必须允许 Microsoft 受信任服务的访问。 如果网络访问限制为选中的网络，则在“网络”选项卡的“例外”部分中选择“允许受信任的 Microsoft 服务访问此存储帐户”  。

![允许受信任的 Microsoft 服务访问存储帐户](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>创建存储帐户的专用终结点（可选）

若要使用结合专用对等互连的 ExpressRoute 进行复制，请为缓存/复制存储帐户（目标子资源：blob）[创建一个专用终结点](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)。

>[!Note]
>
> - 只能在常规用途 v2 (GPv2) 存储帐户上创建专用终结点。 有关定价信息，请参阅 [Azure 页 Blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)和 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)

在与 Azure Migrate 项目专用终结点相同的虚拟网络或与此网络相连接的其他虚拟网络中创建存储帐户的专用终结点。

选择“是”，然后与专用 DNS 区域集成。 专用 DNS 区域有助于通过专用链接将虚拟网络的连接路由到存储帐户。 选择“是”，自动将 DNS 区域链接到虚拟网络，并添加 DNS 记录，用于新创建的 IP 和完全限定的域名的解析。 详细了解[专用 DNS 区域](../dns/private-dns-overview.md)。

如果创建专用终结点的用户也是存储帐户所有者，则会自动批准创建专用终结点。 否则，存储帐户的所有者必须批准专用终结点才能使用。 要批准或拒绝请求的专用终结点连接，请转到存储帐户页中“网络”下的“专用终结点连接” 。

在继续操作之前，查看专用终结点连接状态的状态。

![专用终结点的批准状态](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

创建专用终结点后，使用“复制” > “目标设置” > “缓存存储帐户”中的下拉菜单，选择要通过专用链接进行复制的存储帐户  。  

请确保本地复制设备与其专用终结点上的存储帐户有网络连接。 [详细了解如何验证网络连接性。](./troubleshoot-network-connectivity.md)

>[!Note]
>
> - 若要在复制存储帐户的类型为“高级”的情况下将 Hyper-V VM 迁移到 Azure，则必须为缓存存储帐户选择另一个类型为“标准”的存储帐户 。 在这种情况下，必须为复制和缓存存储帐户创建专用终结点。  

接下来，按照这些说明[查看和启动复制](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)并[执行迁移](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration)。


## <a name="next-steps"></a>后续步骤
- [完成迁移过程](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration)，并查看[迁移后的最佳做法](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices)。
