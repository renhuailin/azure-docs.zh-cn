---
title: 适用于 Windows 的 Azure 上的 SAP BusinessObjects BI 平台部署 | Microsoft Docs
description: 在适用于 Windows 的 Azure 上部署和配置 SAP BusinessObjects BI 平台
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: 0eff97ac5aeecf712674bca247bb6305e550f482
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108773694"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Azure 上适用于 Windows 的 SAP BusinessObjects BI 平台部署指南

本文将介绍在 Azure 上部署适用于 Windows 的 SAP BusinessObjects BI 平台的策略。 在此示例中，配置了两个以高级 SSD 托管磁盘作为其安装目录的虚拟机。 Azure SQL 数据库（PaaS 产品/服务）和 Azure 高级文件（SMB 协议），前者用于中央管理服务 (CMS) 和审核数据库，后者作为文件存储在两个虚拟机之间进行共享。 默认 Tomcat Java Web 应用程序和 BI 平台应用程序一起安装在这两个虚拟机上。 为了对用户请求进行负载均衡，我们使用了 Azure 应用程序网关，该网关具有本机 TLS/SSL 卸载功能。

这种体系结构适用于小型部署或非生产环境。 对于生产或大规模部署，应为 Web 应用程序提供单独的主机，并提供多个 BOBI 应用程序主机，使服务器能够处理更多信息。

