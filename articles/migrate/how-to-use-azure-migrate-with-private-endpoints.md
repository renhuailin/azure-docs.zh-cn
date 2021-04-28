---
title: 使用结合专用终结点的 Azure Migrate
description: 使用 Azure Migrate 专用链接支持，通过专用链接进行发现、评估和迁移。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: 97d4f0a387b75c9b23f64992a8ef39bc0bad17f0
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715167"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>使用结合专用终结点的 Azure Migrate  

本文介绍如何使用 Azure Migrate，通过 [Azure 专用链接](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)在专用网络上发现、评估和迁移服务器。 

你可以使用 [Azure Migrate: 发现和评估](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool)和 [Azure Migrate: 服务器迁移](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)工具，通过 Azure 专用链接，经由 ExpressRoute 专用对等互连或站点到站点的 VPN 连接以私密且安全的方式连接到 Azure Migrate 服务。 

如果组织需要在不遍历公用网络的情况下访问 Azure Migrate 服务和其他 Azure 资源，则建议使用专用终结点连接方法。 你还可以通过专用链接支持，使用现有的 ExpressRoute 专用对等互连线路来满足更高的带宽或延迟要求。 

## <a name="support-requirements"></a>支持要求 

### <a name="required-permissions"></a>所需的权限

订阅的“参与者 + 用户访问管理员”或“所有者”权限 。 

### <a name="supported-scenarios-and-tools"></a>支持的场景和工具

**部署** | **详细信息** | **工具** 
--- | --- | ---
发现和评估 | 对在任何平台上运行的服务器执行无代理的大规模发现和评估，这些平台包括 [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) 或 [Microsoft Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v) 等虚拟机监控程序平台、[AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) 或 [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp) 等公有云，或甚至是[裸机计算机服务器](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical)。 | Azure Migrate: 发现和评估  <br/> 
**软件清单** | 发现在 VMware VM 上运行的应用、角色和功能。 | Azure Migrate: 发现和评估  
依赖项可视化 | 使用依赖项分析功能识别和了解各服务器之间的依赖项。 <br/> Azure Migrate 专用链接支持在本机支持[无代理依赖项可视化](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless)。 <br/>[基于代理的依赖项可视化](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)需要 Internet 连接。 [了解如何](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security)将专用终结点用于基于代理的依赖项可视化。 | Azure Migrate: 发现和评估 |
**迁移** | 执行[无代理 Hyper-V 迁移](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v)，或使用基于代理的方法迁移 [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理服务器](./tutorial-migrate-physical-virtual-machines.md)、[在 AWS 上运行的 VM](./tutorial-migrate-aws-virtual-machines.md)、[在 GCP 上运行的 VM](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines) 或在其他虚拟化提供程序上运行的 VM。 | Azure Migrate:服务器迁移
 
>[!Note]
>
> [无代理 VMware 迁移](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware)需要通过 ExperessRoute Microsoft 对等互连访问或连接 Internet。 <br/> [了解如何](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute)使用专用终结点通过 ExpressRoute 专用对等互连或站点到站点 (S2S) VPN 连接来执行复制。  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>其他集成工具

如果禁用公用网络访问，一些集成工具可能无法将使用情况数据上传到 Azure Migrate 项目。 应将 Azure Migrate 项目配置为允许来自所有网络的流量，以接收来自其他 Microsoft 或外部[独立软件供应商 (ISV)](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) 产品/服务的数据。 


要为 Azure Migrate 项目启用公用网络访问，请转到 Azure 门户上的 Azure Migrate“属性页”，选择“否”，然后选择“保存”  。

