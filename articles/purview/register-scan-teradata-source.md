---
title: 在 Azure Purview 中注册 Teradata 源并设置扫描
description: 本文概述了如何在 Azure Purview 中注册 Teradata 源并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 3d6f30e3d50fa79ac394460f9b13e1127cf16168
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740265"
---
# <a name="register-and-scan-teradata-source"></a>注册并扫描 Teradata 源

本文概述了如何在 Purview 中注册 Teradata 源并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Teradata 源支持完全扫描，以从 Teradata 数据库中提取元数据，并在数据资产之间提取世系 。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建并配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装它，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载它。

4.  你必须在运行自承载集成运行时的虚拟机上手动下载 Teradata 的 JDBC 驱动程序。
    可执行的 JAR 文件可以从 Teradata [网站](https://downloads.teradata.com/)下载。

    > [!Note]
    > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装。

5.  支持的 Teradata 数据库版本为 12.x 至 16.x。 请确保对正在扫描的 Teradata 源具有读取权限。

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

对于 Teradata 源，唯一受支持的身份验证方法是基本身份验证。

## <a name="register-a-teradata-source"></a>注册 Teradata 源

若要在数据目录中注册新的 Teradata 源，请执行以下操作：

1.  导航到你的 Purview 帐户。
2.  在左侧导航区域中选择“数据映射”。
3.  选择“注册”
4.  在“注册源”上，选择“Teradata”。 选择“继续”

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="注册 Teradata 选项" border="true":::

在“注册源(Teradata)”屏幕上，执行以下操作：

1.  输入数据源将在目录中列出的名称。

2.  输入主机名以连接到 Teradata 源。 它也可以是服务器的 IP 地址或完全限定的连接字符串。

3.  选择一个集合或创建新集合（可选）

4.  选择“完成”以注册数据源。

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="注册 Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在“管理中心”，单击“集成运行时”。 确保设置了自承载集成运行时。 如果尚未设置，请使用[此处](./manage-integration-runtimes.md)介绍的步骤来设置自承载集成运行时

2.  在 Purview Studio 的左窗格中选择“数据映射”选项卡。

3.  选择已注册的 Teradata 源。

4.  选择“新建扫描”

5.  提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时。

    c.  **凭据**：选择用于连接到数据源的凭据。 请确保：

    -   在创建凭据时选择“基本身份验证”。
    -   在“用户名”输入字段中提供用于连接到数据库服务器的用户名
    -   将数据库服务器密码存储在密钥中。

        若要进一步了解凭据，请参阅[此处](./manage-credentials.md)的链接

6.  **架构**：列出要导入的架构的子集，以分号分隔的列表形式表示。 例如，schema1; schema2。 如果该列表为空，则会导入所有用户架构。 默认情况下，所有系统架构（例如 SysAdmin）和对象都将被忽略。 当列表为空时，将导入所有可用的架构。

    使用 SQL LIKE 表达式语法的可接受架构名称模式包括使用 %，例如 A%; %B; %C%; D
    - 以 A 开头或    
    - 以 B 结尾或    
    - 包含 C 或    
    - 等于 D

    不能使用 NOT 和特殊字符

7.  **驱动程序位置**：指定运行自承载集成运行时的 VM 中 JDBC 驱动程序位置的路径。 这应当是指向有效 JAR 文件夹位置的路径。

8.  **最大可用内存：** 客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 Teradata 源的大小。

    > [!Note] 
    > 根据经验，请为每 1000 张表提供 2GB 内存

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="设置扫描" border="true":::

6.  单击“继续”。

7.  选择你的扫描触发器。 可以设置一个计划或运行一次扫描。

8.  查看你的扫描并单击“保存并运行”。

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