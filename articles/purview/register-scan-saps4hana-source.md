---
title: 在 Azure Purview 中注册 SAP S/4HANA 源并设置扫描
description: 本文概述了如何在 Azure Purview 中注册 SAP S/4HANA 源并设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 811078d18d6659e600cb5b0931dcf2a217a8856e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751892"
---
# <a name="register-and-scan-a-sap-s4hana-source"></a>注册并扫描 SAP S/4HANA 源

本文概述了如何在 Purview 中注册 SAP S/4HANA 源并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

SAP S/4HANA 源支持完全扫描（用于从 SAP S/4HANA 实例中提取元数据），并在数据资产之间提取世系 。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装它，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载它。

4.  从 SAP 网站下载 64 位 [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)，并将其安装到自承载集成运行时计算机上。 安装期间，请在“可选安装步骤”窗口中确保选择“将程序集安装到 GAC”选项。  

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="先决条件" border="true":::

5.  连接器使用 [SAP Java Connector (JCo)](https://support.sap.com/en/product/connectors/jco.html) 3.0 API 从 SAP 中读取元数据。 因此，请确保可在安装了自承载集成运行时的虚拟机上使用 Java Connector。
    请确保使用适合环境的正确的 JCo 发行版。 例如，在 Microsoft Windows 计算机上，请确保 sapjco3.jar 和 sapjco3.dll 文件可用。

    > [!Note] 
    >VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装它。

6.  按照 [ABAP 函数部署指南](abap-functions-deployment-guide.md)中所述的步骤，在 SAP 服务器上部署元数据提取 ABAP 函数模块。 在 SAP 服务器上创建 RFC 函数模块需要使用 ABAP 开发人员帐户。 用户帐户需要有足够的权限来连接到 SAP 服务器并执行以下 RFC 函数模块：
    -   STFC_CONNECTION（检查连接性）
    -   RFC_SYSTEM_INFO（检查系统信息）

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

对于 SAP S/4HANA 源，唯一受支持的身份验证方法是基本身份验证

## <a name="register-sap-s4hana-source"></a>注册 SAP S/4HANA 源

若要在数据目录中注册新的 SAP S/4HANA 源，请执行以下操作：

1.  导航到你的 Purview 帐户。
2.  在左侧导航区域中选择“数据映射”。
3.  选择“注册”
4.  在“注册源”上，选择“SAP S/4HANA”。 选择“继续”

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="注册 SAP S/4HANA 选项" border="true":::

在“注册源(SAP S/4HANA)”屏幕上，执行以下操作：

1.  输入数据源将在目录中列出的名称。

2.  输入用于连接到 SAP S/4HANA 源的应用程序服务器名称。 它也可以是 SAP 应用程序服务器主机的 IP 地址。

3.  输入 SAP 系统编号。 这是一个 00 到 99 之间的两位数整数。

4.  选择一个集合或创建新集合（可选）

5.  选择“完成”以注册数据源。

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="注册 SAP S/4HANA" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在“管理中心”，单击“集成运行时”。 确保设置了自承载集成运行时。 如果未设置，请使用[此处](./manage-integration-runtimes.md)所述的步骤来创建自承载集成运行时。

2.  导航到“源”。

3.  选择已注册的 SAP S/4HANA 源。

4.  选择“+ 新建扫描”

5.  提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时。

    c.  **凭据**：选择用于连接到数据源的凭据。 请确保：

    -   在创建凭据时选择“基本身份验证”。
    -   在“用户名”输入字段中提供用于连接到 SAP 服务器的用户 ID。
    -   将用于连接到 SAP 服务器的用户密码存储在密钥中。

    d.  **客户端 ID：** 在此处输入 SAP 系统客户端 ID。 可以通过一个 000 到 999 之间的三位数数字来标识客户端。

    e.  **JCo 库路径**：指定 JCo 库所在的文件夹的路径。

    f.  **最大可用内存：** 客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 SAP S/4HANA 源的大小。
    > [!Note] 
    > 根据经验，每 1000 张表应提供 1GB 内存

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="扫描 SAP S/4HANA" border="true":::

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
