---
title: 在 Azure 虚拟机上设置适用于 Micro Focus Enterprise Developer 4.0 的 Micro Focus CICS BankDemo
description: 在 Azure 虚拟机上 (VM) 上运行 Micro Focus BankDemo 应用程序，以了解如何使用 Micro Focus Enterprise Server 和 Enterprise Developer。
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 9ef9dd27183b6c23762dcdc2d5a844123d35559a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045954"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>在 Azure 上设置适用于 Micro Focus Enterprise Developer 4.0 的 Micro Focus CICS BankDemo

在 Azure 上设置 Micro Focus Enterprise Server 4.0 和 Enterprise Developer 4.0 时，可以测试 IBM z/OS 工作负载的部署。 本文介绍如何设置 Enterprise Developer 随附的示例应用程序 CICS BankDemo。

CICS 是“客户信息控制系统”的英文缩写，是许多联机大型机应用程序使用的事务平台。 BankDemo 应用程序非常适合用于了解 Enterprise Server 和 Enterprise Developer 的运行方式，以及如何管理和部署与绿屏终端配套的实际应用程序。

> [!NOTE]
> 即将发布：有关在 Azure VM 上设置 [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) 的说明。

## <a name="prerequisites"></a>先决条件

- 一个装有 [Enterprise Developer](set-up-micro-focus-azure.md) 的 VM。 请注意，Enterprise Developer 包含一个用于开发和测试的完整 Enterprise Server 实例。 此实例是用于演示的 Enterprise Server 实例。

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express)。 请下载此系统并将其安装在 Enterprise Developer VM 上。 Enterprise Server 需要通过一个数据库来管理 CICS 区域，BankDemo 应用程序也使用一个 SQL Server 数据库（名称为 BANKDEMO）。 本演示假设你对这两个数据库使用 SQL Server Express。 安装时请选择基本安装。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 SSMS 用于管理数据库和运行 T-SQL 脚本。 请下载此系统并将其安装在 Enterprise Developer VM 上。

