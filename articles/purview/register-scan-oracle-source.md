---
title: 在 Azure Purview 中注册 Oracle 源并设置扫描
description: 本文概述了如何在 Azure Purview 中注册 Oracle 源并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 1a8956971e48529c75f07db54c196867a6c5955e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216930"
---
# <a name="register-and-scan-oracle-source"></a>注册并扫描 Oracle 源

本文概述了如何在 Purview 中注册 Oracle 数据库并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Oracle 源支持完全扫描（用于从 Oracle 数据库中提取元数据），并在数据资产之间提取世系 。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装它，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载它。

4.  在运行自承载集成运行时的虚拟机上，你必须从[此处](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)手动下载 Oracle JDBC 驱动程序。

    > [!Note] 
    > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装它。

5.  支持的 Oracle 数据库版本为 6i 到 19c。

6.  用户权限：需要对系统表具有只读访问权限。 用户应当具有创建会话的权限并且应当分配有 SELECT\_CATALOG\_ROLE 角色。 另外，还可以针对此连接器从中查询元数据的每个单独系统表向用户授予 SELECT 权限：
       > grant create session to \[user\];\
        grant select on all\_users to \[user\];\
        grant select on dba\_objects to \[user\];\
        grant select on dba\_tab\_comments to \[user\];\
        grant select on dba\_external\_locations to \[user\];\
        grant select on dba\_directories to \[user\];\
        grant select on dba\_mviews to \[user\];\
        grant select on dba\_clu\_columns to \[user\];\
        grant select on dba\_tab\_columns to \[user\];\
        grant select on dba\_col\_comments to \[user\];\
        grant select on dba\_constraints to \[user\];\
        grant select on dba\_cons\_columns to \[user\];\
        grant select on dba\_indexes to \[user\];\
        grant select on dba\_ind\_columns to \[user\];\
        grant select on dba\_procedures to \[user\];\
        grant select on dba\_synonyms to \[user\];\
        grant select on dba\_views to \[user\];\
        grant select on dba\_source to \[user\];\
        grant select on dba\_triggers to \[user\];\
        grant select on dba\_arguments to \[user\];\
        grant select on dba\_sequences to \[user\];\
        grant select on dba\_dependencies to \[user\];\
        grant select on dba\_type\_attrs to \[user\];\
        grant select on V\_\$INSTANCE to \[user\];\
        grant select on v\_\$database to \[user\];
    
## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

对于 Oracle 源，唯一受支持的身份验证方法是基本身份验证。

## <a name="register-an-oracle-source"></a>注册 Oracle 源

若要在数据目录中注册新的 Oracle 源，请执行以下操作：

1.  导航到你的 Purview 帐户。
2.  在左侧导航区域中选择“数据映射”。
3.  选择“注册”
4.  在“注册源”上，选择“Oracle”。 选择“继续”。

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="注册源" border="true":::

在“注册源(Oracle)”屏幕上，执行以下操作：

1.  输入数据源将在目录中列出的名称。

2.  输入主机名以连接到 Oracle 源。 这可以是：
    - JDBC 用于连接到数据库服务器的主机名。 例如，MyDatabaseServer.com 或
    - IP 地址。 例如，192.169.1.2 或
    - 其完全限定的 JDBC 连接字符串。 例如，\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD\_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT\_DATA=(SERVICE\_NAME=orcl)))

3.  输入 JDBC 用于连接到数据库服务器的端口号（对于 Oracle，默认为 1521）。

4.  输入 JDBC 用于连接到数据库服务器的 Oracle 服务名称。

5.  选择一个集合或创建新集合（可选）

6.  选择“完成”以注册数据源。

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在管理中心，选择“集成运行时”。 确保设置了自承载集成运行时。 如果未设置，请使用[此处](./manage-integration-runtimes.md)所述的步骤来创建自承载集成运行时。

2.  导航到“源”。

3.  选择已注册的 Oracle 源。

4.  选择“+ 新建扫描”。

5.  提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时

    c.  **凭据**：选择用于连接到数据源的凭据。 请确保：
    - 在创建凭据时选择“基本身份验证”。        
    - 在“用户名”输入字段中提供 JDBC 用于连接到数据库服务器的用户名        
    - 将 JDBC 用于连接到数据库服务器的用户密码存储在密钥中。

    d.  **架构**：列出要导入的架构的子集，以分号分隔的列表形式表示。 例如：schema1; schema2。 如果该列表为空，则会导入所有用户架构。 默认情况下，所有系统架构（例如 SysAdmin）和对象都将被忽略。 当列表为空时，将导入所有可用的架构。
        使用 SQL LIKE 表达式语法的可接受架构名称模式包括使用 %，例如： A%; %B; %C%; D
       -   以 A 开头或        
       -   以 B 结尾或        
       -   包含 C 或        
       -   等于 D

    不能使用 NOT 和特殊字符。

6.  **驱动程序位置**：指定运行自承载集成运行时的 VM 中 JDBC 驱动程序位置的路径。 这应当是指向有效 JAR 文件夹位置的路径。
    > [!Note]
    > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装。

7.  **最大可用内存**：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 SAP S/4HANA 源的大小。

    > [!Note] 
    > 根据经验，每 1000 张表应提供 1GB 内存

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="扫描 oracle" border="true":::

8.  选择“继续”。

9.  选择你的扫描触发器。 可以设置一个计划或运行一次扫描。

10.  查看扫描，然后选择“保存并运行”。

## <a name="viewing-your-scans-and-scan-runs"></a>查看扫描和扫描运行

1. 导航到管理中心。 在“源和扫描”部分下，选择“数据源” 。

2. 选择所需的数据源。 你将看到该数据源的现有扫描列表。

3. 选择想要查看其结果的扫描。

4. 此页将向你展示先前的所有扫描运行，以及每次扫描运行的指标和状态。 它还将展示扫描是按计划执行还是手动执行、已应用分类的资产数量、发现的总资产数量、扫描的开始和结束时间，以及扫描总持续时间。

## <a name="manage-your-scans"></a>管理扫描

若要管理或删除扫描，请执行以下操作：

1. 导航到管理中心。 在“源和扫描”部分下，选择“数据源”，然后选择所需的数据源 。

2. 选择要管理的扫描。 可以通过选择“编辑”来编辑扫描。

3. 可以通过选择“删除”来删除扫描。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)