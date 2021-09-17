---
title: 在 Azure Purview 中将 Cassandra 注册为源并设置扫描
description: 本文概述如何在 Azure Purview 中注册 Cassandra 服务器并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 8/06/2021
ms.openlocfilehash: 8e2136cdb4cca53d81412335f7f5878fd8d70b86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783293"
---
# <a name="register-and-scan-a-cassandra-source"></a>注册并扫描 Cassandra 源 

本文概述如何在 Purview 中注册 Cassandra 服务器并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Cassandra 源支持完全扫描，以从 Cassandra 服务器中提取元数据，并在各数据资产之间提取世系。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装它，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载它。

4.  受支持的 Cassandra 服务器版本为 3.x 到 4.x

## <a name="register-a-cassandra-server"></a>注册 Cassandra 服务器

若要在数据目录中注册新的 Cassandra 服务器，请执行以下操作：

1.  导航到你的 Purview 帐户。
2.  在左侧导航区域中选择“数据映射”。
3.  选择“注册”。
4.  在“注册源”上，选择“Cassandra”。 选择“继续” **。** 
    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="注册 Cassandra 源" border="true":::
   
在“注册源(Cassandra)”屏幕上，执行以下操作：

1. 输入在目录中列出数据源时将使用的名称。

2. 在“主机”字段中输入 Cassandra 服务器在其中运行的服务器地址。 例如 20.190.193.10

3. 在“端口”字段中输入 Cassandra 服务器使用的端口。
4. 选择一个集合或创建新集合（可选）

5.  单击“注册”。
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="配置 Cassandra 源" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在“管理中心”，单击“集成运行时”。 确保设置了自承载集成运行时。 如果尚未设置，请使用[此处](./manage-integration-runtimes.md)介绍的步骤来设置自承载集成运行时

2.  导航到“源”。

3.  选择已注册的 Cassandra 服务器。

4.  选择“+ 新建扫描”。

5.  提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时

    c.  **凭据：** 配置 Cassandra 凭据时，请确保：

    - 选择“基本身份验证”作为身份验证方法
    - 在“用户名”字段中提供代表正在建立连接的用户的用户名。 
    - 保存代表该用户在密钥保管库的机密中建立连接的 Cassandra 用户的密码

    若要进一步了解凭据，请参阅[此处](manage-credentials.md)的链接。

    d.  **密钥空间**：指定要导入的 Cassandra 密钥空间的列表。 如果有多个密钥空间，必须用分号分隔。 例如 keyspace1;keyspace2。 当列表为空时，将导入所有可用的密钥空间。
    使用 SQL LIKE 表达式语法的可接受密钥空间名称模式包括使用 %， 

    例如：A%；%B；%C%；D
    - 以 A 开头或
    - 以 B 结尾或
    - 包含 C 或
    - 等于 D    
不能使用 NOT 和特殊字符。
    
    f. **使用安全套接字层 (SSL)** ：选择“True”或“False”来指示在连接到 Cassandra 服务器时是否必须使用安全套接字层 (SSL)。 默认情况下，此值设置为 False。

    g. **最大可用内存**：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 Cassandra 的大小。
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="扫描 Cassandra 源" border="true":::

6.  单击“测试连接”。

7.  单击“继续”。

8.  选择你的扫描触发器。 可以设置一个计划或运行一次扫描。

9.  查看你的扫描并单击“保存并运行”。

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