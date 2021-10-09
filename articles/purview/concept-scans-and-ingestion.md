---
title: 扫描和引入
description: 本文介绍了 Azure Purview 中的扫描和引入。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 42162519e9e8f3835498d8955adbd7c254775dd9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211708"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Azure Purview 中的扫描和引入

本文概述了 Azure Purview 中的扫描和引入功能。 使用这些功能，你可以将 Purview 帐户连接到源来填充数据映射和数据目录，以便通过 Purview 开始浏览和管理数据。

## <a name="scanning"></a>扫描

在 Purview 帐户中[注册](manage-data-sources.md)数据源后，下一步是扫描数据源。 在扫描过程中会与数据源建立连接，并捕获技术元数据，例如名称、文件大小、列，等等。 它还会提取结构化数据源的架构，对架构应用分类，并[在你的 Purview 帐户连接到 Microsoft 365 安全与合规中心 (SCC) 的情况下应用敏感度标签](create-sensitivity-label.md)。 扫描过程可以在触发后立即运行，也可以按计划定期运行，使你的 Purview 帐户保持最新。

对于每次扫描，你可以应用自定义项，以便仅在源中扫描所需信息。

### <a name="choose-an-authentication-method-for-your-scans"></a>选择用于扫描的身份验证方法

默认情况下，Purview 是安全的。 不会直接将密码或机密存储在 Purview 中，因此你需要为您的来源选择一种身份验证方法。 有四种可能的方法来验证你的 Purview 帐户，但并非每个数据源都支持所有方法。
 - 托管标识
 - Service Principal
 - SQL 身份验证
 - 帐户密钥或基本身份验证

尽可能首选托管身份作为身份验证方法，因为这无需存储和管理各个数据源的凭据。 这可以大大减少你和你的团队在扫描设置和排除身份验证方面花费的时间。 为 Purview 帐户启用托管标识时，会在 Azure Active Directory 中创建一个标识，并与帐户的生命周期相关联。 

### <a name="scope-your-scan"></a>限定扫描范围

扫描某个源时，可以选择扫描整个数据源，或选择仅扫描特定实体（文件夹/表）。 可用选项取决于所扫描的源，可以针对一次性扫描和计划的扫描进行定义。

例如，[针对 Azure SQL 数据库创建和运行扫描](register-scan-azure-sql-database.md#creating-and-running-a-scan)时，可以选择要扫描的表，或选择整个数据库。

### <a name="scan-rule-set"></a>扫描规则集

扫描规则集决定了针对源之一运行扫描时扫描将查找的信息的类型。 可用规则取决于要扫描的源的类型，但包括应扫描的[文件类型](sources-and-scans.md#file-types-supported-for-scanning)以及需要的[分类](supported-classifications.md)类型等项。

许多数据源类型已存在可用的[系统扫描规则集](create-a-scan-rule-set.md#system-scan-rule-sets)，但是你也可以[创建自己的扫描规则集](create-a-scan-rule-set.md)来为组织定制扫描。

### <a name="schedule-your-scan"></a>计划扫描

Purview 允许你按照自己选择的特定时间每周或每月进行扫描。 每周扫描可能适用于结构正在积极开发或经常更改的数据源。 每月扫描更适用于不经常更改的数据源。 一种最佳做法是与你要扫描的源的管理员合作来确定在什么时间对源的计算需求较低。

### <a name="how-scans-detect-deleted-assets"></a>扫描如何检测已删除的资产

Azure Purview 目录只有在扫描数据存储后才会知道数据存储的状态。 为了解文件、表或容器是否已删除，它会将上次扫描输出与当前扫描输出进行比较。 例如，假设上次扫描 Azure Data Lake Storage Gen2 帐户时，包含一个名为 folder1 的文件夹。 再次扫描同一帐户时，folder1 缺失。 因此，目录假定该文件夹已被删除。

#### <a name="detecting-deleted-files"></a>检测已删除的文件

用于检测缺失文件的逻辑适用于同一个用户以及不同用户的多次日志扫描。 例如，假设某个用户对 Data Lake Storage Gen2 数据存储上的文件夹 A、B 和 C 运行一次扫描。稍后，同一帐户中的不同用户对同一数据存储的 C、D 和 E 文件夹运行一次不同的一次性扫描。 由于文件夹 C 被扫描了两次，因此目录会检查它是否可能被删除。 然而，文件夹 A、B、D 和 E 只扫描一次，并且目录不会将其检查已删除的资产。

为了将已删除的文件从目录中移除，请务必定期运行扫描。 扫描间隔很重要，因为在运行另一次扫描之前，目录无法检测到已删除的资产。 因此，如果你每月对特定存储运行一次扫描，则在运行下一次扫描之前，目录无法检测该存储中的任何已删除的数据资产。

枚举大数据存储（如 Data Lake Storage Gen2）时，有多种方法（包括枚举错误和丢弃的事件）丢失信息。 特定扫描可能会错过已创建或已删除的文件。 因此，除非目录确定某个文件已被删除，否则不会从目录中删除这个文件。 这一策略的意义是，如果某个文件在扫描的数据存储中不存在，但仍存在于目录中，则可能会出现错误。 在某些情况下，可能需要对数据存储进行两次或三次扫描才能捕获某些已删除的资产。

> [!NOTE]
> 成功扫描后将删除标记为删除的资产。 已删除的资产在被处理和删除之前，可能会继续在你的目录中显示一段时间。

## <a name="ingestion"></a>引流

然后，扫描过程识别出的技术元数据或分类将被发送到“引入”。 引入过程负责填充数据映射，由 Purview 管理。  引入会分析来自扫描的输入，[应用资源集模式](concept-resource-sets.md#how-azure-purview-detects-resource-sets)，填充可用的[数据世系](concept-data-lineage.md)信息，然后自动加载数据映射。 只有在引入完成后，才能发现或策展资产/架构。 因此，如果扫描已完成，但你未在数据映射或目录中看到资产，则需要等待引入过程完成。

## <a name="next-steps"></a>后续步骤

有关对源进行扫描的详细信息或特定说明，请点击以下链接。

* 若要了解资源集，请参阅我们的[资源集文章](concept-resource-sets.md)。
* [如何注册并扫描 Azure SQL 数据库](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
