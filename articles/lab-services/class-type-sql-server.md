---
title: 设置实验室以使用 Azure SQL 数据库进行管理和开发 | Azure 实验室服务
description: 了解如何设置实验室以使用 Azure SQL 数据库进行管理和开发。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 50f71ee1ce59f5809fe8905c58f0399cf484f11a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659703"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>设置实验室以使用 SQL Server 进行管理和开发

本文介绍如何在 Azure 实验室服务中设置实验室以提供基础的 SQL Server 管理和开发课程。  数据库概念是大学里大多数计算机系讲授的入门课程之一。 结构化查询语言 (SQL) 是国际标准语言。  SQL 是一种用于关系数据库管理（包括在数据库中添加、访问和管理内容）的标准语言。  它以其快速处理能力、高可靠性、易用性和灵活性而闻名。

本文介绍如何在实验室中使用 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) 和 [Azure Data Studio](https://github.com/microsoft/azuredatastudio) 设置虚拟机模板。  对于此实验室，我们将在整个实验室中使用一个共享的 [SQL Server 数据库](../azure-sql/database/sql-database-paas-overview.md)。 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)是 Azure 提供的平台即服务 (PaaS) 引擎产品/服务。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅[有关设置实验室帐户的教程](./tutorial-setup-lab-account.md)。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 若要详细了解如何启用市场映像，请参阅[指定可供实验室创建者使用的市场映像](specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
| 市场映像 | 启用“用于 Windows 10 企业版 N (x64) 的 Visual Studio 2019 Community（最新版）”映像，以在实验室帐户中使用它。 |

### <a name="shared-resource-configuration"></a>共享资源配置

若要在实验室服务中使用共享资源，首先需要创建虚拟网络和资源本身。  若要创建虚拟网络并将其连接到实验室，请按照[如何在 Azure 实验室服务中使用共享资源创建实验室](how-to-create-a-lab-with-shared-resource.md)的说明操作。  请记住，实验室服务外部的所有资源都单独计费，并且不包含在实验室估算成本之内。

>[!WARNING]
>应在创建实验室之前设置用于实验室的共享资源。  如果在创建实验室之前未将 vnet [与实验室帐户对等互连](how-to-connect-peer-virtual-network.md)，实验室将不具有共享资源的访问权限。

处理资源的网络端后，接下来创建 SQL Server 数据库。  我们将创建[单一数据库](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)，因为它是最快速的 Azure SQL 数据库部署选项。  对于其他部署选项，请创建[弹性池](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)、[托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)或 [SQL 虚拟机](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md)。

1. 从“Azure 门户”菜单中，选择“新建资源”。
2. 选择“SQL 数据库”，并单击“创建” 按钮。
3. 在“创建 SQL 数据库”窗体的“基本信息”选项卡上，为数据库选择资源组。   我们将使用 sqldb-rg。
4. 在“数据库名称”中输入 classlabdb。
5. 在“服务器”设置下，单击“新建”创建新的服务器，以用于保存数据库。 
6. 在“新建服务器”浮出控件中，输入服务器名称。  我们将使用 classlabdbserver。  服务器名称必须全局唯一。
7. 在“服务器管理员登录名”中输入 azureuser。
8. 输入易记的密码。  密码长度必须至少为 8 个字符，并且必须包含特殊字符。
9. 在“位置”中选择区域。  如果可能，请输入实验室帐户和对等互连的 vnet 所在的同一位置，以最大程度减少延迟。
10. 单击“确定”以返回到“创建 SQL 数据库” 窗体。
11. 单击“计算 + 存储”设置下的“配置数据库”链接。 
12. 根据需要为课程修改数据库设置。  可以在“预配”和“无服务器”选项之间进行选择。  在此示例中，我们将使用“无服务器”选项，此选项可自动缩放，最多具有 4 个 Vcore，最少 1 个 Vcore。 我们将自动暂停设置保留为最小值（1 小时）。 单击“应用”。
13. 单击“下一步: 网络”按钮。
14. 在“网络”选项卡上，选择专用终结点作为连接方法。
15. 在“专用终结点”部分下，单击“添加专用终结点” 。
16. 在“创建专用终结点”浮出控件中，选择与实验室帐户对等互连的虚拟网络的同一资源组。
17. 在“位置”中选择虚拟网络的同一位置。
18. 在“名称”中输入 labsql-endpt。
19. 将“目标子资源集”保留为 SqlServer。
20. 在“虚拟网络”中，选择与实验室帐户对等互连的同一虚拟网络。
21. 在“子网”中，选择用于托管终结点的子网。  分配给终结点的 IP 在分配给该子网的范围内。
22. 将“与专用 DNS 集成”设置为“否” 。 为简单起见，我们使用 Azure 的 DNS，而不使用自己的专用 DNS 区域或自己的 DNS 服务器。
23. 单击 **“确定”** 。
24. 单击“下一步:  其他设置”。
25. 在“使用现有数据”设置中，选择“示例” 。  创建数据库时将使用 AdventureWorksLT 数据库中的数据。
26. 单击“查看 + 创建”。
27. 单击“创建”。

SQL 数据库部署成功完成后，即可在实验室模板计算机上创建实验室并安装软件。

### <a name="lab-settings"></a>实验室设置

设置课堂实验室时，请使用下表中的设置。 若要详细了解如何创建课堂实验室，请参阅[创建课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
| 虚拟机大小 | 中等。 此大小最适用于关系数据库、内存中缓存和分析。 |
| 虚拟机映像 | 用于 Windows 10 企业版 N (x64) 的 Visual Studio 2019 Community（最新版） |

创建实验室后，使用所需的软件修改模板计算机。

## <a name="visual-studio"></a>Visual Studio

上面所选的映像包括 [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/)。  映像上已安装所有工作负载和工具集。  使用 Visual Studio 安装程序[安装所需的任何可选工具](/visualstudio/install/modify-visual-studio?view=vs-2019)。  [登录到 Visual Studio](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) 以解锁 Community 版本。

Visual Studio 包括数据存储和处理工具集，其中包含 SQL Server Data Tools (SSDT)。  有关 SSDT 功能的详细信息，请参阅 [SQL Server Data Tools 概述](/sql/ssdt/sql-server-data-tools?view=sql-server-ver15)。  若要验证是否可成功连接到用于课堂的共享 SQL Server，请参阅[连接到数据库并浏览现有对象](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15)。 如果系统出现提示，则将模板计算机 IP 添加到可连接到 SQL Server 实例的[允许的计算机列表](../azure-sql/database/firewall-configure.md) 中。

Visual Studio 支持多种工作负载，其中包括 Web 和云以及桌面和移动工作负载 。  这两种工作负载都支持 SQL Server 作为数据源。 有关使用 ASP.NET Core 或 SQL Server 的详细信息，请参阅[在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](../app-service/tutorial-dotnetcore-sqldb-app.md)教程。  使用 [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) 库从 [Xamarin](/xamarin) 应用连接到 SQL 数据库。

## <a name="install-azure-data-studio"></a>安装 Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) 是多数据库跨平台桌面环境，供在 Windows、macOS 和 Linux 上使用系列本地及云数据平台的数据专业人员使用。

1. 请下载[适用于 Windows 的 Azure Data Studio 系统安装程序](https://go.microsoft.com/fwlink/?linkid=2127432)。 若要查找适用于其他受支持的操作系统的安装程序，请转到 [Azure Data Studio](/sql/azure-data-studio/download) 下载页。
2. 在“许可协议”页上，选择“我接受此协议” 。 单击“下一步”。
3. 在“选择目标位置”页上，单击“下一步”。
4. 在“选择开始菜单文件夹”页上，单击“下一步”。
5. 如果需要桌面图标，请在“选择其他任务”页上选中“创建桌面图标” 。  单击“下一步”。
6. 在“准备安装”上单击“下一步” 。
7. 等待安转程序运行。  单击“完成”  。

安装 Azure Data Studio 后，设置与 Azure SQL 数据库的连接。

1. 在 Azure Data Studio 的“欢迎”页上，单击“新建连接” 链接。
2. 在“连接详细信息”框中，填写所需的信息。
    - 将“服务器”设置为 classlabdbserver.database.windows.net
    - 将“用户名”设置为“azureuser”
    - 将“密码”设置为用于创建数据库的密码。
    - 选中“记住密码”。
    - 在“数据库”中，选择 classlabdb。
3. 单击“连接”  。

## <a name="install-sql-server-management-studio"></a>安装 SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) 是一种集成环境，用于管理任何 SQL 基础结构。  SSMS 是数据库管理员用于部署、监视和升级数据基础结构的工具。

1. [下载 SQL Server Management Studio](https://aka.ms/ssmsfullsetup)。 下载完成后，启动此安装程序。
2. 在“欢迎”页上，单击“安装” 。
3. 在“安装完成”页上，单击“关闭”。 
4. 启动 SQL Server Management Studio。  
5. 在“依赖项配置过程”页上，单击“关闭” 。

请注意，安装 SSMS 后，可以[连接和查询 SQL Server](/sql/ssms/tutorials/connect-query-sql-server)。 设置连接时，请使用以下值：

- 服务器类型：数据库引擎
- 服务器名称：classlabdbserver.database.windows.net
- 身份验证：SQL Server 身份验证
- 登录名：azureuser
- 密码：用于创建数据库的密码。

## <a name="cost-estimate"></a>成本估算

我们来介绍此课程可能的估算成本。 估算成本不包括运行 SQL Server 的成本。  有关数据库定价的最新详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database)。

课程将提供给 25 名学生。 课程时间计划为 20 小时。 此外，在计划的课程时间之外还为每名学生提供 10 小时配额，用于完成家庭作业或课外作业。 我们所选的虚拟机大小为“中等”，包含“42 个实验室单位”。

下面是此课程可能的成本估算示例：

25 名学生 \*（计划 20 小时 \+ 配额 10 小时）* 每小时 0.42 美元 = 315.00 美元

>[!IMPORTANT]
>成本估算仅用于示例目的。 有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建、管理和发布模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)