![适用于 Windows 的 Azure 上的 SAP BOBI 部署](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

在此示例中，使用了以下产品版本和文件系统布局

- SAP BusinessObjects 平台 4.3 SP01 补丁 1
- Windows Server 2019
- Azure SQL 数据库（版本：12.0.2000.8）
- Microsoft ODBC 驱动程序 - msodbcsql.msi（版本：13.1）

| 文件系统        | 说明                                                                                                               | 大小(GB)             | 必需访问权限  | 存储                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | 用于安装 SAP BOBI 实例、默认 Tomcat Web 应用和数据库驱动程序的文件系统（如有必要） | SAP 大小调整准则 | 本地管理权限 | 托管高级磁盘 - SSD |
| \\\azusbobi.file.core.windows.net\frsinput  | 装载目录适用于将用作输入文件存储目录的所有 BOBI 主机上的共享文件 | 业务需求         | 本地管理权限 | Azure NetApp 文件         |
| \\\azusbobi.file.core.windows.net\frsoutput | 装载目录适用于将用作输出文件存储目录的所有 BOBI 主机上的共享文件 | 业务需求         | 本地管理权限 | Azure NetApp 文件         |

## <a name="deploy-windows-virtual-machine-via-azure-portal"></a>通过 Azure 门户部署 Windows 虚拟机

在本部分中，我们将使用 Windows 操作系统 (OS) 映像为 SAP BOBI 平台创建两个虚拟机 (VM)。 用于创建虚拟机的概括性步骤如下所示：

1. 创建[资源组](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. 创建[虚拟网络](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)。

   - 请勿在 SAP BI 平台部署中为所有 Azure 服务使用单个子网。 基于 SAP BI 平台体系结构，可能需要创建多个子网。 在此部署中，我们将创建两个子网：BI 应用程序子网和应用程序网关子网。
   - 遵循 SAP 说明 [2276646](https://launchpad.support.sap.com/#/notes/2276646)，确定跨不同组件进行 SAP BusinessObjects BI 平台通信的端口。
   - 通过端口 1433 进行的 Azure SQL 数据库通信。 因此，应允许 SAP BOBI 应用服务器通过端口 1433 的出站流量。
   - 在 Azure 中，应用程序网关需要位于单独的子网中。 有关详细信息，请参阅 [Azure 应用程序网关](../../../application-gateway/configuration-overview.md)一文。
   - 如果使用 Azure NetApp 文件而不是 Azure 文件存储进行文件存储，则需要为 Azure NetApp 文件创建一个单独的子网。 有关详细信息，请查看 [Azure NetApp 文件网络规划准则](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)一文。

3. 创建可用性集。

   - 若要为多实例部署中的每个层实现冗余，请将每个层的虚拟机置于可用性集中。 请确保根据你的体系结构分离每个层的可用性集。

4. 创建虚拟机 1 (azuswinboap1)。

   - 可以使用自定义映像，也可以从 Azure 市场中选择一个映像。 请根据你的需求，参阅[为 SAP 部署来自 Azure 市场的 VM](deployment-guide.md) 或[使用自定义映像为 SAP 部署 VM](deployment-guide.md)。

5. 创建虚拟机 2 (azuswinboap2)。
6. 添加一个高级 SSD 盘。 它将用作 SAP BOBI 安装目录。

## <a name="provision-azure-premium-files"></a>预配 Azure 高级文件

在继续设置 Azure 文件存储之前，请先熟悉 [Azure 文件存储](../../../storage/files/storage-files-introduction.md)文档。

Azure 文件存储提供在基于硬盘（基于 HDD）的硬件上托管的标准文件共享，以及在基于固态磁盘（基于 SSD）的硬件上托管的高级文件共享。 对于 SAP BusinessObjects 文件存储，建议使用 Azure 高级文件。

Azure 高级文件共享在区域子集中提供本地和区域冗余。 若要确定高级文件共享目前是否可在你的区域中使用，请参阅 Azure 的[产品的上市区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)页。 有关支持 ZRS 的区域的信息，请参阅 [Azure 存储冗余](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>部署 Azure 文件存储帐户和 NFS 共享

Azure 文件共享将部署到存储帐户。存储帐户是代表存储共享池的顶级对象。 此存储池可用于部署多个文件共享。 Azure 支持多种类型的存储帐户，适用于客户可能遇见的不同存储场景，但对于 SAP BusinessObjects 文件存储，需要创建 FileStorage 存储帐户。 它允许你在基于固态磁盘 (SSD) 的高级硬件上部署 Azure 文件共享。

> [!NOTE]
> FileStorage 帐户只能用于存储 Azure 文件共享；其他任何存储资源（Blob 容器、队列、表等）都不能部署在 FileStorage 帐户中。

可通过部署在 SAP BOBI 平台的同一虚拟网络中的[专用终结点](../../../storage/files/storage-files-networking-endpoints.md)访问存储帐户。 通过此设置，形成 SAP 系统的流量不会离开虚拟网络安全边界。 SAP 系统通常包含敏感且关键的数据，并且对于许多客户来说，保持在虚拟网络的边界内是重要的安全考虑因素。

如果需要从不同的虚拟网络访问存储帐户，则可以使用 [Azure VNET 对等互连](../../../virtual-network/virtual-network-peering-overview.md)。

#### <a name="azure-files-storage-account"></a>Azure 文件存储帐户

1. 要通过 Azure 门户创建存储帐户，请转到“创建资源” > “存储” > “存储帐户”  。

2. 在“基本信息”选项卡上，填写所有创建存储帐户的必填字段。

   a. 选择“订阅”、“资源组”和“区域”  。

   b. 输入“存储帐户名称”（例如 azusbobi） 。 此名称必须全局唯一，但可以提供任何想要的名称。

   c. 选择“高级”作为性能层，然后选择“FileStorage”作为帐户类型 。

   d. 对于“复制”标签，请选择冗余级别。 选择“本地冗余存储(LRS)”。

   对于高级 FileStorage，本地冗余存储 (LRS) 和区域冗余存储 (ZRS) 是唯一可用的选项。 因此，根据部署策略（可用性集或可用性区域），请选择合适的冗余级别。 有关详细信息，请参阅 [Azure 存储冗余](../../../storage/common/storage-redundancy.md)。

   e. 选择“下一步”。

3. 在“网络”选项卡上，选择[专用终结点](../../../storage/files/storage-files-networking-endpoints.md)作为连接方法。 有关详细信息，请参阅 [Azure 文件存储的网络注意事项](../../../storage/files/storage-files-networking-overview.md)。

   a. 选择专用终结点部分中的“添加”。

   b. 选择“订阅”、“资源组”和“位置”  。

   c. 输入专用终结点的名称（例如 azusbobi-pe）。

   d. 在“存储子资源”中选择“文件” 。

   e. 在“网络”部分中，选择将部署 SAP BusinessObjects BI 应用程序的“虚拟网络”和“子网”  。

   f. 接受“默认值(是)”，以便与专用 DNS 区域集成 。

   g. 从下拉列表中选择“专用 DNS 区域”。

   h. 选择“确定”，返回到“创建存储帐户”中的“网络”选项卡。

4. 在“数据保护”选项卡中，为存储帐户中的 Azure 文件共享配置软删除策略。 默认情况下，软删除功能处于禁用状态。 若要了解有关软删除的详细信息，请参阅[如何防止意外删除 Azure 文件共享](../../../storage/files/storage-files-prevent-file-share-deletion.md)。

5. 在“高级”选项卡上，查看不同的安全选项。

   “需要安全传输”字段指示存储帐户是否需要对与存储帐户的通信进行传输中加密。 如果需要 SMB 2.1 支持，则必须禁用此字段。 对于 SAP BusinessObjects BI 平台，请将其保留为“默认值(禁用)”。

6. 继续执行并创建存储帐户。

有关如何创建存储帐户的详细信息，请参阅“[创建 FileStorage 存储帐户](../../../storage/files/storage-how-to-create-file-share.md)”。

#### <a name="create-azure-file-shares"></a>创建 Azure 文件共享

下一步是在存储帐户中创建 Azure 文件存储。 Azure 文件存储将预配模型用于高级文件共享。 在预配业务模型中，可以主动向 Azure 文件存储服务指定存储需求，而不是根据使用的资源进行计费。 要了解有关此模型的详细信息，请参阅[预配模型](../../../storage/files/understanding-billing.md#provisioned-model)。 在此示例中，我们为 SAP BOBI 文件存储创建两个 Azure 文件存储，分别是 frsinput (256 GB) 和 frsoutput (256 GB)。

导航到存储帐户“azusbobi” > “文件共享” 

1. 选择“新建文件共享”。
2. 输入文件共享的名称（例如 frsinput 或 frsouput）
3. 在预配容量中插入所需的文件共享大小（例如 256 GB）
4. 选择“SMB”作为“协议” 。
5. 选择“创建”  。

## <a name="configure-data-disk-on-windows-virtual-machine"></a>在 Windows 虚拟机上配置数据磁盘

本部分中的步骤使用以下前缀：

**[A]** ：该步骤适用于所有主机

### <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

SAP BusinessObjects BI 应用程序需要一个可以在上面安装二进制文件的分区。 可以在操作系统 (C: ) 上安装 SAP BOBI 应用程序，但请确保有足够用于部署和操作系统的空间。 建议至少为临时文件和 Web 应用程序提供 2 GB 的可用空间。 考虑到所有情况，建议将 SAP BOBI 安装二进制文件分隔到单独的分区中。

在此示例中，SAP BOBI 应用程序将安装在单独的分区 (F: ) 中。 初始化在预配虚拟机的过程中附加的高级 SSD 磁盘。

1. [A] 如果没有数据磁盘附加到虚拟机（azuswinboap1 和 azuswinboap2），请按照[添加磁盘](../../windows/attach-managed-disk-portal.md#add-a-data-disk)中的步骤附加新的托管数据磁盘。
2. [A] 托管磁盘附加到虚拟机后，请按照[初始化新数据磁盘](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk)文档中的步骤初始化磁盘。

### <a name="mount-azure-premium-files"></a>装载 Azure 高级文件

要使用 Azure 文件存储作为必须装载的文件存储，这意味着需要为其分配一个驱动器号或装入点路径。

1. [A] 要装载 Azure 文件共享，请按照[装载 Azure 文件共享](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share)文档中的步骤执行。

要在 windows 服务器上装载 Azure 文件共享，请确保端口 445 处于打开状态。 SMB 协议要求 TCP 端口 445 处于打开状态；如果端口 445 被阻止，则连接会失败。 可以通过[故障排除](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)指南中的 `Test-NetConnection` cmdlet，检查防火墙是否正在阻止端口 445。

## <a name="configure-cms-database---azure-sql"></a>配置 CMS 数据库 - Azure SQL

本部分提供有关如何使用 Azure 门户预配 Azure SQL 的详细信息。 它还介绍了如何为 SAP BOBI 平台创建 CMS 和审核数据库以及如何创建用于访问数据库的用户帐户。

这些准则仅在使用 Azure SQL（Azure 上的 DBaaS 产品/服务）时适用。 关于其他数据库，请参阅 SAP 或数据库特定的文档以了解相关说明。

### <a name="create-sql-database-server"></a>创建 SQL Database 服务器

Azure SQL 数据库提供不同的部署选项：单一数据库、弹性池和数据库服务器。 对于 SAP BOBI，我们需要两个数据库（CMS 和审核）。 因此，你可以创建管理单一数据库和弹性池的组的 SQL 数据库服务器，而不是创建两个单一数据库。  以下是创建 SQL 数据库服务器的步骤：

1. 浏览[选择 SQL 部署选项](https://portal.azure.com/#create/Microsoft.AzureSQL)页。
2. 在“SQL 数据库”下，将“资源类型”更改为“数据库服务器”，然后选择“创建”   。
3. 在“基本信息”选项卡上，填写“创建 SQL 数据库服务器”的所有必填字段 。

   a. 选择“项目详细信息”下的“订阅”和“资源组”  。

   b. 输入“服务器名称”（例如 azussqlbodb）。 服务器名称必须全局唯一，但可以提供任何想要的名称。

   c. 选择“位置”。

   d. 输入服务器管理员登录名（例如 boadmin）和密码 。

4. 在“网络”选项卡上，将“防火墙规则”下的“允许 Azure 服务和资源访问此服务器”更改为“否”   。
5. 在“其他设置”中，保留默认设置。
6. 继续执行并创建 SQL 数据库服务器。

在下一步中，在 SQL 数据库服务器 (azussqlbodb.database.windows.net) 中创建 CMS 和审核数据库。

### <a name="create-the-cms-and-the-audit-database"></a>创建 CMS 和审核数据库

预配 SQL 数据库服务器后，浏览到资源 azussqlbodb，然后按照以下步骤创建 CMS 和审核数据库。

1. 在 azussqlbodb 的“概述”页上，选择“创建数据库” 。
2. 在“基本信息”选项卡上，填写所有必填字段

   a. 输入“数据库名称”（例如 bocms 或 boaudit）。

   b. 在“计算 + 存储”选项中，选择“配置数据库”，然后根据调整大小的结果选择合适的模型 。 请参阅 [Azure SQL 数据库的调整大小模型](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database)，了解这些选项。
3. 在“网络”选项卡上，选择[专用终结点](../../../private-link/tutorial-private-endpoint-sql-portal.md)作为连接方法。 专用终结点用于访问已配置的虚拟网络中的 Azure SQL 数据库。

   a. 选择“添加专用终结点”。

   b. 选择“订阅”、“资源组”和“位置”  。

   c. 输入专用终结点的名称（例如 azusbodb-pe）。

   e. 选择“目标子资源”中的“SqlServer” 。

   f. 在“网络”部分中，选择将部署 SAP BusinessObjects BI 应用程序的“虚拟网络”和“子网”  。

   g. 接受“默认值(是)”，以便与专用 DNS 区域集成 。

   h. 从下拉列表中选择“专用 DNS 区域”。

   i. 选择“确定”，返回到“创建 SQL 数据库”中的“网络”选项卡。
4. 在“其他设置”选项卡中，将“排序规则”更改为“SQL_Latin1_General_CP850_BIN2”  。
5. 继续执行并创建 CMS 数据库。

同样，也可创建“审核”数据库（例如 boaudit）。

### <a name="download-and-install-odbc-driver"></a>下载并安装 ODBC 驱动程序

要使 SAP BOBI 应用程序服务器能够访问 CMS 或审核数据库，需要数据库客户端/驱动程序。 Microsoft ODBC 驱动程序用于访问在 Azure SQL 数据库上运行的 CMS 和审核数据库。 本部分提供有关如何在 Windows 上下载并设置 ODBC 驱动程序的说明。 

1. 要查看与 Azure SQL 数据库兼容的数据库连接器，请参阅[适用于 SAP BusinessObjects BI 平台的产品可用性矩阵](https://support.sap.com/pam)中的“OS 支持的 CMS + 审核存储库”。
2. 从[链接](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15)下载 ODBC 驱动程序版本。 在此示例中，我们将下载 ODBC 驱动程序 [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131)。
3. 在所有 BI 服务器上安装 ODBC 驱动程序（azuswinboap1 和 azuswinboap2）。
4. 在“azuswinboap1”中安装驱动程序后，导航到“启动” > “Windows 管理工具” > “ODBC 数据源(64 位)”   。  
5. 导航到“系统 DSN”选项卡。 
6. 选择“添加”，创建与 CMS 数据库的连接。
7. 选择“ODBC Driver 13 for SQL Server”，然后选择“完成” 。
8. 输入 CMS 数据库的信息（如下所示），然后选择“下一步”。
   - **名称：** 在“创建 CMS 和审核数据库”部分中创建的数据库名称（例如 bocms 或 boaudit）
   - **说明：** 描述数据源的说明（例如 CMS 数据库或审核数据库）
   - **服务器：** 在“创建 SQL 数据库服务器”部分中创建的 SQL Server 的名称（例如 azussqlbodb.database.windows.net）
9. 选择“使用用户输入的登录 ID 和密码进行 SQL Server 身份验证”，验证 SQL Server 真实性。 输入在创建 Azure SQL 数据库服务器（例如 boadmin）时创建的用户凭据，然后选择“下一步”。
10. 将默认数据库更改为 bocms，并将其他内容保留为默认值 。 选择“**下一页**”。
11. 选中“对数据使用强加密”，并将其他所有内容保留为默认值。 选择“完成”。
12. CMS 数据库的数据源已经创建完成，现在可以选择“测试数据源”，以验证从 BI 应用程序到 CMS 数据库的连接。 应该已成功完成。 如果失败，请[排查](../../../azure-sql/database/troubleshoot-common-errors-issues.md)连接性问题。

>[!Note]
>通过端口 1433 进行的 Azure SQL 数据库通信。 因此，应允许 SAP BOBI 应用服务器通过端口 1433 的出站流量。

重复上述步骤，为服务器 azuswinboap1 上的审核数据库创建连接。 同样在所有 BI 应用程序服务器上 (azuswinboap2) 安装和配置 ODBC 数据源（bocms 和 boaudit）。 

## <a name="server-preparation"></a>服务器准备

请按照 SAP 的最新指南为 BI 平台的安装准备服务器。 有关最新信息，请参阅[适用于 Windows 的商业智能平台安装指南](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html)中的“准备”部分。

## <a name="installation"></a>安装

要在 Windows 主机上安装 BI 平台，请使用具有本地管理权限的用户身份登录。

导航到 SAP BusinsessObjects BI 平台的媒体，然后运行 `setup.exe`。

遵循特定于你的版本的[适用于 Windows 的 SAP 商业智能平台安装指南](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html)。 在 Windows 上安装 SAP BOBI 平台时要注意的几个要点。 

- 在“配置目标文件夹”屏幕上，提供要在其中安装 BI 平台的目标文件夹。 （例如 F:\SAP BusinessObjects\)。 

- 在“配置产品注册”屏幕上，你可以使用 SAP 说明 [1288121](https://launchpad.support.sap.com/#/notes/1288121) 中的 SAP BusinessObjects 解决方案临时许可密钥，也可以在 SAP 服务市场中生成许可密钥。

- 在“选择安装类型”屏幕上，为第一台服务器 (azuswinboap1) 选择“完整”安装；为另一台服务器 (azuswinboap2) 选择“自定义/扩展”，这将扩展现有 BOBI 设置  。

- 在“选择默认或现有数据库”屏幕上，选择“配置现有数据库”，这将提示你选择 CMS 和审核数据库。  选择“使用 ODBC 的 Microsoft SQL Server”作为 CMS 数据库类型和审核数据库类型。

  如果不想在安装期间配置审核，还可以选择“无审核数据库”。

- 根据你的 SAP BOBI 体系结构，在“选择 Java Web 应用程序服务器”屏幕上选择适当的选项。 在此示例中，我们选择了选项 1，它在同一 SAP BOBI 平台上安装 tomcat 服务器。

- 在“配置 CMS 存储库数据库 - SQL Server”中输入 CMS 数据库信息。 适用于 Windows 安装的 CMS 数据库信息的示例输入。
  
  ![适用于 Windows 的 CMS 数据库信息](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)

- （可选）在“配置审核存储库数据库 - SQL Server (ODBC)”中输入审核数据库信息。 适用于 Windows 安装的审核数据库信息的示例输入。
  
  ![适用于 Windows 的审核数据库信息](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- 按照说明，提供所需的输入来完成安装。

对于多实例部署，请在第二个主机 (azuswinboap2) 上运行安装程序。 在“选择安装类型”屏幕中，选择“自定义/扩展”，这将扩展现有 BOBI 设置。  可在[通过 Azure SQL DB 进行 SAP BusinessObjects 商业智能平台设置](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/)上查看 SAP 博客

> [!IMPORTANT]
> SQL Server 和 Azure SQL 数据库的数据库引擎版本号之间没有可比性，它们分别是这两项产品的内部版本号。 适用于 Azure SQL 数据库的数据库引擎与 SQL Server 数据库引擎基于相同的代码基础映像。 最重要的是，Azure SQL 数据库中的数据库引擎始终具有 SQL 数据库引擎的最新功能。 Azure SQL 数据库版本 12 比 SQL Server 版本 15 更新。

要查看当前 Azure SQL 数据库的版本，可以签入中央管理控制台 (CMC) 的设置，也可以使用 [sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) 或 [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15) 运行以下查询。 SQL 版本与默认兼容性的对齐可以在[数据库兼容性级别](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15)一文中找到。

![CMC 中的数据库信息](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

```sql
1> select @@version as version;
2> go
version
------------------------------------------------------------------------------------------
Microsoft SQL Azure (RTM) - 12.0.2000.8
        Feb 20 2021 17:51:58
        Copyright (C) 2019 Microsoft Corporation

(1 rows affected)

1> select name, compatibility_level from sys.databases;
2> go
name                                                                  compatibility_level
--------------------------------------------------------------------- -------------------
master                                                                                150
bocms                                                                                 150
boaudit                                                                               150

(3 rows affected)
```

## <a name="post-installation"></a>安装后

SAP BOBI 平台的多实例安装完成后，需要执行额外的后配置步骤来支持应用程序的高可用性。

### <a name="configuring-cluster-name"></a>配置群集名称

在 SAP BOBI 平台的多实例部署中，你想要在群集中同时运行多个 CMS 服务器。 一个群集由两个或更多 CMS 服务器组成，这些服务器在一个通用 CMS 系统数据库上协同工作。 如果在 CMS 上运行的某个节点出现故障，则另一个 CMS 上的节点将继续为 BI 平台请求提供服务。 默认情况下，在 SAP BOBI 平台中，群集名称反映的是你安装第一个 CMS 的主机名。 

若要在 Windows 上配置群集名称，请按照 [SAP 商业智能平台管理员指南](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US)中所述的说明进行操作。 配置群集名称后，请按照 SAP 说明 [1660440](https://launchpad.support.sap.com/#/notes/1660440) 在 CMC 或 BI 启动台登录页上设置默认的系统入口。 

### <a name="configure-input-and-output-filestore-location-to-azure-premium-files"></a>将输入和输出文件存储位置配置到 Azure 高级文件

文件存储是指实际 SAP BusinessObjects 文件所在的磁盘目录。 SAP BOBI 平台的文件存储库服务器的默认位置位于本地安装目录中。 在多实例部署中，请务必在 Azure 高级文件或 Azure NetApp 文件等共享存储上设置文件存储，以便能够从所有存储层服务器实现访问。

1. 如果未创建，请按照上一节“预配 Azure 高级文件”中提供的说明创建并装载 Azure 高级文件。

   > [!Tip]
   > 根据虚拟机部署（可用性集或可用性区域）为 Azure 高级文件（LRS 或 ZRS）选择存储冗余。

2. 遵循 SAP 说明 [2512660](https://launchpad.support.sap.com/#/notes/0002512660)，更改文件存储库（输入和输出）的路径。

### <a name="tomcat-clustering---session-replication"></a>Tomcat 群集 - 会话复制

Tomcat 支持将两个或更多应用程序服务器组成群集，用于会话复制和故障转移。 SAP BOBI 平台会话将进行序列化，即使应用程序服务器发生故障，用户会话也可以无缝地故障转移到另一个 tomcat 实例。 例如，如果用户连接到的 web 服务器在用户在 SAP BI 应用程序中导航文件夹层次结构时失败。 在正确配置的群集上，用户可以继续导航文件夹层次结构，而无需重定向到登录页。

在 SAP 说明 [2808640](https://launchpad.support.sap.com/#/notes/2808640) 中，使用多播提供了配置 tomcat 群集的步骤。 但在 Azure 中，不支持多播。 因此，若要使 Tomcat 群集在 Azure 中可用，必须使用 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership)（SAP 说明 [2764907](https://launchpad.support.sap.com/#/notes/2764907)）。 请参阅 SAP 博客上的[使用 SAP BusinessObjects BI 平台静态成员身份的 Tomcat 群集](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)，在 Azure 中设置 tomcat 群集。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 平台的负载均衡 web 层

在 SAP BOBI 多实例部署中，Java Web 应用程序服务器（web 层）在两个或更多主机上运行。 若要在 Web 服务器之间均匀分配用户负载，可以在最终用户与 Web 服务器之间使用负载均衡器。 在 Azure 中，可以使用 Azure 负载均衡器或 Azure 应用程序网关来管理 web 应用程序服务器的流量。 下一节将介绍有关每个产品的详细信息。

1. [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)是高性能、低延迟的第 4 层（TCP、UDP）负载均衡器，可在正常运行的虚拟机之间分配流量。 负载均衡器的运行状况探测监视每个 VM 上的给定端口，并仅将流量分发给正常运行的虚拟机。 可以根据是否允许从 Internet 访问 SAP BI 平台，选择公共负载均衡器或内部负载均衡器。 它是区域冗余的，确保跨可用性区域的高可用性。

   请参阅下图中的“内部负载均衡器”部分，其中 Web 应用程序服务器在端口 8080（默认 Tomcat HTTP 端口）上运行，该端口将由运行状况探测进行监视。 因此，来自最终用户的任何传入请求都将被重定向到后端池中的 Web 应用程序服务器 (azuswinboap1 或 azuswinboap2)。 负载均衡器不支持 TLS/SSL 终止（也称为 TLS/SSL 卸载）。 如果使用 Azure 负载均衡器将流量分发到 Web 服务器，建议使用标准负载均衡器。

   > [!NOTE]
   > 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](high-availability-guide-standard-load-balancer-outbound-connections.md)。

   ![用于跨 Web 服务器均衡流量的 Azure 负载均衡器](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

2. [Azure 应用程序网关](../../../application-gateway/overview.md)以服务的形式提供应用程序传送控制器 (ADC)，用于帮助应用程序将用户流量定向到一个或多个 Web 应用程序服务器。 它为应用程序提供各种第 7 层负载均衡功能，例如 TLS/SSL 卸载、Web 应用程序防火墙 (WAF)、基于 Cookie 的会话相关性和其他功能。

   在 SAP BI 平台中，应用程序网关将应用程序 Web 流量定向到后端池（azuswinboap1 或 azuswinboap2）中的指定资源。 你将向端口分配侦听器，创建规则，并向后端池中添加资源。 在下图中，具有专用前端 IP 地址 (10.31.3.25) 的应用程序网关充当用户的入口点，处理传入的 TLS/SSL (HTTPS - TCP/443) 连接，对 TLS/SSL 进行解密，并将请求 (HTTP - TCP/8080) 传递到后端池中的服务器。 通过内置的 TLS/SSL 终止功能，只需在应用程序网关上维护一个 TLS/SSL 证书，这可以简化操作。

   ![用于跨 Web 服务器均衡流量的应用程序网关](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   若要为 SAP BOBI Web 服务器配置应用程序网关，可以参阅 SAP 博客上的[使用 Azure 应用程序网关实现 SAP BOBI 的负载均衡](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)。

   > [!NOTE]
   > 建议使用 Azure 应用程序网关对流量进行负载均衡，因为它提供 SSL 卸载、集中 SSL 管理（用于降低服务器上的加密和解密开销）、轮询算法（用于分发流量）、Web 应用程序防火墙 (WAF) 功能、高可用性等功能。

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Azure 上的 SAP BusinessObjects BI 平台可靠性

SAP BusinessObjects BI 平台包含不同的层，这些层针对特定任务和操作进行了优化。 当任何一层中的组件变为不可用时，SAP BOBI 应用程序将变得不可访问，或者应用程序的某些功能将无法正常工作。 因此，需要确保每个层的可靠性，使应用程序可以正常运行而不会出现任何业务中断。

本指南将探讨应如何将 Azure 的原生功能与 SAP BOBI 平台配置相结合，以改进 SAP 部署的可用性。 本节将重点介绍在 Azure 上实现 SAP BOBI 平台可靠性的以下选项：

- **备份和还原：** 将数据和应用程序的副本定期创建到单独位置的过程。 这样，如果原始数据或应用程序丢失或损坏，可将其还原或恢复到以前的状态。

- **高可用性：** 高可用平台的所有组件在 Azure 区域中都至少有两个实例，这样，即使其中一个服务器不可用，也能使应用程序保持正常运行。
- **灾难恢复：** 这是在出现灾难性损失（例如由于某些自然灾害而导致整个 Azure 区域不可用）时还原应用程序功能的过程。

此解决方案的实现因 Azure 中系统设置的性质而异。 因此，客户需要根据其业务需求定制他们的备份/还原、高可用性及灾难恢复解决方案。

## <a name="back-up-and-restore"></a>备份和还原

备份和还原是将数据和应用程序定期复制到单独位置的过程。 这样，如果原始数据或应用程序丢失或损坏，可将其还原或恢复到以前的状态。 它也是任何业务灾难恢复策略的基本组成部分。 这些备份可在配置的保留期内将应用程序和数据库还原到某个时间点。

若要为 SAP BOBI 平台开发全面的备份和还原策略，请确定导致应用程序系统停机或中断的组件。 在 SAP BOBI 平台中，以下组件的备份对保护应用程序至关重要。

- SAP BOBI 安装目录（托管高级磁盘）
- 文件存储（用于分布式安装的 Azure 高级文件或 Azure NetApp 文件）
- CMS 和审核数据库（Azure SQL 数据库、Azure database for MySQL 或 Azure 虚拟机上的数据库）

以下部分介绍如何为 SAP BOBI 平台上的每个组件实现备份和还原策略。

### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI 安装目录的备份和还原

在 Azure 中，要对包括所有附加磁盘的虚拟机进行备份，最简单的方法是使用 [Azure VM 备份](../../../backup/backup-azure-vms-introduction.md)服务。 它提供独立且隔离的备份，防止 VM 上的数据被意外破坏。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和缩放很简单，备份经过优化，可以在需要时轻松还原。

在备份过程中，将创建快照，并会将数据传输到恢复服务保管库，而不影响生产工作负载。 快照提供不同的一致性级别，如[快照一致性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)一文中所述。 除了 [Azure VM 备份](../../../backup/backup-azure-vms-introduction.md)，Azure 备份还使用 [Azure 磁盘备份](../../../backup/disk-backup-overview.md)为备份托管标识提供并行支持。 如果每天都需要对虚拟机进行一次一致性备份，并且经常需要对 OS 磁盘或崩溃一致的特定数据磁盘进行备份，则此方法非常有用。 有关详细信息，请参阅 [Azure VM 备份](../../../backup/backup-azure-vms-introduction.md)、[Azure 磁盘备份](../../../backup/disk-backup-overview.md)和[常见问题解答 - 备份 Azure VM](../../../backup/backup-azure-vm-backup-faq.yml)。

### <a name="backup--restore-for-filestore"></a>文件存储的备份和还原

根据你的部署，SAP BOBI 平台的文件存储可以位于 Azure NetApp 或 Azure 文件存储中。 根据用于文件存储的存储点，从以下选项中选择备份和还原的方式。

1. 对于 Azure NetApp 文件，可以创建按需快照，并使用快照策略计划自动拍摄快照。 快照副本提供 ANF 卷的时间点副本。 有关详细信息，请参阅[使用 Azure NetApp 文件管理快照](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)。
2. Azure 文件备份与本机 [Azure 备份](../../../backup/backup-overview.md)服务集成，该服务将备份和还原功能与 VM 备份集中在一起，从而简化操作。 有关详细信息，请参阅 [Azure 文件共享备份](../../../backup/azure-file-share-backup-overview.md)和[常见问题解答 - 备份 Azure 文件](../../../backup/backup-azure-files-faq.yml)。

如果已创建单独的 NFS 服务器，请确保为其实施相同的备份和还原策略。

### <a name="backup--restore-for-cms-and-audit-database"></a>适用于 CMS 和审核数据库的备份和还原

对于在 Windows 虚拟机上运行的 SAP BOBI 平台，CMS 和审核数据库可以在任何支持的数据库上运行，如 Azure 上 SAP BusinessObjects BI 平台规划和实施指南的[支持矩阵](businessobjects-deployment-guide.md#support-matrix)中所述。 因此，请务必根据用于 CMS 和审核数据存储的数据库来采用备份和还原策略。

1. Azure SQL 数据库使用 SQL Server 技术创建[完整备份](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15)（每周一次）、[差异备份](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15)（每 12 到 24 小时一次）和[事务日志](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15)备份（每 5 到 10 分钟一次）。 事务日志备份的频率取决于计算大小和数据库活动量。 用户可以选择一个选项在本地冗余、区域冗余或异地冗余存储 blob 之间配置备份存储冗余。 Azure 存储冗余机制会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 默认情况下，Azure SQL 数据库将备份存储在复制到[配对区域](../../../best-practices-availability-paired-regions.md)的异地冗余[存储 Blob](../../../storage/common/storage-redundancy.md) 中。 可以根据业务要求，将其更改为本地冗余或区域冗余存储 blob。 有关 Azure SQL 数据库备份计划、保留和存储消耗的更多最新信息，请参阅 [自动备份 - Azure SQL 数据库和 SQL 托管实例](../../../azure-sql/database/automated-backups-overview.md)

2. Azure Database for MySQL 可自动创建服务器备份并存储在用户配置的本地冗余或异地冗余存储中。 Azure Database for MySQL 会创建数据文件和事务日志的备份。 根据支持的最大存储大小，将进行完整备份和差异备份（最大 4 TB 的存储服务器）或快照备份（最大 16 TB 的存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认备份保持期为 7 天，可以[选择将其配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)为最多 35 天。 所有备份都使用 AES 256 位加密进行加密。 这些备份文件不公开给用户，因此无法导出。 这些备份只能用于 Azure Database for MySQL 中的还原操作。 可以使用 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) 复制数据库。 有关详细信息，请参阅[在 Azure Database for MySQL 中进行备份和还原](../../../mysql/concepts-backup.md)。

3. 对于在 Azure 虚拟机上安装的数据库，可以为支持的数据库使用标准备份工具或 [Azure 备份](../../../backup/sap-hana-db-about.md)服务。 此外，如果 Azure 服务和工具无法满足你的要求，则可以使用支持的第三方备份工具，而该工具应可为所有 SAP BOBI 平台组件的备份和还原提供代理。

## <a name="high-availability"></a>高可用性

高可用性指的是一系列技术，这些技术可通过相同数据中心内受冗余、容错或故障转移保护的组件，为应用程序/服务提供业务连续性，以最大程度减少 IT 中断的情况。 在本例中，数据中心位于一个 Azure 区域内。 [适用于 SAP 的高可用性体系结构和方案](sap-high-availability-architecture-scenarios.md)一文为 Azure 上针对 SAP 应用程序提供的各种高可用性技术和建议提供了见解，这将补充本部分中的说明。

根据 SAP BOBI 平台的大小调整结果，你需要设计格局，并确定跨 Azure 虚拟机和子网的 BI 组件分布。 分布式体系结构中的冗余级别取决于业务所需的恢复时间目标 (RTO) 和恢复点目标 (RPO)。 SAP BOBI 平台包含不同的层，应对每个层上的组件进行设计以实现冗余。 这样，如果一个组件发生故障，几乎或完全不会对 SAP BOBI 应用程序造成任何干扰。 例如，

- 冗余应用程序服务器，如 BI 应用程序服务器和 Web 服务器
- CMS 数据库、文件存储、负载均衡器等独特组件

下一部分介绍如何在 SAP BOBI 平台的每个组件上实现高可用性。

### <a name="high-availability-for-application-servers"></a>应用程序服务器的高可用性

对于 BI 和 Web 应用程序服务器，无论它们是单独安装还是一起安装的，都不需要特定的高可用性解决方案。 可以通过冗余实现高可用性，即在不同的 Azure 虚拟机中配置 BI 和 Web 服务器的多个实例。 可以根据业务要求的 RTO，在[可用性集](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set)或[可用性区域](sap-high-availability-architecture-scenarios.md#azure-availability-zones)中部署此虚拟机。 对于跨可用性区域的部署，请确保 SAP BOBI 平台中的所有其他组件也都被设计为区域冗余。

目前，并非所有 Azure 区域都提供可用性区域，因此你需要根据区域采用部署策略。 [Azure 可用性区域](../../../availability-zones/az-overview.md)中列出了提供局部区域的 Azure 区域。

> [!Important]
> Azure 可用性区域和 Azure 可用性集的概念是互斥的。 这意味着，可以将一对或多个 VM 部署到特定的可用性区域或 Azure 可用性集中。 但不能同时部署到这两者中。

### <a name="high-availability-for-cms-database"></a>CMS 数据库的高可用性

如果在 CMS 和审核数据库中使用 Azure 数据库即服务 (DBaaS) 这项服务，则默认提供本地冗余的高可用性框架。 只需选择区域和服务固有的高可用性、冗余和复原能力，无需配置任何其他组件。 如果 SAP BOBI 平台的部署策略跨可用性区域，则需要确保为 CMS 和审核数据库实现区域冗余。 有关 Azure 中支持的 DBaaS 产品的高可用性提供情况的详细信息，请参阅 [Azure SQL 数据库的高可用性](../../../mysql/concepts-high-availability.md)和 [Azure Database for MySQL 中的高可用性](../../../azure-sql/database/high-availability-sla.md)。 

对于 CMS 数据库的其他 DBMS 部署，请参阅[适用于 SAP 工作负载的 DBMS 部署指南](dbms_guide_general.md)，其中提供了有关不同 DBMS 部署及其实现高可用性的方法的见解。

### <a name="high-availability-for-filestore"></a>文件存储的高可用性

文件存储指存储报表、universe 和连接等内容的磁盘目录。 它在该系统的所有应用程序服务器之间共享。 因此，必须确保它与其他 SAP BOBI 平台组件都具有高可用性。

对于在 Windows 上运行的 SAP BOBI 平台，可以选择适用于文件存储的 [Azure 高级文件](../../../storage/files/storage-files-introduction.md)或 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)，这些经专门设计的文件在本质上具有高可用性和高耐用性。 Azure 高级文件支持区域冗余存储，这对于 SAP BOBI 平台的跨区域部署非常有用。 有关详细信息，请参阅 Azure 文件存储的[冗余](../../../storage/files/storage-files-planning.md#redundancy)部分。

由于文件共享服务仅在部分区域可用，因此请务必参考[各区域的可用产品](https://azure.microsoft.com/en-us/global-infrastructure/services/)站点以了解最新信息。 如果该服务在你的区域中不可用，可以创建 NFS 服务器，并从中将文件系统共享给 SAP BOBI 应用程序。 但你还需要考虑其高可用性。

### <a name="high-availability-for-load-balancer"></a>负载均衡器的高可用性

若要在 web 服务器之间分配流量，可以使用 Azure 负载均衡器或 Azure 应用程序网关。 可根据你为部署选择的 SKU 来实现任一负载均衡器的冗余。

1. 对于 Azure 负载均衡器，可以通过将标准负载均衡器前端配置为区域冗余来实现冗余。 有关详细信息，请参阅[标准负载均衡器和可用性区域](../../../load-balancer/load-balancer-standard-availability-zones.md)
2. 对于应用程序网关，可以根据部署过程中选择的层类型来实现高可用性。
   1. 如果部署了两个或更多实例，v1 SKU 支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，确保实例不会全部同时发生故障。 因此，使用此 SKU 可在区域内实现冗余。
   2. v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果选择“区域冗余”，则最新实例还将分布到各个可用性区域中以提供区域性故障复原能力。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关 v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台的参考高可用性体系结构

以下参考体系结构介绍了如何跨 Windows 服务器上运行的可用性区域设置 SAP BOBI 平台。 该体系结构展示如何使用不同的 Azure 服务提供内置冗余，如 Azure 应用程序网关、Azure 高级文件 (Filestore) 以及适用于的 SAP BOBI 平台的 Azure SQL 数据库（CMS 和审核数据库），以降低管理不同高可用性解决方案的复杂性。

在下图中，使用跨多个可用性区域的 Azure 应用程序网关 v2 SKU 对传入流量 (HTTPS - TCP/443) 进行负载均衡。 应用程序网关跨 Web 服务器分布用户请求，而这些 Web 服务器又跨可用性区域进行分布。 Web 服务器将请求转发到管理和处理服务器实例，这些实例部署在跨可用性区域的单独的虚拟机中。 具有区域冗余存储的 Azure 高级文件通过专用链接附加到管理和存储层级虚拟机，以访问报表、universe 和连接等内容。 应用程序访问在区域冗余 Azure SQL 数据库 (DBaaS) 上运行的 CMS 和审核数据库，该数据库可复制 Azure 区域内跨多个物理位置的数据库。

![Windows 上适用于 SAP BOBI 平台的高可用性体系结构](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

上述体系结构提供了有关如何在 Azure 上部署 SAP BOBI 的见解。 但它并未涵盖 Azure 上的 SAP BOBI 平台的所有可能的配置选项。 客户可根据其业务需求定制其部署，为负载均衡器、文件存储库服务器和 DBMS 等组件选择不同的产品/服务。

如果所选区域中的可用性区域不可用，则可以在可用性集中部署 Azure 虚拟机。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 如果硬件或软件发生故障，只有一部分 VM 会受到影响，整体解决方案仍会保持正常运行。

## <a name="disaster-recovery"></a>灾难恢复

本部分中的说明介绍为 SAP BOBI 平台提供灾难恢复保护的策略。 它补充了 [SAP 的灾难恢复](../../../site-recovery/site-recovery-sap.md)文档，该文档代表总体 SAP 灾难恢复方法的主要资源。 对于 SAP BusinessObjects BI 平台，请参阅 SAP 说明 [2056228](https://launchpad.support.sap.com/#/notes/2056228)，其中介绍了以下安全实现 DR 环境的方法。

 1. 完整或选择性使用生命周期管理 (LCM) 或联合，以提升/分发来自主系统的内容。
 2. 定期复制 CMS 和 FRS 内容。

在本指南中，我们将讨论实现 DR 环境的第二个选项。 其中不包含所有可能的灾难恢复配置选项的详尽列表，但涵盖与 SAP BOBI 平台配置结合使用原生 Azure 服务的解决方案。

>[!Important]
>在 SAP BusinessObjects BI 平台中，每个组件的可用性都应在次要区域中纳入考虑范畴，并且整个灾难恢复策略必须经过全面测试。

### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台的参考灾难恢复体系结构

此参考体系结构通过冗余的应用程序服务器运行 SAP BOBI 平台的多实例部署。 对于灾难恢复，应将 SAP BOBI 平台的所有组件故障转移到次要区域。 在下图中，Azure 高级文件用作文件存储，Azure SQL 数据库用作 CMS/审核存储库，而 Azure 应用程序网关用于对流量执行负载均衡。 为每个组件实现灾难恢复保护的策略各不相同，详见下一节中的详细介绍。

![适用于 Windows 的 SAP BusinessObjects BI 平台灾难恢复](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>负载均衡器

负载均衡器用于在 SAP BOBI 平台的 Web 应用程序服务器之间分配流量。 在 Azure 中，可以使用 Azure 负载均衡器或 Azure 应用程序网关来对整个 Web 服务器上的流量执行负载均衡。 若要为负载均衡器服务实现 DR，需要在次要区域中实现另一个 Azure 负载均衡器或 Azure 应用程序网关。 若要在完成 DR 故障转移后保留相同的 URL，需要更改 DNS 中的条目，并指向在次要区域中运行的负载均衡服务。

### <a name="virtual-machines-running-web-and-bi-application-servers"></a>运行 web 和 BI 应用程序服务器的虚拟机

可使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 服务在次要区域中复制运行 Web 和 BI 应用程序服务器的虚拟机。 它会将服务器及其中附加的所有托管磁盘复制到次要区域，以便在发生灾难和停机时，你可以轻松地将故障转移到复制的环境中，并继续工作。 若要开始将所有 SAP 应用程序虚拟机复制到 Azure 灾难恢复数据中心，请遵循 [将虚拟机复制到 Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md) 中的相关指导。

### <a name="filestore"></a>文件存储

文件存储指磁盘目录，其中存储了报表、BI 文档等实际文件。 文件存储中的所有文件都必须同步到 DR 区域，这一点很重要。 根据针对在 Windows 上运行的 SAP BOBI 平台所使用的文件共享服务类型，需要采用必要的 DR 策略来同步内容。

- **Azure 高级文件** 仅支持本地冗余 (LRS) 和区域冗余存储 (ZRS)。 对于 Azure 高级文件 DR 策略，可以使用 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 或 [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0) 将文件复制到不同区域中的其他存储帐户。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../../../storage/common/storage-disaster-recovery-guidance.md)

- **Azure NetApp 文件** 提供 NFS 和 SMB 卷，因此可使用任何基于文件的复制工具在 Azure 区域之间复制数据。 有关如何在另一个区域中复制 ANF 卷的详细信息，请参阅[关于 Azure NetApp 文件的常见问题解答](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  可以使用 Azure NetApp 文件跨区域复制，该功能当前提供[预览版](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)，它使用 NetApp SnapMirror® 技术。 因此只会以经压缩的高效格式通过网络发送更改后的块。 这项专有技术最大程度地减少了跨区域复制所需的数据量，可以节省数据传输成本。 它还可以缩短复制时间，让你可以实现较小的还原点目标 (RPO)。 有关详细信息，请参阅[使用跨区域复制的要求和注意事项](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)。

### <a name="cms-database"></a>CMS 数据库

DR 区域中的 CMS 和审核数据库必须是在主要区域中运行的数据库的副本。 根据数据库类型，请务必按照业务要求的 RTO 和 RPO 将数据库复制到 DR 区域。 本部分介绍可用于 Azure 中的每种数据库即服务 (DBaaS) 服务以及支持 Windows 上运行的 SAP BOBI 应用程序的不同选项。

#### <a name="azure-sql-database"></a>Azure SQL 数据库

对于 [Azure SQL 数据库](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) DR 策略，有以下两个选项可用于将数据库复制到次要区域。 这两种恢复选项具有不同级别的 RTO 和 RPO。 有关每个恢复选项的 RTO 和 RPO 的详细信息，请参阅[将数据库恢复到现有服务器](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server)。

1. [异地冗余数据库备份还原](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   默认情况下，Azure SQL 数据将数据存储在复制到[配对区域](../../../best-practices-availability-paired-regions.md)的异地冗余[存储 Blob](../../../storage/common/storage-redundancy.md) 中。 对于 SQL 数据库，可以在创建 CMS 和审核数据库时配置备份存储冗余，也可以针对现有数据库更新备份存储冗余；对现有数据库所做的更改仅适用于将来的备份。 可在任何 Azure 区域中的任何 Azure SQL 数据上从最新异地复制的备份中还原 SQL 数据库。 异地还原使用异地复制的备份作为源。 但在创建备份后，将其异地复制到其他区域中的 Azure Blob 时会出现延迟。 因此，还原的数据库可能比原始数据库晚最多一个小时。

   >[!Important]
   >异地还原适用于配置了异地冗余[备份存储](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy)的 Azure SQL 数据库。

2. [异地复制](../../../azure-sql/database/active-geo-replication-overview.md)或[自动故障转移组](../../../azure-sql/database/auto-failover-group-overview.md)

   异地复制是一项 Azure SQL 数据库功能，可用于在相同或不同的区域的服务器中创建各个数据库的可读辅助数据库。 如果为 CMS 和审核数据库启用了异地复制，则应用程序可以向其他 Azure 区域中的辅助数据库启动故障转移。 为单个数据库启用异地复制，但要为 SAP BOBI 应用程序的多个数据库（CMS 和审核）启用透明且协调的故障转移，建议使用自动故障转移组。 它在活动异地复制的顶层提供组语义，这意味着整个 Azure SQL Server（所有数据库）会复制到其他区域，而不是单个数据库。 查看[将异地复制与故障转移组](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups)进行比较的功能表。

   自动故障转移组提供在故障转移期间保持不变的读写和只读侦听器终结点。 可在 CMS 和审核数据库的 ODBC 连接项中将读写终结点作为侦听器进行维护。 因此无论使用手动故障转移激活还是自动故障转移激活，故障转移都会将组中所有的辅助数据库切换到主数据库。 数据库故障转移完成后，会自动更新 DNS 记录，以便将终结点重定向到新的区域。 因此应用程序将自动连接到 CMS 数据库，同时读写终结点在 ODBC 连接中作为侦听器进行维护。

   在下图中，在美国东部 2 区域上运行的 Azure SQL Server (azussqlbodb) 的自动故障转移组会复制到美国东部次要区域（DR 站点）。 对于在 Windows 上运行的 BI 应用程序服务器，读取/写入侦听器终结点在 ODBC 连接中作为侦听器进行维护。 故障转移后，终结点将保持不变，并且无需手动干预即可将 BI 应用程序连接到次要区域中的 Azure SQL 数据库。

   ![Azure SQL 数据库自动故障转移组](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   此选项提供的 RTO 和 RPO 级别比选项 1 更低。 有关此选项的详细信息，请参阅[使用自动故障转移组启用多个数据库的协调式透明故障转移](../../../azure-sql/database/auto-failover-group-overview.md)

#### <a name="azure-database-for-mysql"></a>Azure database for MySQL

Azure Database for MySQL 提供了多个选项，可在发生任何灾难时恢复数据库。 选择适用于你的业务的适当选项。

1. 可以使用跨区域只读副本来增强业务连续性和灾难恢复计划。 可将源服务器中的数据复制到最多 5 个副本。 只读副本使用 MySQL 的二进制日志复制技术进行异步更新。 副本是新的服务器，可以像管理普通的 Azure Database for MySQL 服务器一样对其进行管理。 从[只读副本概念文章](../../../mysql/concepts-read-replicas.md)详细了解有关只读副本、可用区域、限制以及如何进行故障转移的信息。

2. 使用 Azure Database for MySQL 的异地还原功能，该功能使用异地冗余备份来还原服务器。 即使你的服务器所在的区域离线，也可访问这些备份。 可以使用这些备份还原到任何其他区域并使服务器恢复联机。

  > [!Important]
  > 只有当为服务器预配了异地冗余备份存储时，异地还原才是可行的。 不支持在创建服务器后更改备份冗余选项。 有关详细信息，请参阅[备份冗余](../../../mysql/concepts-backup.md#backup-redundancy-options)一文。

下面是此示例中使用的每个层的灾难恢复建议。

| SAP BOBI 平台层                          | 建议                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure 应用程序网关或 Azure 负载均衡器 | 次要区域上应用程序网关的并行安装    |
| Web 应用服务器                          | 使用 Site Recovery 进行复制                             |
| BI 应用程序服务器                           | 使用 Site Recovery 进行复制                             |
| Azure 高级文件                              | AzCopy 或 Azure PowerShell                               |
| Azure NetApp 文件                               | 基于文件的复制工具将数据复制到次要区域或 ANF 跨区域复制（预览） |
| Azure SQL 数据库                               | 异地复制/自动故障转移组或异地还原。     |
| Azure Database for MySQL                         | 跨区域只读副本或从异地冗余备份还原备份。 |
## <a name="next-steps"></a>后续步骤

- [为多层 SAP 应用部署设置灾难恢复](../../../site-recovery/site-recovery-sap.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
- [适用于 SAP 的 Azure 虚拟机 DBMS 部署](./dbms_guide_general.md)