![显示如何更改网络访问模式的关系图。](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>其他注意事项   

**注意事项** | **详细信息**
--- | --- 
**定价** | 有关定价信息，请参阅 [Azure blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)和 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。  
**虚拟网络要求** | ExpressRoute/VPN 网关应位于选中的虚拟网络或与其相连接的虚拟网络中。 在虚拟网络中，你可能需要大约 15 个 IP 地址。  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>创建一个具有专用终结点连接的项目

使用[本文](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time)设置新的 Azure Migrate 项目。 

> [!Note]
> 你不能将现有 Azure Migrate 项目的连接方法更改为专用终结点连接。

在“高级”配置部分中，提供以下详细信息，为 Azure Migrate 项目创建专用终结点。
- 在“连接方法”中，选择“专用终结点” 。 
- 在“禁用公共终结点访问”中，保留默认设置“否” 。 如果禁用公用网络访问，一些集成工具可能无法将使用情况数据上传到 Azure Migrate 项目。 [了解详细信息。](#other-integrated-tools)
- 在“虚拟网络订阅”中，选择专用终结点虚拟网络的订阅。 
- 在“虚拟网络”中，选择专用终结点的虚拟网络。 Azure Migrate 设备或其他需要连接到 Azure Migrate 项目的软件组件必须位于此网络上或已连接的虚拟网络上。
- 在“子网”中，选择专用终结点的子网。 

选择“创建”。 等待几分钟，让 Azure Migrate 项目部署完成。 在创建项目过程中，请勿关闭此页。

![创建项目](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
这将创建一个 Migrate 项目并向其附加一个专用终结点。 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>使用 Azure 专用链接发现和评估要迁移的服务器 

### <a name="set-up-the-azure-migrate-appliance"></a>设置 Azure Migrate 设备 

1. 在“发现计算机” > “计算机是否已虚拟化?”中，选择服务器类型 。
2. 在“生成 Azure Migrate 项目密钥”中，提供 Azure Migrate 设备的名称。 
3. 选择“生成密钥”，创建所需的 Azure 资源。 

    > [!Important]
    > 在创建资源期间，请不要关闭“发现计算机”页。  
    - 在此步骤，Azure Migrate 创建密钥保管库、存储帐户、恢复服务保管库（仅用于无代理 VMware 迁移）和一些内部资源，并向每种资源附加一个专用终结点。 专用终结点在创建项目期间选择的虚拟网络中创建。  
    - 创建专用终结点后，Azure Migrate 资源的 DNS CNAME 资源记录更新为前缀为 privatelink 的子域中的别名。 默认情况下，Azure Migrate 还创建一个与每种资源类型的 privatelink 子域相对应的专用 DNS 区域，并为相关专用终结点插入 DNS A 记录。 这使 Azure Migrate 设备和在源网络中驻留的其他软件组件能够访问专用 IP 地址上的 Azure Migrate 资源终结点。  
    - Azure Migrate 还为 Migrate 项目启用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，并授予托管标识可以安全访问存储帐户的权限。  

4. 成功生成密钥后，复制密钥的详细信息以配置和注册设备。   

#### <a name="download-the-appliance-installer-file"></a>下载设备安装程序文件

“Azure Migrate：发现和评估”使用轻型 Azure Migrate 设备。 设备执行服务器发现并将服务器配置和性能元数据发送到 Azure Migrate。

要设置设备，请从门户下载包含安装程序脚本的压缩文件。 将压缩文件复制到将要托管该设备的服务器上。

请确保服务器满足所选场景（VMware、Hyper-V 或物理等）的[硬件要求](https://docs.microsoft.com/azure/migrate/migrate-appliance)，并可以连接到所需的 Azure URL - [公有云](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity)和[政府云](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity)。

下载压缩文件后，请验证文件安全性，并运行部署设备的安装程序脚本。

#### <a name="verify-file-security"></a>验证文件安全性

在部署压缩文件之前检查其安全性。

1. 在下载文件的服务器上，打开管理员命令窗口。 
2. 运行以下命令以生成 zip 文件的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 示例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-PrivateEndPoint.zip SHA256```
3. 验证 Azure 公有云的最新设备版本和脚本：

    **算法** | **下载** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72
    Hyper-V (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72
    物理或其他 (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

#### <a name="run-the-script"></a>运行脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。 
2. 以管理员（提升）权限在计算机上启动 PowerShell。
3. 将 PowerShell 目录更改为包含从下载的压缩文件中提取的内容的文件夹。
4. 运行脚本 AzureMigrateInstaller.ps1，如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1```
   
5. 脚本会在成功运行后启动设备配置管理器，以便你可以配置设备。 如果遇见任何问题，请查看位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志。

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>配置设备并启动持续发现

在可以连接到设备服务器的任何计算机上打开一个浏览器，然后打开设备配置管理器的 URL：`https://appliance name or IP address: 44368`。 或者，可以通过选择配置管理器的快捷方式，从设备服务器桌面打开配置管理器。

#### <a name="set-up-prerequisites"></a>设置先决条件

1. 阅读第三方信息，并接受许可条款。    
 
2. 在配置管理器 >“设置先决条件”中执行以下操作：
   - 连接：设备会检查对所需 URL 的访问权限。 如果服务器使用代理：
     - 选择“设置代理”，指定代理地址 `http://ProxyIPAddress` 或 `http://ProxyFQDN` 以及侦听端口。
     - 如果代理需要身份验证，请指定凭据。 仅支持 HTTP 代理。
     - 如果需要，可以添加应绕过代理服务器的 URL/IP 地址的列表。 如果要使用 ExpressRoute 专用对等互连，请确保绕过这些 [URL](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)。
     - 如果已更新代理服务器详细信息，或已添加 URL/IP 地址用于绕过代理，则需要选择“保存”以注册配置。
     
        > [!Note]
        > 如果在连接性检查的过程中遇到了 aka.ms/* 链接错误，并且不希望设备通过 Internet 访问此 URL，则需要按照 [**此处**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update)的步骤在设备上禁用自动更新服务。 禁用自动更新后，将跳过 aka.ms/* URL 连接性检查。 

   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以选择“查看设备服务”查看设备服务器上运行的服务的状态和版本。
        > [!Note]
        > 如果已选择在设备上禁用自动更新服务，则可以按照[此处](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version)的步骤手动更新设备服务以获取最新版本的服务。
   - 安装 VDDK：（仅 VMware 设备需要）设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>注册设备并启动持续发现

完成先决条件检查后，按照以下步骤注册设备并启动针对各自场景的连续发现：[VMware VM](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate)、[Hyper-V VM](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate)、[物理服务器](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate)、[AWS VM](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate)、[GCP VM](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate)。


>[!Note]
> 如果在注册设备期间或启动发现时遇到了 DNS 解析问题，请确保可从托管 Azure Migrate 设备的本地服务器访问在门户上执行“生成密钥”步骤期间创建的 Azure Migrate 资源。 [详细了解如何验证网络连接性](#troubleshoot-network-connectivity)。

### <a name="assess-your-servers-for-migration-to-azure"></a>评估服务器以迁移到 Azure
完成发现后，使用“Azure Migrate: 发现和评估”工具评估服务器（[VMware VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)、[Hyper-V VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v)、[物理服务器](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)、[AWS VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws) 和 [GCP VM](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)）以迁移到 Azure VM 或 Azure VMware 解决方案 (AVS)。 

还可使用导入的逗号分隔值 (CSV) 文件，通过“Azure Migrate: 发现和评估”工具[评估本地计算机](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv)。   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>使用 Azure 专用链接将服务器迁移到 Azure

以下部分介绍通过 ExpressRoute 专用对等互连或 VPN 连接，使用结合[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)的 Azure Migrate 进行迁移所需的步骤。  

本文介绍了基于代理的复制的概念证明部署路径，以使用 Azure 专用终结点迁移 [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理服务器](./tutorial-migrate-physical-virtual-machines.md)、[在 AWS 上运行的 VM](./tutorial-migrate-aws-virtual-machines.md)、[在 GCP 上运行的 VM](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines) 或在其他虚拟化提供程序上运行的 VM。 可使用类似的方法通过专用链接执行[无代理 Hyper-V 迁移](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v)。

>[!Note]
>[无代理 VMware 迁移](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical)需要通过 ExperessRoute Microsoft 对等互连访问或连接 Internet。 

### <a name="set-up-a-replication-appliance-for-migration"></a>设置复制设备以进行迁移 

下图介绍了使用“Azure Migrate: 服务器迁移”工具，通过专用终结点进行的基于代理的复制工作流。  

![复制体系结构](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

该工具使用复制设备将服务器复制到 Azure。 使用本文[为复制设备准备和设置计算机](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)。

设置复制设备后，使用以下说明创建执行迁移所需的资源。 

1. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“未虚拟化/其他”。  
2. 在“目标区域”中，选择并确认要向其迁移计算机的 Azure 区域。
3. 选择“创建资源”，创建所需的 Azure 资源。 在创建资源期间，请勿关闭页面。   
    - 这会在后台创建一个恢复服务保管库，并为该保管库启用一个托管标识。 恢复服务保管库是一个实体，它包含服务器的复制信息并用于触发复制操作。  
    - 如果 Azure Migrate 项目具有专用终结点连接，则会为恢复服务保管库创建专用终结点。 这将向专用终结点添加五个完全限定的专用名称 (FQDN)，每个名称对应一种链接到恢复服务保管库的微服务。   
    - 这五个域名的格式为： <br/> {Vault-ID}-asr-pod01-{type}-.{target-geo-code}.privatelink.siterecovery.windowsazure.com  
    - 默认情况下，Azure Migrate 自动创建一个专用 DNS 区域，并为恢复服务保管库微服务添加 DNS A 记录。 这个专用 DNS 区域随后链接到专用终结点虚拟网络。 这允许本地复制设备将完全限定的域名解析为其专用 IP 地址。

4. 在注册复制设备之前，请确保可从托管复制设备的计算机访问保管库的专用链接 FQDN。 [详细了解如何验证网络连接性。](#troubleshoot-network-connectivity) 

5. 验证连接性后，下载设备安装和密钥文件，运行安装过程，并将设备注册到 Azure Migrate。 请在[此处](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance)查看详细步骤。 设置复制设备后，按照这些说明在要迁移到的计算机上[安装移动服务](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service)。 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>使用 Azure 专用链接将服务器复制到 Azure 

现在，按照[这些步骤](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines)选择要复制的服务器。  

在“复制” > “目标设置” > “缓存/复制存储帐户”中，使用下拉菜单选择要通过专用链接复制的存储帐户  。  

如果 Azure Migrate 项目具有专用终结点连接，则必须[授予恢复服务保管库托管标识](#grant-access-permissions-to-the-recovery-services-vault)访问 Azure Migrate 所需的存储帐户的权限。   

此外，若要启用通过专用链接的复制，请[为存储帐户创建专用终结点。](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>向恢复服务保管库授予访问权限

恢复服务保管库托管标识需要对缓存/复制存储帐户进行经过身份验证的访问的权限。 

使用以下指南识别 Azure Migrate 创建的恢复服务保管库，并授予所需权限。 

识别恢复服务保管库和托管标识对象 ID

你可在“Azure Migrate: 服务器迁移”的属性页上找到恢复服务保管库的详细信息。 

1. 转到“Azure Migrate 中心”，选择“Azure Migrate: 服务器迁移”磁贴上的“概述” 。

    ![Azure Migrate 中心上的概述页](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. 在左窗格上，选择“属性”。 记下恢复服务保管库名称和托管标识 ID。 保管库的“连接类型”为“专用终结点”，“复制类型”为“其他”。 向保管库提供访问权限时，需要此信息。
      
    ![“Azure Migrate: 服务器迁移”属性页](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

授予访问存储帐户所需的权限

 必须向保管库的托管标识授予复制所需的存储帐户的以下角色权限。  在这种情况下，必须提前创建存储帐户。

>[!Note]
> 要使用专用链接将 Hyper-V VM 迁移到 Azure，则必须同时授予对复制存储帐户和缓存存储帐户的访问权限。 

角色权限根据存储帐户的类型而有所不同。

- 基于资源管理器的存储帐户（标准类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)和
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 基于资源管理器的存储帐户（高级类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)和
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. 转到选择要进行复制的复制/缓存存储帐户。 在左侧窗格中选择“访问控制(IAM)”。 

1. 在“添加角色分配”部分，选择“添加” ：

   ![添加角色分配](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. 在“添加角色分配”页的“角色”字段中，从上述权限列表中选择相应的角色 。 输入之前记下的保管库的名称，然后选择“保存”。

    ![提供基于角色访问权限](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. 除了这些权限之外，你还必须允许 Microsoft 受信任服务的访问。 如果网络访问限制为选中的网络，则在“网络”选项卡的“例外”部分中选择“允许受信任的 Microsoft 服务访问此存储帐户”  。 

![允许受信任的 Microsoft 服务访问存储帐户](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>创建存储帐户的专用终结点（可选）

若要使用结合专用对等互连的 ExpressRoute 进行复制，请为缓存/复制存储帐户（目标子资源：blob）[创建一个专用终结点](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint)。 

>[!Note]
>
> - 只能在常规用途 v2 (GPv2) 存储帐户上创建专用终结点。 有关定价信息，请参阅 [Azure 页 Blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)和 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)

存储帐户的专用终结点应在与 Azure Migrate 项目专用终结点相同的虚拟网络或与此网络相连接的其他虚拟网络中创建。 

选择“是”，然后与专用 DNS 区域集成。 专用 DNS 区域有助于通过专用链接将虚拟网络的连接路由到存储帐户。 选择“是”，自动将 DNS 区域链接到虚拟网络，并添加 DNS 记录，用于新创建的 IP 和完全限定的域名的解析。 详细了解[专用 DNS 区域](https://docs.microsoft.com/azure/dns/private-dns-overview)。

如果创建专用终结点的用户也是存储帐户的所有者，则将自动批准专用终结点。 否则，存储帐户的所有者必须批准专用终结点才能使用。 要批准或拒绝请求的专用终结点连接，请转到存储帐户页中“网络”下的“专用终结点连接” 。

在继续操作之前，查看专用终结点连接状态的状态。 

![专用终结点的批准状态](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

创建专用终结点后，使用“复制” > “目标设置” > “缓存存储帐户”中的下拉菜单，选择要通过专用链接进行复制的存储帐户  。  

请确保本地复制设备与其专用终结点上的存储帐户有网络连接。 [详细了解如何验证网络连接性。](#troubleshoot-network-connectivity)

>[!Note]
>
> - 若要在复制存储帐户的类型为“高级”的情况下将 Hyper-V VM 迁移到 Azure，则必须为缓存存储帐户选择另一个类型为“标准”的存储帐户 。 在这种情况下，必须为复制和缓存存储帐户创建专用终结点。  

接下来，按照这些说明[查看和启动复制](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines)并[执行迁移](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration)。  

## <a name="troubleshoot-network-connectivity"></a>排查网络连接问题 

### <a name="validate-private-endpoints-configuration"></a>验证专用终结点的配置 

请确保专用终结点的状态为“已批准”。  

1. 转到“Azure Migrate: 发现和评估”以及“Azure Migrate: 服务器迁移”的属性页。
2. 属性页包含 Azure Migrate 自动创建的专用终结点和专用链接 FQDN 的列表。  

3. 选择要诊断的专用终结点。  
    1. 验证连接状态是否为“已批准”。
    2. 如果连接处于挂起状态，则需要使其获得批准。
    3. 可能还要导航到专用终结点资源，查看虚拟网络是否与 Migrate 项目专用终结点虚拟网络相匹配。 

    ![查看专用终结点连接](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>验证 DNS 解析 

本地设备（或复制提供程序）将使用其完全限定的专用链接域名 (FQDN) 访问 Azure Migrate 资源。 可能需要其他 DNS 设置，以便从源环境解析专用终结点的专用 IP 地址。 [通过本文](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder)了解有助于排查任何网络连接问题的 DNS 配置场景。  

要验证专用链接的连接，请从托管 Migrate 设备的本地服务器执行 Azure Migrate 资源终结点（专用链接资源 FQDN）的 DNS 解析，并确保其解析为专用 IP 地址。 专用终结点的详细信息和专用链接资源 FQDN 的信息在“发现和评估”以及“服务器迁移”属性页中提供。 选择“下载 DNS 设置”查看列表。   

 ![Azure Migrate: 发现和评估的属性](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: 服务器迁移的属性](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

存储帐户专用链接 FQDN 的 DNS 解析的说明性示例。  

- 输入 nslookup<storage-account-name>.blob.core.windows.net。  将 <storage-account-name> 替换为用于 Azure Migrate 的存储帐户的名称。  

    你将收到如下消息：  

   ![DNS 解析示例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- 将为存储帐户返回专用 IP 地址 10.1.0.5。 此地址属于专用终结点虚拟网络子网。   

可使用类似方法验证其他 Azure Migrate 项目的 DNS 解析。   

如果 DNS 解析不正确，请执行以下步骤：  

- 如果使用自定义 DNS，请查看自定义 DNS 设置，并验证 DNS 配置是否正确。 有关指南，请参阅[专用终结点概述：DNS 配置](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)。
- 如果使用 Azure 提供的 DNS 服务器，请参阅以下部分进一步排除故障。  

> [!Tip]
> 通过使用专用链接资源 FQDN 及其相关专用 IP 地址来编辑本地设备上的 DNS 主机文件，以此手动更新源环境的 DNS 记录。 此选项仅建议用于测试。 <br/>  


### <a name="validate-the-private-dns-zone"></a>验证专用 DNS 区域   
如果 DNS 解析未按上一部分所述方式工作，则专用 DNS 区域可能出现问题。  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>确认所需的专用 DNS 区域资源存在  
默认情况下，Azure Migrate 还创建与每种资源类型的 privatelink 子域相对应的专用 DNS 区域。 专用 DNS 区域将在与专用终结点资源组相同的 Azure 资源组中创建。 Azure 资源组应包含采用以下格式的专用 DNS 区域资源：
- privatelink.vaultcore.azure.net - 密钥保管库 
- privatelink.blob.core.windows.net - 存储帐户
- privatelink.siterecovery.windowsazure.com - 恢复服务保管库（用于 Hyper-V 和基于代理的复制）
- privatelink.prod.migration.windowsazure.com - Migrate 项目、评估项目和发现站点。   

专用 DNS 区域将由 Azure Migrate 自动创建（用户选择的缓存/复制存储帐户除外）。 可通过导航到专用终结点页并选择 DNS 配置，找到链接的专用 DNS 区域。 应该会在专用 DNS 集成部分下看到专用 DNS 区域。 

![DNS 配置屏幕截图](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

如果 DNS 区域不存在（如下所示），请[创建新的专用 DNS 区域资源。](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![创建专用 DNS 区域](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>确认专用 DNS 区域是否已链接到虚拟网络  
专用 DNS 区域应链接到虚拟网络，该虚拟网络包含 DNS 查询用于解析资源终结点的专用 IP 地址的专用终结点。 如果专用 DNS 区域未链接到正确的虚拟网络，则从该虚拟网络进行的任何 DNS 解析都会忽略专用 DNS 区域。   

在 Azure 门户中导航到专用 DNS 区域资源，然后从左侧菜单中选择虚拟网络链接。 应会看到已链接的虚拟网络。

![查看虚拟网络链接](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

此时会显示一个链接列表，每个链接都有一个订阅中的虚拟网络的名称。 包含专用终结点资源的虚拟网络必须在此处列出。 否则，[根据文本](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)将专用 DNS 区域链接到虚拟网络。    

专用 DNS 区域链接到虚拟网络后，源自虚拟网络的 DNS 请求将查找专用 DNS 区域中的 DNS 记录。 对于为在其中创建了专用终结点的虚拟网络进行正确的地址解析，此操作是必需的。   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>确认专用 DNS 区域是否包含正确的 A 记录 

转到要进行故障排除的专用 DNS 区域。 概述页显示该专用 DNS 区域的所有 DNS 记录。 验证是否存在该资源的 DNS A 记录。 A 记录的值（IP 地址）必须为资源的专用 IP 地址。 如果发现 A 记录的 IP 地址错误，则必须删除错误的 IP 地址，然后添加新的 IP 地址。 建议删除整个 A 记录并添加一个新的记录，然后在本地源设备上执行 DNS 刷新。   

专用 DNS 区域中的存储帐户 DNS A 记录的说明性示例：

![DNS 记录](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

专用 DNS 区域中的恢复服务保管库微服务 DNS A 记录的说明性示例： 

![恢复服务保管库的 DNS 记录](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> 删除或修改 A 记录时，计算机仍可能会解析为旧 IP 地址，因为 TTL（生存时间）的值可能尚未过期。  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>可能影响专用链接连接性的其他因素  

这是一个可在高级或复杂场景中找到的非详尽项目列表： 

- 防火墙设置，可以是连接到虚拟网络的 Azure 防火墙，也可以是在设备计算机中部署的自定义防火墙解决方案。  
- 网络对等互连，这可能会对使用哪些 DNS 服务器以及如何路由流量造成影响。  
- 自定义网关 (NAT) 解决方案，可能会影响流量（包括来自 DNS 查询的流量）的路由方式。

有关详细信息，请参阅[专用终结点连接问题的排除指南。](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>后续步骤 
- [完成迁移过程](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration)，并查看[迁移后的最佳做法](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)。