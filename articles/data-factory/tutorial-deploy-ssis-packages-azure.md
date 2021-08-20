---
title: 预配 Azure-SSIS 集成运行时
description: 了解如何在 Azure 数据工厂中预配 Azure-SSIS 集成运行时，以便能够在 Azure 中部署和运行 SSIS 包。
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 07/19/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 6b2f1f796c7a3c41aa28040e023be6cc86bc21f8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447785"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中预配 Azure-SSIS 集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本教程提供使用 Azure 门户在 Azure 数据工厂 (ADF) 中预配 Azure-SQL Server Integration Services (SSIS) 集成运行时 (IR) 的步骤。 Azure-SSIS IR 支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

预配 Azure-SSIS IR 后，可以使用熟悉的工具在 Azure 中部署和运行包。 这些工具已启用 Azure 并包括 SQL Server Data Tools (SSDT)、SQL Server Management Studio (SSMS) 和命令行实用工具，例如 [dtutil](/sql/integration-services/dtutil-utility) 和 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md)。

有关 Azure-SSIS IR 的概念性信息，请参阅 [Azure-SSIS 集成运行时概述](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教程中，将完成以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅**。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

- **Azure SQL 数据库服务器（可选）** 。 如果还没有数据库服务器，请在启动之前在 Azure 门户中创建一个。 数据工厂进而会在此数据库服务器上创建一个 SSISDB 实例。 

  建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。

  请记住以下几点：

  - 根据所选的数据库服务器，系统可以代表你创建 SSISDB 实例作为单一数据库、创建此实例作为弹性池的一部分，或者在托管实例中创建。 可以在公用网络中访问或者通过加入虚拟网络来访问该实例。 有关选择用来托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 SQL 数据库与 SQL 托管实例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。 
  
    如果使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来承载 SSISDB，或者需要在未配置自承载 IR 的情况下访问本地数据，则需要将 Azure-SSIS IR 加入虚拟网络。 有关详细信息，请参阅[在虚拟网络中创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)。

  - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来承载 SSISDB 时，此设置并不适用。 有关详细信息，请参阅[保护 Azure SQL 数据库的安全](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。

  - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../azure-sql/database/firewall-configure.md)。

  - 若要连接到数据库服务器，可以通过使用 SQL 身份验证（使用服务器管理员凭据）的方式，也可以通过使用 Azure Active Directory (Azure AD) 身份验证（使用为数据工厂指定的系统/用户分配的托管标识）的方式。 对于后一种方式，需要将为数据工厂指定的系统/用户分配的托管标识添加到有权访问数据库服务器的 Azure AD 组中。 有关详细信息，请参阅[使用 Azure AD 身份验证创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)。

  - 确认你的数据库服务器还没有 SSISDB 实例。 预配 Azure-SSIS IR 时不支持使用现有的 SSISDB 实例。

> [!NOTE]
> 如需目前提供数据工厂和 Azure-SSIS IR 的 Azure 区域的列表，请参阅[数据工厂和 SSIS IR 在各区域的上市情况](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。 

## <a name="create-a-data-factory"></a>创建数据工厂

若要通过 Azure 门户创建数据工厂，请按照[通过 UI 创建数据工厂](./quickstart-create-data-factory-portal.md#create-a-data-factory)中的分步说明操作。 执行此操作时，请选择“固定到仪表板”，以便在创建后能够快速访问数据工厂。 

创建数据工厂后，在 Azure 门户中打开其概述页。 选择“创建和监视”磁贴，在单独的选项卡中打开“开始”页。 然后可以继续创建 Azure-SSIS IR。

## <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时

### <a name="from-the-data-factory-overview"></a>使用数据工厂概览

1. 在主页上，选择“配置 SSIS”磁贴。 

   ![屏幕截图显示 Azure 数据工厂主页。](./media/doc-common-process/get-started-page.png)

1. 请参阅[预配 Azure-SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

### <a name="from-the-authoring-ui"></a>使用创作 UI

1. 在 Azure 数据工厂 UI 中切换到“管理”选项卡，然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时 。 

   ![用于查看现有 IR 的选择](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 选择“新建”，创建 Azure-SSIS IR，然后打开“集成运行时设置”窗格 。 

   ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在“集成运行时设置”窗格中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”磁贴，然后选择“继续”  。

   ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 请参阅[预配 Azure-SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure-SSIS 集成运行时

“集成运行时设置”窗格中有三个页面，在其中可以连续配置常规设置、部署设置和高级设置。

### <a name="general-settings-page"></a>“常规设置”页

在“集成运行时设置”窗格的“常规设置”页中完成以下步骤 。 

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. 对于“名称”，请输入集成运行时的名称。 

   1. 对于“说明”，请输入集成运行时的说明。 

   1. 对于“位置”，请选择集成运行时的位置。 界面上仅显示支持的位置。 建议选择承载 SSISDB 所需的数据库服务器的位置。 

   1. 对于“节点大小”，请选择集成运行时群集中的节点大小。 仅显示支持的节点大小。 如果需要运行多个计算密集型或内存密集型包，请选择较大的节点大小（纵向扩展）。 

   1. 对于“节点数”，请选择集成运行时群集中的节点数。 仅显示支持的节点数。 如果需要并行运行多个包，请选择包含许多节点的大型群集（横向扩展）。 

   1. 对于“版本/许可证”，请选择集成运行时的 SQL Server 版本：“标准”或“企业”。 如果需要在集成运行时上使用高级功能，请选择“Enterprise”。 

   1. 对于“节省资金”，请选择适用于集成运行时的“Azure 混合权益”选项：“是”或“否”。 如果需要自带具有软件保障的 SQL Server 许可证，以便充分利用使用混合权益带来的成本节省，请选择“是”。 

   1. 选择“继续”。 

### <a name="deployment-settings-page"></a>“部署设置”页

在“集成运行时设置”窗格的“部署设置”页中，你可以选择创建 SSISDB 和/或 Azure-SSIS IR 包存储 。

#### <a name="creating-ssisdb"></a>创建 SSISDB

在“集成运行时设置”窗格的“部署设置”页中，如果要将包部署到 SSISDB（项目部署模型），请选中“创建由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 来存储项目/包/环境/执行日志”复选框  。 或者，如果要将包部署到由 Azure SQL 托管实例（包部署模型）托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中，则无需创建 SSISDB 或选中该复选框。

无论采用哪种部署模型，如果要使用由 Azure SQL 托管实例托管的 SQL Server 代理来协调/安排包执行，请选中该复选框，因为它已由 SSISDB 启用。 有关详细信息，请参阅[通过 Azure SQL 托管实例代理来安排 SSIS 包执行](./how-to-invoke-ssis-package-managed-instance-agent.md)。
   
如果选中该复选框，请完成以下步骤以提供你自己的数据库服务器来承载我们将代表你创建和管理的 SSISDB。

   ![SSISDB 的部署设置](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. 对于“订阅”，请选择使用数据库服务器来托管 SSISDB 的 Azure 订阅。 

   1. 对于“位置”，请选择用于托管 SSISDB 的数据库服务器的位置。 建议选择集成运行时的位置。

   1. 对于“目录数据库服务器终结点”，请选择用于承载 SSISDB 的数据库服务器的终结点。 
   
      根据所选的数据库服务器，系统可以代表你创建 SSISDB 实例作为单一数据库、创建此实例作为弹性池的一部分，或者在托管实例中创建。 可以在公用网络中访问或者通过加入虚拟网络来访问该实例。 有关选择用来托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 SQL 数据库与 SQL 托管实例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。   

      如果选择具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来承载 SSISDB，或者需要在未配置自承载 IR 的情况下访问本地数据，则需要将 Azure-SSIS IR 加入虚拟网络。 有关详细信息，请参阅[在虚拟网络中创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)。

   1. 选中“结合使用 AAD 身份验证和数据工厂的系统托管标识”或“结合使用 AAD 身份验证和数据工厂的用户分配的托管标识”复选框，以选择 Azure AD 身份验证方法来让 Azure-SSIS IR 访问承载 SSISDB 的数据库服务器 。 如果不选中上述任一复选框，则会改为选择 SQL 身份验证方法。

      如果选中上述任一复选框，则需要将数据工厂的指定系统/用户分配的托管标识添加到有权访问数据库服务器的 Azure AD 组中。 如果选中“结合使用 AAD 身份验证和数据工厂的用户分配的托管标识”复选框，则随后可以选择通过所指定用户分配的托管标识创建的任何现有凭据，或者创建新的凭据。 有关详细信息，请参阅[使用 Azure AD 身份验证创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)。

   1. 对于“管理员用户名”，请输入承载 SSISDB 的数据库服务器的 SQL 身份验证用户名。 

   1. 对于“管理员密码”，请输入承载 SSISDB 的数据库服务器的 SQL 身份验证密码。 

   1. 选中“结合使用双备用 Azure-SSIS Integration Runtime 对与 SSISDB 故障转移”复选框可配置双备用 Azure-SSIS IR 对，该对与 Azure SQL 数据库/托管实例故障转移组同步工作，以实现业务连续性和灾难恢复 (BCDR)。
   
      如果选中该复选框，请在“双备用对名称”文本框中输入名称以标识主要和辅助 Azure-SSIS IR 对。 创建主要和辅助 Azure-SSIS IR 时，需要输入相同的对名称。

      有关详细信息，请参阅[配置 Azure-SSIS IR 以实现 BCDR](./configure-bcdr-azure-ssis-integration-runtime.md)。

   1. 对于“目录数据库服务层级”，请选择用于承载 SSISDB 的数据库服务器的服务层级。 选择“基本”、“标准”或“高级”层级，或选择弹性池名称。

如果适用，请选择“测试连接”，如果成功，请选择“继续” 。

#### <a name="creating-azure-ssis-ir-package-stores"></a>创建 Azure-SSIS IR 包存储

在“集成运行时设置”窗格的“部署设置”页中，如果要使用 Azure-SSIS IR 包存储来管理部署到 MSDB、文件系统或 Azure 文件存储中的包（包部署模型），请选中“创建包存储以管理部署到由 Azure SQL 托管实例托管的文件系统/Azure 文件/SQL Server 数据库 (MSDB) 中的包”复选框  。
   
借助 Azure-SSIS IR 包存储，你可以通过与[旧版 SSIS 包存储](/sql/integration-services/service/package-management-ssis-service)类似的 SSMS 导入/导出/删除/运行包以及监视/停止正在运行的包。 有关详细信息，请参阅[使用 Azure-SSIS IR 包存储管理 SSIS 包](./azure-ssis-integration-runtime-package-store.md)。
   
如果选中此复选框，则可以通过选择“新建”将多个包存储添加到 Azure-SSIS IR。 相反，多个 Azure SSIS IR 可以共享一个包存储。

![MSDB/文件系统/Azure 文件存储的部署设置](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

在“添加包存储”窗格上，完成以下步骤。
   
   1. 对于“包存储名称”，请输入包存储的名称。 

   1. 对于“包存储链接服务”，请选择现有的链接服务，该服务存储在其中部署包的文件系统/Azure 文件存储/Azure SQL 托管实例的访问信息，或通过选择“新建”创建新链接服务 。 在“新建链接服务”窗格中，完成以下步骤。 

      > [!NOTE]
      > 可以使用 Azure文件存储或文件系统链接服务来访问 Azure 文件 。 如果你使用 Azure 文件存储链接服务，则 Azure-SSIS IR 包存储仅支持基本（不支持帐户密钥或 SAS URI）身份验证方法   。 

      ![链接服务的部署设置](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. 对于“名称”，请输入链接服务的名称。 
         
      1. 对于“说明”，请输入链接服务的说明。 
         
      1. 对于“类型”，请选择“Azure 文件存储”、“Azure SQL 托管实例”或“文件系统”   。

      1. 可以忽略“通过集成运行时连接”，因为我们始终使用 Azure-SSIS IR 来提取包存储的访问信息。
      
      1. 如果选择“Azure 文件存储”，请对“身份验证方法”选择“基本”，然后完成以下步骤  。 

         1. 对于“帐户选择方法”，请选择“从 Azure 订阅”或“手动输入”  。
         
         1. 如果选择“从 Azure 订阅”，请选择相关的“Azure 订阅”、“存储帐户名称”和“文件共享”   。
            
         1. 如果选择“手动输入”，对于“主机”，请输入 `\\<storage account name>.file.core.windows.net\<file share name>`，对于“用户名”，请输入 `Azure\<storage account name>`，对于“密码”，请输入 `<storage account key>`，或选择在其中作为机密存储的“Azure 密钥保管库”    。

      1. 如果选择“Azure SQL 托管实例”，请完成以下步骤。 

         1. 选择“连接字符串”，或选择将这些信息作为机密存储的 Azure 密钥保管库 。
         
         1. 如果选择“连接字符串”，请完成以下步骤。 
             1. 对于“帐户选择方法”，如果选择“从 Azure 订阅”，请选择相关的 Azure 订阅、服务器名称、终结点类型和数据库名称     。 如果选择“手动输入”，请完成以下步骤。 
                1.  对于“完全限定的域名”，请输入 `<server name>.<dns prefix>.database.windows.net` 或 `<server name>.public.<dns prefix>.database.windows.net,3342` 分别作为 Azure SQL 托管实例的专用或公共终结点。 如果输入专用终结点，则“测试连接”不适用，因为 ADF UI 无法访问它。

                1. 对于“数据库名称”，请输入 `msdb`。
               
            1. 对于“身份验证类型”，请选择“SQL 身份验证”、“托管标识”、“服务主体”或“用户分配的托管标识”    。

                - 如果选择“SQL 身份验证”，请输入相关的“用户名”和“密码”，或选择在其中作为机密存储的“Azure 密钥保管库”   。

                -  如果选择“托管标识”，请向 ADF 的系统托管标识授予对 Azure SQL 托管实例的访问权限。

                - 如果选择“服务主体”，请输入相关的“服务主体 ID”和“服务主体密钥”，或选择在其中作为机密存储的“Azure 密钥保管库”   。
                
                -  如果选择“用户分配的托管标识”，请向 ADF 的指定用户分配的托管标识授予对 Azure SQL 托管实例的访问权限。 然后，可以选择通过所指定用户分配的托管标识创建的任何现有凭据，或者创建新的凭据。

      1. 如果选择“文件系统”，请输入在其中为“主机”部署了包的文件夹的 UNC 路径，以及相关的“用户名”和“密码”，或选择在其中作为机密存储的“Azure 密钥保管库”    。

      1. 如果适用，请选择“测试连接”，如果成功，请选择“创建” 。

   1. 添加的包存储将显示在“部署设置”页上。 若要删除它们，请选中其复选框，然后选择“删除”。

如果适用，请选择“测试连接”，如果成功，请选择“继续” 。

### <a name="advanced-settings-page"></a>“高级设置”页

在“集成运行时设置”窗格的“高级设置”页中，请完成以下步骤 。 

   ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. 对于“每个节点的最大并行执行数”，请选择要在集成运行时群集中并发运行的最大包数（按节点）。 仅显示支持的包数。 如果需要使用多个核心来运行单个计算密集型或内存密集型的大型包，请选择较小的数字。 如果需要在单个核心中运行一个或多个小型包，请选择较大的数字。 

   1. 选中“使用其他系统配置/组件安装自定义 Azure-SSIS 集成运行时”复选框，选择是否要在 Azure-SSIS IR 上添加标准/快速自定义设置。 有关详细信息，请参阅 [Azure-SSIS IR 的自定义安装](./how-to-configure-azure-ssis-ir-custom-setup.md)。
   
   1. 选中“选择 Azure-SSIS 集成运行时要加入到的 VNet，允许 ADF 创建特定的网络资源，视情况引入你自己的静态公共 IP 地址”复选框，选择是否要将 Azure-SSIS IR 加入虚拟网络。

      如果使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来承载 SSISDB，或者需要在未配置自承载 IR 的情况下访问本地数据，请选中此复选框。 有关详细信息，请参阅[在虚拟网络中创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)。 
   
   1. 选中“将自承载集成运行时安装为 Azure-SSIS 集成运行时的代理”复选框，选择是否要将自承载 IR 配置为 Azure-SSIS IR 的代理。 有关详细信息，请参阅[将自承载 IR 设置为代理](./self-hosted-integration-runtime-proxy-ssis.md)。   

   1. 选择“继续”。 

在“集成运行时设置”窗格的“摘要”页上，查看所有预配设置，将建议的文档链接添加为书签，然后选择“创建”开始创建集成运行时  。 

   > [!NOTE]
   > 此过程应在 5 分钟内完成（不包括任何自定义安装时间）。
   >
   > 如果使用 SSISDB，数据工厂服务将连接到数据库服务器以准备 SSISDB。 
   > 
   > 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel 文件、Access 文件和各种 Azure 数据源的连接。 有关内置/已预安装组件的详细信息，请参阅 [Azure-SSIS IR 上的内置/已预安装组件](./built-in-preinstalled-components-ssis-integration-runtime.md)。 有关可安装的其他组件的详细信息，请参阅 [Azure-SSIS IR 的自定义安装](./how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="connections-pane"></a>“连接”窗格

在“管理”中心的“连接”窗格中，切换到“集成运行时”页，然后选择“刷新”   。 

   ![“连接”窗格](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   可以通过选择 Azure-SSIS IR 的名称来对其进行编辑/重新配置。 此外，还可以选择相应的按钮来监视/启动/停止/删除 Azure-SSIS IR，通过“执行 SSIS 包”活动自动生成 ADF 管道以在 Azure-SSIS IR 上运行，并查看 Azure-SSIS IR 的 JSON 代码/有效负载。  只能在 Azure-SSIS IR 停止时对其执行编辑/删除操作。

## <a name="deploy-ssis-packages"></a>部署 SSIS 包

如果使用 SSISDB，可将包部署到其中，并使用已启用 Azure 的 SSDT 或 SSMS 工具在 Azure-SSIS IR 上运行它们。 这些工具通过数据库服务器的服务器终结点来与该服务器建立连接： 

- 对于 Azure SQL 数据库服务器，服务器终结点格式为 `<server name>.database.windows.net`。
- 对于具有专用终结点的托管实例，服务器终结点格式为 `<server name>.<dns prefix>.database.windows.net`。
- 对于具有公共终结点的托管实例，服务器终结点格式为 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

如果不使用 SSISDB，则可以将包部署到由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 MSDB 中，并使用 [dtutil](/sql/integration-services/dtutil-utility) 和 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) 命令行实用工具在 Azure-SSIS IR 上运行它们。 

有关详细信息，请参阅[部署 SSIS 项目/包](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)。

在这两种情况下，还可以使用数据工厂管道中的“执行 SSIS 包”活动在 Azure-SSIS IR 上运行已部署的包。 有关详细信息，请参阅[以第一类数据工厂活动的形式调用 SSIS 包执行](./how-to-invoke-ssis-package-ssis-activity.md)。

另请参阅以下 SSIS 文档： 

- [在 Azure 中部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [连接到 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [在 Azure 中计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>后续步骤

若要了解如何自定义 Azure-SSIS Integration Runtime，请参阅下文： 

> [!div class="nextstepaction"]
> [自定义 Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)