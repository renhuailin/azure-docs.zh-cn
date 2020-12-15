---
title: 注册 Teradata 源并设置扫描（预览版）
description: 本文概述了如何在 Azure Purview 中注册 Teradata 源并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841623"
---
# <a name="register-and-scan-teradata-source-preview"></a>注册并扫描 Teradata 源（预览版）

本文概述了如何在 Purview 中注册 Teradata 源并设置扫描。

> [!IMPORTANT]
> 此数据源目前以预览版提供。 欢迎试用并提供反馈。

## <a name="supported-capabilities"></a>支持的功能

Teradata 源支持完全扫描，以从 Teradata 数据库中提取元数据，并在数据资产之间提取世系 。

## <a name="prerequisites"></a>先决条件

- 连接器支持仅位于本地网络、Azure 虚拟网络或 Amazon 虚拟私有云内部的数据存储。 因此，需要设置[自承载集成运行时](manage-integration-runtimes.md)才能连接到该连接器。

- 确保在安装了自承载集成运行时的虚拟机上安装了 Java Runtime Environment (JRE)。
 
- 确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新4”。 如果尚未安装，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载。

- 必须在本地虚拟机上手动安装名为 Teradata JDBC Driver 的驱动程序。 可执行的 JAR 文件可以从 [Teradata 网站](https://downloads.teradata.com/)下载。

    > [!Note] 
    > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装。

- 支持的 Teradata 数据库版本为 12.x 至 16.x。 请确保对正在扫描的 Teradata 源具有读取权限。

### <a name="feature-flag"></a>功能标志

功能标志后面提供了 Teradata 源的注册和扫描功能。 将以下内容附加到 URL：?feature.ext.datasource=%7b"teradata":"true"%7d 

例如完整的 URL [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证
为 Teradata 源设置身份验证的唯一支持方法是基本数据库身份验证

## <a name="register-a-teradata-source"></a>注册 Teradata 源

若要在数据目录中注册新的 Teradata 源，请执行以下操作：

1. 导航到你的 Purview 帐户
2. 在左侧导航区域中选择“源”
3. 选择“注册”
4. 在“注册源”上，选择“Teradata” 
5. 选择“继续”

在“注册源(Teradata)”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
2. 输入主机名以连接到 Teradata 源。 它也可以是服务器的 IP 地址或完全限定的连接字符串。
3. 选择一个集合或创建新集合（可选）
4. 选择“完成”以注册数据源。

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：
1. 在“管理中心”，单击“集成运行时”。 确保设置了自承载集成运行时。 如果未设置，请使用[此处](manage-integration-runtimes.md)中所述的步骤创建一个自承载集成运行时，以便在可以访问本地网络的本地或 Azure VM 上进行扫描。

2. 接下来，导航到“源”

3. 选择已注册的 Teradata 源。

4. 选择“+ 新建扫描”
 
5. 提供以下详细信息：

    - 姓名：扫描的名称

    - 通过集成运行时进行连接：选择已配置的自承载集成运行时

    - 身份验证方法：目前仅支持数据库身份验证方法。 默认情况下将选择此方法

    - 用户名：用于连接到数据库服务器的用户名。 此用户名应具有对服务器的读取权限

    - 密码：用于连接数据库服务器的用户密码

    - 架构：列出要导入的架构的子集，以分号分隔的列表形式表示。 例如 schema1; schema2。 如果该列表为空，则导入所有用户架构。 默认情况下，所有系统架构（例如 SysAdmin）和对象都将被忽略。

        使用 SQL LIKE 表达式语法的可接受架构名称模式包括使用 %，例如 A%; %B; %C%; D
        - 以 A 开头或    
        - 以 B 结尾或    
        - 包含 C 或    
        - 等于 D

        不能使用 NOT 和特殊字符

    - 驱动程序位置：客户 VM 上 Teradata 驱动程序位置的完整路径。 Teradata JDBC 驱动程序名称必须为 com.teradata.jdbc.TeraDriver

    - 最大可用内存：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 Teradata 源的大小。

    > [!Note] 
    > 根据经验，请为每 1000 张表提供 2GB 内存

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="设置扫描" border="true":::

6. 单击“继续”。

7. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="扫描触发器" border="true":::

8. 查看扫描并单击“保存并运行”。

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
