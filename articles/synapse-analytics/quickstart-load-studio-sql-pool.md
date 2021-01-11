---
title: 快速入门：使用专用 SQL 池大容量加载数据
description: 使用 Synapse Studio 将数据大容量加载到 Azure Synapse Analytics 中的专用 SQL 池中。
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 53b6810b0042df382fd1f553bc4bd0fae61793b3
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672791"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>快速入门：使用 Synapse Studio 进行大容量加载

使用 Synapse Studio 中的“大容量加载”向导可轻松加载数据。 Synapse Studio 是 Azure Synapse Analytics 的一项功能。 “大容量加载”向导将引导你使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)创建 T-SQL 脚本以将数据大容量加载到专用 SQL 池中。 

## <a name="entry-points-to-the-bulk-load-wizard"></a>“大容量加载”向导的入口点

可右键单击 Synapse Studio 中的以下区域来大容量加载数据：Azure 存储帐户中附加到工作区的文件或文件夹。

![显示右键单击存储帐户中的文件或文件夹的屏幕截图。](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>先决条件

- 此向导会生成一个COPY 语句，它使用 Azure Active Directory (Azure AD) 直通方式进行身份验证。 [Azure AD 用户必须](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication)至少具有 Azure Data Lake Storage Gen2 帐户的存储 Blob 数据参与者 Azure 角色才能访问工作区。 

- 若要创建一个新表来将数据加载到其中，必须具有 [COPY 语句的使用权限](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions)和“创建表”权限。

- 与 Data Lake Storage Gen2 帐户关联的链接服务必须能够访问要加载的文件或文件夹。 例如，如果链接服务的身份验证机制为“托管标识”，则工作区托管标识必须至少对存储帐户拥有存储 Blob 数据读取者权限。

- 如果在工作区上启用了虚拟网络，请确保与源数据和错误文件位置的 Data Lake Storage Gen2 帐户链接服务关联的集成运行时已启用交互式创作。 在向导中自动检测架构、预览源文件内容和浏览 Data Lake Storage Gen2 存储帐户需要交互式创作。

## <a name="steps"></a>步骤

1. 在“源存储位置”面板上，选择要从中加载数据的存储帐户以及文件或文件夹。 向导将自动尝试检测 Parquet 文件以及带分隔符的文本 (CSV) 文件，包括将源字段从文件映射到相应的目标 SQL 数据类型。 

   ![显示选择源位置的屏幕截图。](./sql/media/bulk-load/bulk-load-source-location.png)

2. 当大容量加载过程中存在拒绝的行时，请选择文件格式设置（包括错误设置）。 也可选择“预览数据”来了解 COPY 语句如何分析文件，以便帮助你配置文件格式设置。 每次更改文件格式设置时选择“预览数据”，以了解 COPY 语句将如何使用更新的设置来分析文件。

   ![显示预览数据的屏幕截图。](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - 大容量加载向导不支持预览带有多字符字段终止符的数据。 指定多字符字段终止符时，向导将预览单个列中的数据。 
   > - 选择“推断列名”时，大容量加载向导将分析“第一行”字段指定的第一行中的列名 。 大容量加载向导会自动将 COPY 语句中的 `FIRSTROW` 值加 1，以忽略此标头行。 
   > - COPY 语句支持指定多字符行终止符。 但是，大容量加载向导不支持该操作，并将引发错误。

3. 选择要用于加载的专用 SQL 池，包括选择该加载是针对现有表还是针对新表。
   ![显示选择目标位置的屏幕截图。](./sql/media/bulk-load/bulk-load-target-location.png)
4. 选择“配置列映射”，以确保具有适当的列映射。 请注意，如果启用了“推断列名”，将自动检测列名称。 就新表来说，配置列映射对于更新目标列数据类型至关重要。

   ![显示配置列映射的屏幕截图。](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. 选择“打开脚本”。 会生成一个 T-SQL 脚本，其中包含用于从数据湖加载数据的 COPY 语句。
   ![显示打开 SQL 脚本的屏幕截图。](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>后续步骤

- 有关 COPY 功能的详细信息，请查看 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)一文。
- 有关使用提取、转换和加载 (ETL) 过程的信息，请查看[数据加载概述](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)一文。
