---
title: 在 Azure Purview 中注册 Hive 元存储数据库和安装程序扫描
description: 本文概述了如何在 Azure Purview 中注册 Hive 元存储数据库并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 7552b562e930e39ba4a0f848ba095762d3838c22
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214098"
---
# <a name="register-and-scan-hive-metastore-database"></a>注册并扫描 Hive 元存储数据库

本文概述了如何在 Purview 中注册 Hive 元存储数据库并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Hive 元存储源支持完全扫描，用于从 **Hive 元存储数据库** 中提取元数据，并在数据资产之间提取世系。 支持的平台为 Apache Hadoop、Cloudera、Hortonworks 和 Databricks。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装它，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载它。

4.  你必须在运行自承载集成运行时的虚拟机上手动下载 Hive 元存储数据库的 JDBC 驱动程序。 例如，如果使用的数据库为 mssql，请确保下载 Microsoft 用于 [SQL Server 的 JDBC 驱动程序](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

    > [!Note]
    > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装它。

5.  支持的 Hive 版本为 2.x 到 3.x。 支持的 Databricks 版本为 8.0 及更高版本。 

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

Hive 元存储数据库唯一支持的身份验证是 **基本身份验证。**

## <a name="register-a-hive-metastore-database"></a>注册 Hive 元存储数据库

若要在数据目录中注册新的 Hive 元存储数据库，请执行以下操作：

1.  导航到你的 Purview 帐户。

2.  在左侧导航区域中选择“数据映射”。

3.  选择“注册”

4.  在“注册源”中选择 Hive **元存储**。 选择“继续”。

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="注册 hive 源" border="true":::

在“注册源（Hive 元存储）”屏幕上，执行以下操作：

1.  输入数据源将在目录中列出的名称。

2.  输入 **Hive 群集 URL。** 群集 URL 可以从 Ambari URL 获取，或从 Databricks 工作区 URL 获取。 例如，hive.azurehdinsight.net 或 adb-19255636414785.5.azuredatabricks.net

3.  输入 **Hive 元存储 Server URL。** 例如，sqlserver://hive.database.windows.net 或 jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443

4.  选择一个集合或创建新集合（可选）

5.  选择“完成”以注册数据源。

       :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="配置 hive 源" border="true":::


## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在管理中心，选择“集成运行时”。 确保设置了自承载集成运行时。 如果尚未设置，请使用[此处](./manage-integration-runtimes.md)介绍的步骤来设置自承载集成运行时

2.  导航到“源”。

3.  选择已注册的 **Hive 元存储** 数据库。

4.  选择“+ 新建扫描”。

5.  提供以下详细信息：

    1. **名称**：扫描操作的名称

    1. **通过集成运行时进行连接**：选择已配置的自承载集成运行时。

    1. **凭据**：选择用于连接到数据源的凭据。 请确保：

       - 在创建凭据时选择“基本身份验证”。
       - 在“用户名”输入字段中提供元存储用户名
       - 将元存储密码存储在密钥中。

       若要进一步了解凭据，请参阅[此处](manage-credentials.md)的链接。 

       **Databricks 使用情况**：导航至你的 Databricks 群集 -> 应用程序 -> 启动 Web 终端。 运行 cmdlet **cat /databricks/hive/conf/hive-site.xml**

       可从如下所示的两个属性访问用户名和密码

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **元存储 JDBC 驱动程序位置**：指定运行自承载集成运行时的 VM 中 JDBC 驱动程序位置的路径。 这应当是指向有效 JAR 文件夹位置的路径。

       如果要扫描 Databricks，请参阅下方关于 Databricks 的章节。

       > [!Note]
       > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装。

    1. **元存储 JDBC 驱动程序类**：提供连接驱动程序类名称。 例如，\com.microsoft.sqlserver.jdbc.SQLServerDriver。
    
       **Databricks 使用情况**：导航至你的 Databricks 群集 -> 应用程序 -> 启动 Web 终端。 运行 cmdlet **cat /databricks/hive/conf/hive-site.xml**
    
       可从如下所示的属性访问驱动程序类。
    :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **元存储 JDBC URL**：提供连接 URL 值并定义与元存储 DB 服务器 URL 的连接。 例如 `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`。

       **Databricks 使用情况**：导航至你的 Databricks 群集 -> 应用程序 -> 启动 Web 终端。 运行 cmdlet **cat /databricks/hive/conf/hive-site.xml**
    
       可以从如下所示的“连接 URL 属性”访问 JDBC URL。
       
       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::
    
       > [!NOTE]
       > 从 *hive-site.xml* 复制 URL 时，务必从字符串中删除 `amp;`，否则扫描将失败。

       对于此 URL，将路径附加到 VM 上的 SSL 证书所在的位置。 可从[此处](../mysql/howto-configure-ssl.md)下载 SSL 证书。

       元存储 JDBC URL 将为：
    
       `jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&amp;useSSL=true&sslCA=D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem`

    1. **元存储数据库名称**：提供 Hive 元存储数据库名称。
    
       如果要扫描 Databricks，请参阅下方关于 Databricks 的章节。

       **Databricks 使用情况**：导航至你的 Databricks 群集 -> 应用程序 -> 启动 Web 终端。 运行 cmdlet **cat /databricks/hive/conf/hive-site.xml**

       可以从如下所示的“JDBC URL 属性”访问数据库名称。 例如：organization1829255636414785
       
       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    1. **架构**：指定要导入的 Hive 架构的列表。 例如：schema1; schema2。 
    
        如果该列表为空，则会导入所有用户架构。 默认情况下，所有系统架构（例如 SysAdmin）和对象都将被忽略。 

        当列表为空时，将导入所有可用的架构。 使用 SQL LIKE 表达式语法的可接受架构名称模式包括使用 %，例如 A%; %B; %C%; D

        - 以 A 开头或    
        - 以 B 结尾或    
        - 包含 C 或    
        - 等于 D

        不能使用 NOT 和特殊字符。

     1. **最大可用内存**：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 Hive 元存储数据库的大小。

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="扫描 hive 源" border="true":::

6.  选择“继续”。

7.  选择你的扫描触发器。 可以设置一个计划或运行一次扫描。

8.  查看扫描，然后选择“保存并运行”。

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
