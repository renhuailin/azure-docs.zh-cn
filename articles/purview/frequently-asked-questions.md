---
title: 常见问题 (FAQ)
description: 本文解答了有关 Azure 监控范围的常见问题。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667809"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>常见问题 (常见问题解答) 有关 Azure 监控范围

## <a name="overview"></a>概述

许多组织对其数据缺乏全面的了解。 了解哪些数据存在、数据所在的位置，以及如何查找和访问相关数据是非常困难的。 数据缺少内容（如沿袭、分类和全面的元数据），使得业务用户难以搜索适当的数据并适当地使用数据。 因此，只使用一小部分收集的数据来通知商业决策。 最后，确定数据安全问题和保护敏感数据是不一致的。 它需要持续的时间和精力，特别是在保持数据灵活性的同时。

Azure 监控范围是一个数据管理解决方案。 它可帮助客户深入了解其所有数据，同时保持对其使用的控制。 借助 Azure 监控范围，组织将发现和组织数据。 它们可深入了解数据，并集中控制对数据的访问。

## <a name="purpose-of-this-faq"></a>本 FAQ 的目的

此常见问题回答了客户和现场团队经常询问的常见问题。 它旨在阐明有关 Azure 监控范围和相关解决方案的问题，例如 Azure 数据目录 (ADC) Gen 2 (弃用) 和 Azure 信息保护。

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>可用于元数据扫描和分类的源类型有哪些？

|Azure|非 Azure|
|---------|---------|
|Azure Blob 存储|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|2020)  (可用的 Teradata|
|Azure SQL 托管实例|SAP ECC (可通过 2020) 提供|
|Azure 数据资源管理器|SAP S/4 HANA (按 2020) 提供|
|Azure Data Lake Storage Gen1|Hive 元存储 (可通过 2020) 使用|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure 文件|--|
|Azure SQL 数据库|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>我们可以连接哪些数据系统/处理器并获取沿袭？

|数据系统/处理器 
|---------
|Azure 数据工厂：复制活动，数据流活动 
|自定义沿袭   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>ADC 第2代、Azure 信息保护和 Azure 监控范围如何相关？

Azure 监控范围最初以 ADC 第2代方式开始，但由于扩大了的作用域。 它现在包含 ADC 第2代的高级目录功能，以及 Azure 信息保护的数据分类、标签和合规性策略强制功能。 如今，它更接近数据管理的更广泛的行业定义。

### <a name="what-happens-to-customers-using-adc-gen-1"></a>使用 ADC 第1代客户会发生什么情况？

Azure 监控范围是 Microsoft 的目录解决方案空间中所有产品创新的重点。 将继续支持 ADC 第1代。

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>客户是否可以在同一订阅中拥有多个 Azure 监控范围帐户？

是的，我们支持每个订阅和租户的多个 Azure 监控范围帐户。

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>能否并行运行 ADC 第1代和 Azure 监控范围？

是的。 两者都是独立的服务。

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>如何实现将现有 ADC 第1代数据资产迁移到 Azure 监控范围？

使用 Azure 监控范围 Api 从 ADC 第1代提取并摄取到 Azure 监控范围。 对于术语表，我们支持基于 CSV 的批量工具。

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>如何实现使用 Azure 监控范围对 SQL 表的敏感数据进行加密？

数据加密是在数据源级别上完成的。 Azure 监控范围仅存储元数据。 它不会预览数据。

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>ADC 第2代的所有功能是否都存在于 Azure 监控范围中？

是的。

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>术语表和分类之间有何区别？

术语表使用命名约定，后面是数据的非技术/业务用户，也称为数据使用者。 这些类型的人员是使用 Azure 监控范围的业务分析人员或数据科学家，可根据业务使用情况搜索特定类型的数据。 例如，供应链分析师可能需要搜索 *SKU 类型* 和 *发货详细信息* 条款。 它们搜索术语表以查找相关数据。
分类是在表、列或文件级别应用于数据资产的标记，用于标识资产中存在的数据。 根据找到的数据类型，可以自动或手动应用分类。 通常，使用分类标记来确定资产是否包含敏感数据，以及可能的敏感数据的类型。

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure 监控范围是否在 Sharepoint 和 OneDrive 中扫描和分类电子邮件、Pdf 等？

通过 Azure 信息保护扫描程序来提供本地 SharePoint 站点和库扫描。 使用以下 Sku，可通过客户的 Microsoft 365 订阅使用该扫描程序： AIP P1、EMS E3 和 M365 E3。 如果你具有这些 Sku 中的任何一个，则应具有使用 Azure 信息保护扫描程序开始的正确权利。

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>用于扫描的计算有哪些？
存在 Microsoft 管理的扫描基础结构。 对于我们支持的大多数 Azure/AWS 资源，无需部署扫描基础结构。

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>是否有方法通过 Azure 资源管理器 (ARM) 模板/CLI/PowerShell 预配 Azure 监控范围？

是，ARM 模板可用

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>我已经在使用阿特拉斯，可以轻松地迁移到 Azure 监控范围？

Azure 监控范围与阿特拉斯 API 兼容。 如果要从阿特拉斯迁移，则建议先使用 Azure 监控范围扫描数据源。 在帐户中提供资产后，可以使用类似的阿特拉斯 Api 来集成，例如更新资产或添加自定义沿袭。 Azure 监控范围会将搜索 API 修改为使用 Azure 搜索，以便你应该能够使用高级搜索。

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>能否在我的租户中创建多个目录？

是的，可以为每个订阅和租户创建多个 Azure 监控范围帐户。 可以 [通过 Azure 监控范围查看限制页管理和增加资源的配额](how-to-manage-quotas.md)。

我们的 [Azure 监控范围部署最佳做法](deployment-best-practices.md)中记录了不应或不应具有多个帐户的其他建议。

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>能否在单个 Azure 监控范围帐户中注册多个租户？

否，当前要扫描其他租户的数据源，需要在该租户中创建单独的 Azure 监控范围帐户。

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure 监控范围是否支持列级沿袭？

是的，Azure 监控范围支持列级沿袭。