- 包含最新服务包的 [Visual Studio 2019](https://azure.microsoft.com/downloads/)，或者可免费下载的 [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)。

- Rumba Desktop 或其他 3270 仿真器。

## <a name="configure-the-windows-environment"></a>配置 Windows 环境

在 VM 上安装 Enterprise Developer 4.0 后，必须配置其中随附的 Enterprise Server 实例。 为此，需要按如下所述安装额外的几个 Windows 功能。

1. 使用 RDP 登录到创建的 Enterprise Server 4.0 VM。

2. 单击“开始”按钮旁边的“搜索”图标，然后键入“Windows 功能”  。 此时会打开“服务器管理器 - 添加角色和功能向导”。

3. 选择“Web 服务器(IIS)角色”，然后选中以下选项：

    - Web 管理工具
    - IIS 6 管理兼容性（选择所有可用功能）
    - IIS 管理控制台
    - IIS 管理脚本和工具
    - IIS 管理服务

4. 选择“万维网服务”，然后选中以下选项：

     应用程序开发功能：
    - .NET 可扩展性
    - ASP.NET
    - 常用 HTTP 功能：添加所有可用功能
    - 运行状况和诊断：添加所有可用功能
    - 安全性：
        - 基本身份验证
        - Windows 身份验证

5. 选择“Windows 进程激活服务”及其所有子项。

6. 对于“功能”，请选中“Microsoft .NET Framework 3.5.1”，并选中以下选项 ：

    - Windows Communication Foundation HTTP 激活
    - Windows Communication Foundation 非 HTTP 激活

7. 对于“功能”，请选中“Microsoft .NET Framework 4.6”，并选中以下选项 ：

   - 命名管道激活
   - TCP 激活
   - TCP 端口共享

     ![添加角色和功能向导：角色服务](media/01-demo-roles.png)

8. 选择所有选项后，单击“下一步”开始安装。

9. 添加 Windows 功能后，转到“控制面板”\>“系统和安全”\>“管理工具”，并选择“服务” 。 向下滚动，确保以下服务正在运行并设置为“自动”：

    - **NetTcpPortSharing**
    - **Net.Pipe 侦听器适配器**
    - **Net.tcp 侦听器适配器**

10. 若要配置 IIS 和 WAS 支持，请在菜单中找到并以管理员身份运行“Micro Focus Enterprise Developer 命令提示符(64 位)” 。

11. 键入 wassetup –i 并按 Enter 。

12. 脚本运行后，可以关闭窗口。

## <a name="configure-the-local-system-account-for-sql-server"></a>为 SQL Server 配置本地系统帐户

某些 Enterprise Server 进程需要能够登录到 SQL Server 并创建数据库和其他对象。 这些进程使用本地系统帐户，因此必须为该帐户授予 sysadmin 权限。

1. 启动 SSMS，然后单击“连接”以使用 Windows 身份验证连接到本地 SQLEXPRESS Server 。 “服务器名称”列表中应已显示该服务器。

2. 在左侧，展开“安全”文件夹并选择“登录名” 。

3. 依次选择“NT AUTHORITY\\SYSTEM”、“属性” 。

4. 选择“服务器角色”并选中“sysadmin” 。

     ![SSMS 对象资源管理器窗口：登录属性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>创建 BankDemo 数据库及其所有对象

1. 打开“Windows 资源管理器”，导航到“C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL” 。

2. 将 BankDemoCreateAll.SQL 文件的内容复制到剪贴板。

3. 打开“SSMS”。 在右侧，单击“服务器”并选择“新建查询” 。

4. 将剪贴板的内容粘贴到“新建查询”框中。

5. 在查询上方的“命令”选项卡中单击“执行”以执行 SQL 。

查询应会正常运行而不会出错。 完成后，你将获得 BankDemo 应用程序的示例数据库。

![SQLQuery1.sql 输出](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>验证是否已创建数据库表和对象

1. 右键单击“BANKDEMO”数据库并选择“刷新” 。

2. 展开“数据库”并选择“表” 。 你应会看到如下内容。

     ![BANKDEMO 表已在对象资源管理器中展开](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>在 Enterprise Developer 中生成应用程序

1. 打开 Visual Studio 并登录。

2. 在“文件”菜单选项下选择“打开项目/解决方案”，导航到“C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo”，然后选择“sln”文件   。

3. 请花点时间来检查对象。 COBOL 程序将连同 CBL 扩展以及 CopyBooks (CPY) 和 JCL 一起显示在解决方案资源管理器中。

4. 右键单击“BankDemo2”项目并选择“设为启动项目” 。

    > [!NOTE]
    > BankDemo 项目使用 HCOSS（SQL Server 的主机兼容性选项），但本演示不使用它。

5. 在“解决方案资源管理器”中，右键单击“BankDemo2”项目并选择“生成”  。

    > [!NOTE]
    > 在解决方案级别生成会导致错误，因为未配置 HCOSS。

6. 生成项目后，查看“输出”窗口。 它应该如下图所示。

     ![显示已成功生成的输出窗口](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>将 BankDemo 应用程序部署到区域数据库中

1. 以管理员身份打开 Enterprise Developer 命令提示符（64 位）。

2. 导航到“%PUBLIC%\\Documents\\Micro Focus\\Enterprise Developer\\samples\\Mainframe\\CICS\\DotNet\\BankDemo”。

3. 在命令提示符下，执行 bankdemodbdeploy 并包含要部署到的数据库的参数，例如：

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 请务必使用正斜杠 (/)，而不要使用反斜杠 (\\)。 此脚本将运行一段时间。

![管理：Enterprise Developer 命令提示符窗口](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在 Enterprise Administrator for .NET 中创建 BankDemo 区域

1. 打开“Enterprise Server for .NET Administration”UI。

2. 若要启动 MMC 管理单元，请从 Windows“开始”菜单中选择“Micro Focus Enterprise Developer”\>“配置”\>“Enterprise Server for .NET Admin”。（对于 Windows Server，请选择“Micro Focus Enterprise Developer”\>“Enterprise Server for .NET Admin”）。

3. 在左侧窗格中展开“区域”容器，然后右键单击“CICS” 。

4. 选择“定义区域”，以在（本地）数据库中创建名为 BANKDEMO 的新 CICS 区域 。

5. 提供数据库服务器实例，单击“下一步”，然后输入区域名称 BANKDEMO 。

     ![“定义区域”对话框](media/07-demo-cics.png)

6. 若要选择跨区域数据库的区域定义文件，请在“C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo”中找到“region\_bankdemo\_db.config” 。

     ![定义区域 - 区域名称：BANKDEMO](media/08-demo-cics.png)

7. 单击“完成”。

## <a name="create-xa-resource-definitions"></a>创建 XA 资源定义

1. 在“Enterprise Server for .NET Administration”UI 的左侧窗格中，依次展开“系统”、“XA 资源定义”  。 此设置定义区域如何与 Enterprise Server 和应用程序数据库互操作。

2. 右键单击“XA 资源定义”并选择“添加服务器实例” 。

3. 在下拉框中，选择“数据库服务实例”。 该实例将是本地计算机 SQLEXPRESS。

4. 选择“XA 资源定义(machinename\\sqlexpress)”容器中的实例，然后单击“添加” 。

5. 选择“数据库 XA 资源定义”，然后在“名称”和“区域”中键入 BANKDEMO   。

     ![“新建数据库 XA 资源定义”屏幕](media/09-demo-xa.png)

6. 单击省略号图标 (...) 打开“连接字符串”向导。 对于“服务器名称”，请键入 (local)\\SQLEXPRESS 。 对于“登录”，请选择“Windows 身份验证” 。 对于“数据库名称”，请键入 BANKDEMO

     ![“编辑连接字符串”屏幕](media/10-demo-string.png)

7. 测试连接。

## <a name="start-the-bankdemo-region"></a>启动 BANKDEMO 区域

> [!NOTE]
> 第一步非常重要：必须将区域设置为使用刚刚创建的 XA 资源定义。

1. 导航到“区域容器”下的“BANDEMO CICS 区域”，然后在“操作”窗格中选择“编辑区域启动文件”   。 向下滚动到“SQL 属性”，在“XA 资源名称”中输入 bankdemo，或使用省略号图标选择该名称 。

2. 单击“保存”图标保存更改。

3. 在“控制台”窗格中右键单击“BANKDEMO CICS 区域”，并选择“启动/停止区域”  。

4. 在中间窗格中显示的“启动/停止区域”框的底部，选择“启动” 。 几秒钟后，该区域即会启动。

     ![SQL“启动/停止”框](media/11-demo-sql.png)

     ![“CICS 区域 BANKDEMO - 已启动”屏幕](media/12-demo-cics.png)

## <a name="create-a-listener"></a>创建侦听器

为访问 BankDemo 应用程序的 TN3270 会话创建侦听器。

1. 在左侧窗格中，展开“配置编辑器”并选择“侦听器” 。

2. 单击“打开文件”图标，并选择“seelistener.exe.config”文件 。 将编辑此文件，每次启动 Enterprise Server 时都会加载它。

3. 注意先前定义的两个区域（ESDEMO 和 JCLDEMO）。

4. 若要为 BANKDEMO 创建新区域，请右键单击“区域”并选择“添加区域” 。

5. 选择“BANKDEMO 区域”。

6. 通过右键单击“BANKDEMO 区域”并选择“添加通道”来添加 TN3270 通道 。

7. 对于“名称”，请输入 TN3270 。 对于“端口”，请输入 9024 。 ESDEMO 应用程序使用端口 9230，因此你需要使用不同的端口。

8. 若要保存文件，请单击“保存”图标，或选择“文件”\>“保存”  。

9. 若要启动侦听器，请单击“启动侦听器”图标，或选择“选项”\>“启动侦听器”  。

     ![“侦听器配置编辑器”窗口](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>配置 Rumba 以访问 BankDemo 应用程序

需要执行的最后一个步骤是使用 Rumba（3270 仿真器）配置 3270 会话。 完成此步骤后，即可通过创建的侦听器访问 BankDemo 应用程序。

1. 在 Windows“开始”菜单中，启动 Rumba Desktop。

2. 在“连接”菜单项下，选择“TN3270” 。

3. 单击“插入”，键入 127.0.0.1 作为 IP 地址，键入 9024 作为用户定义的端口  。

4. 在对话框底部，单击“连接”。 此时会显示一个黑色 CICS 屏幕。

5. 键入 bank 显示 BankDemo 应用程序的初始 3270 屏幕。

6. 对于“用户 ID”，请键入 B0001；对于“密码”，请键入任何内容。 此时会打开第一个屏幕 BANK20。

![大型机显示画面 - 欢迎屏幕](media/14-demo.png)
![大型机显示画面 - Rumba - 子系统演示屏幕](media/15-demo.png)

祝贺你！ 现在，你正在使用 Micro Focus Enterprise Server 在 Azure 中运行一个 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

- [在 Azure 上的 Docker 容器中运行 Enterprise Server](run-enterprise-server-container.md)
- [大型机迁移 - 门户](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [虚拟机](../../../linux/overview.md)
- [故障排除](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [揭秘大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)