---
title: 注册 Google BigQuery 项目和在 Azure Purview 中设置扫描
description: 本文概述了如何在 Azure Purview 中注册 Google BigQuery 项目和设置扫描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: caaf78e14669d67f525e5756efd8e2fd301f9b38
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218523"
---
# <a name="register-and-scan-google-bigquery-source-preview"></a>注册和扫描 Google BigQuery 源（预览版）

本文概述了如何在 Purview 中注册 Google BigQuery 项目和设置扫描。

## <a name="supported-capabilities"></a>支持的功能

BigQuery 源支持完全扫描，以从 BigQuery 项目中提取元数据，并在数据资产之间提取数据世系。

## <a name="prerequisites"></a>先决条件

1.  设置最新的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。
    有关详细信息，请参阅[创建并配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

2.  确保在安装了自承载集成运行时的虚拟机上安装 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)。

3.  确保在自承载集成运行时计算机上安装了“Visual C++ 可再发行程序包 2012 更新 4”。 如果尚未安装它，请在[此处](https://www.microsoft.com/download/details.aspx?id=30679)下载它。

4.  你必须通过[此处](https://cloud.google.com/bigquery/providers/simba-drivers)在运行自承载集成运行时的虚拟机上手动下载 BigQuery 的 JDBC 驱动程序

    > [!Note]
    > VM 中的所有帐户均应可以访问该驱动程序。 请不要在用户帐户中安装它。

5.  支持的 Google BigQuery 版本为 11.0.0

## <a name="register-a-google-bigquery-project"></a>注册 Google BigQuery 项目

若要在数据目录中注册新的 Google BigQuery 项目，请执行以下操作：

1.  导航到你的 Purview 帐户。
2.  在左侧导航区域中选择“数据映射”。
3.  选择“注册”。
4.  在“注册源”中选择 Google BigQuery。 选择“继续” **。** 
    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="注册 BigQuery 源" border="true":::
   
在“注册源 (Google BigQuery)”屏幕上，执行以下操作：

1.  输入数据源将在目录中列出的名称。

2.  输入 ProjectID。 这应该是完全限定的项目 Id。例如，mydomain.com:myProject

3.  选择一个集合或创建新集合（可选）

4.  选择“注册”  。
    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="配置 BigQuery 源" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1.  在管理中心，选择“集成运行时”。 确保设置了自承载集成运行时。 如果尚未设置，请使用[此处](./manage-integration-runtimes.md)介绍的步骤来设置自承载集成运行时

2.  导航到“源”。

3.  选择已注册的 BigQuery 项目。

4.  选择“+ 新建扫描”。

5.  提供以下详细信息：

    a.  **名称**：扫描操作的名称

    b.  **通过集成运行时进行连接**：选择已配置的自承载集成运行时

    c.  凭据：配置 BigQuery 凭据时，请确保：

    - 选择“基本身份验证”作为身份验证方法
    - 在“用户名”字段中提供服务帐户的电子邮件 ID。 例如 xyz\@developer.gserviceaccount.com
    - 将服务帐户的私钥文件以 JSON 格式保存在密钥保管库的机密中

    若要从 Google 云平台创建新私钥，请在导航菜单中选择“IAM 和管理员”-\>“服务帐户”-\> 选择一个项目 -\> 选择要为其创建密钥的服务帐户的电子邮件地址 -\> 选择“密钥”选项卡 -\> 选择“添加密钥”下拉菜单，然后选择“创建新密钥” 。 现在，选择“JSON 格式”。

      > [!Note]
      > 当扫描进程正在运行时，私钥的内容会保存在 VM 上的临时文件中。 成功完成扫描后，系统将删除此临时文件。 如果扫描失败，系统将继续重试，直到成功。 请确保在运行 SHIR 的 VM 上适当限制访问权限。**

    若要进一步了解凭据，请参阅[此处](manage-credentials.md)的链接。

    d.  **驱动程序位置**：指定运行自承载集成运行时的 VM 中 JDBC 驱动程序位置的路径。 这应当是指向有效 JAR 文件夹位置的路径    
    > [!Note]
    > VM 中的所有帐户均应可以访问该驱动程序。请勿在用户帐户中安装。

    e.  数据集：指定要导入的 BigQuery 数据集列表。 例如 dataset1、dataset2。 当列表为空时，所有可用的数据集均将导入。
        使用 SQL LIKE 表达式语法的可接受数据集名称模式包括使用 %， 

    例如：A%；%B；%C%；D
    - 以 A 开头或
    - 以 B 结尾或
    - 包含 C 或
    - 等于 D    
不能使用 NOT 和特殊字符。
    
    f.  **最大可用内存**：客户 VM 上可供扫描进程使用的最大内存 (GB)。 这取决于要扫描的 Google BigQuery 项目的大小。
        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="扫描 BigQuery 源" border="true":::

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