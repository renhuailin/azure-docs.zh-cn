---
title: 在 Azure Purview 中注册 Looker 并设置扫描
description: 本文概述了如何在 Azure Purview 中注册 Looker 源并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: 903435ff45249d0c066b386d84ce060057bac666
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666571"
---
# <a name="register-and-scan-lookerpreview"></a>注册并扫描 Looker（预览）

本文概述了如何在 Purview 中注册 Looker 服务器并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Looker 源支持完全扫描，以从 Looker 服务器提取元数据。 它从 Looker 服务器导入元数据，包括数据库连接、LookML 模型和相关报表（Looks 和 Dashboards）。 此数据源还会获取数据资产之间的世系。

> [!Note]
> 在个人预览版中，当前支持将 Looker 作为源。 注册此源并在非生产 Purview 帐户中设置扫描，并向我们提供反馈。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在运行自承载集成运行时的 VM 上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装此更新，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载。

4.  支持的 Looker 服务器版本为 7.2

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

需要 API3 密钥才能连接到 Looker 服务器。 API3 密钥包含在公共 client_id 和专用 client_secret 中，并遵循 OAuth2 身份验证模式。

## <a name="register-a-looker-server"></a>注册 Looker 服务器

若要在数据目录中注册新的 Looker 服务器，请执行以下操作：

1. 导航到你的 Purview 帐户。
2. 在左侧导航区域中选择“源”。
3. 选择“注册”。
4. 在“注册源”上，选择“Looker”。 选择“继续”。
    :::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="注册 looker 源" border="true":::


在“注册源(Looker)”屏幕上，执行以下操作：

1. 输入在目录中列出数据源时将使用的名称。

2. 在“服务器 API URL”字段中输入 Looker API URL。 API 请求的默认端口是端口 19999。 此外，所有 Looker API 终结点都需要 HTTPS 连接。 例如“https://azurepurview.cloud.looker.com”

3. 选择一个集合或创建新集合（可选）

4. 选择“完成”以注册数据源。

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="扫描 looker 源" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在“管理中心”，单击“集成运行时”。 如果尚未设置，请使用[此处](./manage-integration-runtimes.md)介绍的步骤来设置自承载集成运行时

2. 导航到“源”。

3. 选择已注册的 Looker 服务器。

4. 选择“+ 新建扫描”。

5. 提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时。

    c.  “服务器 API URL”根据注册期间输入的值自动填充。

    d.  **凭据：** 配置 Looker 凭据时，请确保：

    - 选择“基本身份验证”作为身份验证方法
    - 在“用户名”字段中提供 Looker API3 密钥的客户端 ID
    - 将 Looker API3 密钥的客户端密码保存在密钥保管库的密钥中。

    注意：若要访问客户端 ID 和客户端密码，请导航到“Looker”-\>“管理员”-\>“用户”-\> 针对用户单击“编辑”-\> 单击“编辑密钥” -\> 使用客户端 ID 和客户端密码或创建新的客户端 ID 和客户端密码  。
    :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="获取 looker 详细信息" border="true":::
    

    若要进一步了解凭据，请参阅[此处](manage-credentials.md)的链接

    e.  **项目筛选器** - 通过提供以分号分隔的 Looker 项目列表来确定扫描范围。 此选项用于按其父项目选择 looks 和 dashboards。

    f.  **最大可用内存**：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 erwin Mart 的大小。

    :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="触发扫描" border="true":::

6. 单击“测试连接”。

7. 单击“继续”。

8. 选择你的扫描触发器。 可以设置一个计划或运行一次扫描。

9. 查看你的扫描并单击“保存并运行”。

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
