---
title: 注册 Erwin Mart 并设置扫描
description: 本文概述如何在 Azure Purview 中注册 Erwin Mart 并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 8077658b644853f9ce0789a1d0863b3bde902818
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211515"
---
# <a name="register-and-scan-erwin-mart-server-preview"></a>注册并扫描 Erwin Mart 服务器（预览版）

本文概述如何在 Purview 中注册 Erwin Mart 服务器并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Erwin 源支持完全扫描，以从 erwin Mart 服务器中提取元数据。 元数据包括：

1.  具有实体、属性和域 OR 的仅逻辑模型
2.  具有表、列、数据类型 OR 的仅物理模型
3.  逻辑/物理模型

此数据源还会获取数据资产之间的世系。

> [!Note]
> erwin 作为当前预览版支持的源。 注册此源并在非生产 Purview 帐户中设置扫描，并向我们提供反馈。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅  
    [创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

    > [!Note]
    > 确保在运行 erwin Mart 实例的 VM 上运行自承载集成运行时。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在运行自承载集成运行时的 VM 上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装此更新，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载。

4.  支持的 erwin Mart 版本为 9.x - 2021

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

erwin Mart 源唯一支持的身份验证是用户名和密码形式的服务器身份验证

## <a name="register-an-erwin-mart"></a>注册 erwin Mart

若要在数据目录中注册新的 erwin Mart，请执行以下操作：

1. 导航到你的 Purview 帐户。
1. 在左侧导航区域中选择“数据映射”。
1. 选择“注册”
1. 在“注册源”上，选择“erwin”。 选择“继续”。
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="注册 erwin 源" border="true":::

在注册源 (erwin) 屏幕上，执行以下操作：

1.  输入数据源将在目录中列出的名称。

2.  输入 erwin Mart“服务器名称”。 这是用于连接到 erwin Mart 服务器的网络主机名， 例如 localhost

3.  输入连接到 erwin Mart 时使用的“端口”号。 默认情况下，此值为 18170。

4.  输入“应用程序名称”

    >[!Note]
    > 可以通过导航到 erwin Data Modeler 找到上述详细信息。 选择“Mart”-\>“连接”，查看与服务器名称、端口和应用程序名称相关的详细信息。

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="查找 erwin 详细信息" border="true":::
    
5.  选择一个集合或创建新集合（可选）

6.  选择“完成”以注册数据源。

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="注册源" border="true":::
    

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在管理中心，选择“集成运行时”。 确保在运行 erwin Mart 实例的 VM 上设置了自承载集成运行时。 如果尚未设置，请使用[此处](./manage-integration-runtimes.md)介绍的步骤来设置自承载集成运行时

2.  导航到“源”。

3.  选择已注册的 erwin Mart。

4.  选择“+ 新建扫描”。

5.  提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时。

    c.  “服务器名称、端口”和“应用程序名称”根据注册期间输入的值自动填充 。

    d.  **凭据：** 选择配置为用于连接 erwin Mart 服务器的凭据。 创建凭证时，请确保：
    - 选择“基本身份验证”作为身份验证方法
    - 在“用户名”字段中提供 erwin Mart 服务器的用户名。
    - 将用于服务器身份验证的用户密码保存在密钥保管库的机密中。

    若要进一步了解凭据，请参阅[此处](manage-credentials.md)的链接。

    e.  使用 Internet Information Services (IIS) - 通过选择“True”或“False”来指示在连接到 erwin Mart 服务器时是否必须使用 Microsoft Internet Information Services (IIS)。 默认情况下，此值设置为 False。

    f.  使用安全套接字层 (SSL) - 通过选择“True”或“False”来指示在连接到 erwin Mart 服务器时是否必须使用安全套接字层 (SSL)。 默认情况下，此值设置为 False。

    > [!Note]
    > 此参数仅适用于 erwin Mart 版本 9.1 或更高版本。

    g.  浏览模式 - 选择用于浏览 erwin Mart 的模式。 可能的选项是"库和模型"或"仅库"。

    h.  模型 - 通过提供以分号分隔的 erwin 模型定位符字符串列表来确定扫描范围。 例如 mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    i.  **最大可用内存**：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 erwin Mart 的大小。
    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="触发扫描" border="true":::
   

6.  选择“测试连接”。

7.  选择“继续”。

8.  选择你的扫描触发器。 可以设置一个计划或运行一次扫描。

9.  查看扫描，然后选择“保存并运行”。

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
