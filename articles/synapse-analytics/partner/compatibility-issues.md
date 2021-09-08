---
title: 与第三方应用程序和 Azure Synapse Analytics 的兼容性问题
description: 介绍第三方应用程序可能发现的 Azure Synapse 的已知问题
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: b5b6805e9ce39af71fd5e4592b1e51f0096e2452
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481389"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>与第三方应用程序和 Azure Synapse Analytics 的兼容性问题

专为 SQL Server 构建的应用程序可与 Azure Synapse 专用 SQL 池无缝配合使用。 但是，在某些情况下，SQL Server 中常用的功能和语言元素可能在 Azure Synapse 中不可用，或者它们的行为可能不同。

本文列出了将第三方应用程序与 Azure Synapse Analytics 配合使用时可能遇到的已知问题。 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau 错误：“完成事务的尝试已失败。 找不到相应的事务”

从 Azure Synapse 专用 SQL 池版本 10.0.11038.0 开始，某些包含存储过程调用的 Tableau 查询可能会失败，并出现以下错误消息：“[Microsoft][ODBC Driver 17 for SQL Server][SQL Server] 111214；尝试完成事务失败。找不到相应的事务。”

### <a name="cause"></a>原因

这是 Azure Synapse 专用 SQL 池中的一个问题，是由 ODBC 和 JDBC 驱动程序自动调用的新系统存储过程引入的。 如果这些系统存储过程执行失败，其中一个可能会导致打开的事务中止。 根据客户端应用程序逻辑，可能会发生此问题。

### <a name="solution"></a>解决方案
使用连接到 Azure Synapse 专用 SQL 池的 Tableau 时遇到此特定问题的客户应在 SQL 连接中将 FMTONLY 设置为“是”。 对于 Tableau Desktop 和 Tableau Server，应该使用 Tableau 数据源自定义 (TDC) 文件，确保 Tableau 将此参数传递给驱动程序。  

> [!NOTE] 
> Microsoft 不提供对第三方工具的支持。 虽然我们已经测试得出此解决方案适用于 Tableau Desktop 2020.3.2，但应根据自己的容量使用此解决方法。
>

* [要了解如何在 Tableau Desktop 上使用 TDC 文件进行全局自定义，请参阅 Tableau Desktop 文档。](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [要了解如何在 Tableau Server 上使用 TDC 文件进行全局自定义，请参阅“在 Tableau Server 上使用 .TDC 文件”。](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

下面的示例显示一个 Tableau TDC 文件，该文件将 FMTONLY=YES 参数传递到 SQL 连接字符串：

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
有关使用 TDC 文件的更多详细信息，请联系 Tableau 支持。 

## <a name="see-also"></a>另请参阅

* [Azure Synapse Analytics 中专用 SQL 池的 T-SQL 语言元素。](../sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements.md)
* [Azure Synapse Analytics 中专用 SQL 池支持的 T-SQL 语句。](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md)