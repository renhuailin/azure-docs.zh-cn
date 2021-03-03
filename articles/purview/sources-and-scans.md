---
title: 支持的数据源和文件类型
description: 本文提供了有关监控范围中支持的数据源和文件类型的概念详细信息。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677919"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure 监控范围中受支持的数据源和文件类型

本文介绍了监控范围中受支持的数据源、文件类型和扫描概念。

## <a name="supported-data-sources"></a>支持的数据源

Azure 监控范围支持以下源：

| 存储类型 | 支持的身份验证类型 | 通过 UX/PowerShell 设置扫描 |
| ---------- | ------------------- | ------------------------------ |
| 本地 SQL Server                   | SQL 身份验证                        | HP-UX                                |
| Azure Synapse Analytics（以前称为 SQL DW）            | SQL 身份验证，服务主体，MSI               | HP-UX                             |
| Azure SQL Database (DB)                   | SQL 身份验证，服务主体，MSI               | HP-UX |
| Azure SQL 数据库托管实例      | SQL 身份验证，服务主体，MSI               | HP-UX    |
| Azure Blob 存储                       | 帐户密钥、服务主体、MSI | HP-UX            |
| Azure 数据资源管理器                      | Service Principal                              | HP-UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1)  | 服务主体，MSI                              | HP-UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | 帐户密钥、服务主体、MSI            | HP-UX            |
| Azure Cosmos DB                          | 帐户密钥                                    | HP-UX            |


> [!Note]
> Azure Data Lake Storage Gen2 现已正式发布。 我们建议你立即开始使用它。 有关详细信息，请参阅[产品页](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)。

## <a name="file-types-supported-for-scanning"></a>支持扫描的文件类型

以下文件类型支持扫描，以便进行架构提取和分类（如果适用）：

- 扩展名支持的结构化文件格式： AVRO、ORC、PARQUET、CSV、JSON、PSV、SSV、TSV、TXT、XML、GZIP
- 扩展名支持的文档文件格式： DOC、DOCM、.DOCX、点、ODP、ODS、ODT、PDF、.POT、PPS、PPSX、PPT、PPTM、.PPTX、XLC、XLS、.XLSB、XLSM、.XLSX、.XLT
- 监控范围还支持自定义文件扩展名和自定义分析器。
 
> [!Note]
> 每个 Gzip 文件都必须映射到中的单个 csv 文件。 Gzip 文件服从系统和自定义分类规则。 目前，我们不支持扫描映射到中的多个文件的 gzip 文件或 csv 以外的任何文件类型。 

## <a name="sampling-within-a-file"></a>文件内的采样

在监控范围术语中，
- L1 扫描：提取基本信息和元数据（如文件名、大小和完全限定的名称）
- L2 扫描：提取结构化文件类型和数据库表的架构
- L3 扫描：在适用的情况下提取架构，并将采样文件与系统和自定义分类规则相关

对于所有结构化文件格式，监控范围扫描器会按以下方式对文件进行采样：

- 对于结构化文件类型，它会在每个列中采样128行或 1 MB，取两者中较低者。
- 对于文档文件格式，它将每个文件的大小设置为 20 MB。
    - 如果文档文件大于 20 MB，则不受深层扫描 (受制于分类) 。 在这种情况下，监控范围只捕获基本元数据（如文件名和完全限定名）。

## <a name="resource-set-file-sampling"></a>资源集文件采样

如果某个文件夹或分区文件组与系统资源集策略或客户定义的资源集策略相匹配，则会在监控范围中检测到该 *资源集* 。 如果检测到资源集，则监控范围将对它包含的每个文件夹进行采样。 [在此处](concept-resource-sets.md)了解资源集的详细信息。

资源集的文件采样（按文件类型）：

- 100文件中 **(CSV、PSV、SSV、TSV)** -1 的分隔文件在被视为 "资源集" 的一个或一组分区文件中采样 (L3 扫描) 
- **Data Lake 的文件类型 (Parquet、Avro、Orc)** -1，18446744073709551615 (长的最大) 文件会在被视为 *资源集* 的一个或一组分区文件中采样 (L3 扫描) 
- 100文件中 **(JSON、XML、TXT) 的其他结构化文件类型** 将在被视为 "资源集" 的一个或一组分区文件中 (L3 扫描) 进行采样
- **SQL 对象和 CosmosDB 实体** -每个文件都是 L3 扫描的。
- **文档文件类型** -每个文件都是 L3 扫描的。 资源集模式不适用于这些文件类型。

## <a name="scan-regions"></a>扫描区域
下面是监控范围扫描程序运行) 区域 (数据中心的所有 Azure 数据源的列表。 如果 Azure 数据源位于此列表之外的某个区域，则该扫描程序将在监控范围实例的区域中运行。
 
### <a name="purview-scanner-regions"></a>监控范围扫描仪区域

- EastUs
- EastUs2 
- Default-machinelearning-southcentralus
- WestUs
- WestUs2
- SoutheastAsia
- 西欧
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>分类

所有105系统分类规则都适用于结构化文件格式。 只有 MCE 分类规则适用于文档文件类型 (而不是数据扫描本机 regex 模式，布隆基于筛选器的检测) 。 有关支持的分类的详细信息，请参阅 [Azure 监控范围中支持的分类](supported-classifications.md)。

## <a name="next-steps"></a>后续步骤

- [教程：运行初学者工具包并扫描数据](tutorial-scan-data.md)
- [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md)