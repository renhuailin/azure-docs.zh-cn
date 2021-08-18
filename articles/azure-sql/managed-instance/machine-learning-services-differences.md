---
title: 机器学习服务的主要区别
description: 本文介绍 Azure SQL 托管实例中的机器学习服务与 SQL Server 机器学习服务之间的主要区别。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: mathoma, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 77637dc90bff07c96526fda421e872fc702f0ad9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751218"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL 托管实例和 SQL Server 中机器学习服务之间的主要差异

本文介绍 [Azure SQL 托管实例中的机器学习服务](machine-learning-services-overview.md)与 [SQL Server 机器学习服务](/sql/advanced-analytics/what-is-sql-server-machine-learning)在功能上的几个主要区别。

## <a name="language-support"></a>语言支持

SQL 托管实例和 SQL Server 中的机器学习服务都支持 Python 和 R [扩展性框架](/sql/machine-learning/concepts/extensibility-framework)。 SQL 托管实例中的主要区别是：

- 仅支持 Python 和 R。 无法添加 Java 等外部语言。

- Python 和 R 的初始版本不同：

  | 平台                   | Python 运行时版本           | R 运行时版本                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL 托管实例 | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 和 3.7.2（CU22 及更高版本） | 3.3.3 和 3.5.2（CU22 及更高版本）     |
  | SQL Server 2016            | 不可用                    | 3.2.2 和 3.5.2（SP2 CU14 及更高版本） |

## <a name="python-and-r-packages"></a>Python 包和 R 包

SQL 托管实例不支持依赖于外部运行时（例如 Java）或需要访问 OS API 才能安装或使用的包。

有关管理 Python 包和 R 包的详细信息，请参阅：

- [获取 Python 包信息](/sql/machine-learning/package-management/python-package-information?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&preserve-view=true&view=azuresqldb-mi-current)
- [获取 R 包信息](/sql/machine-learning/package-management/r-package-information?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&preserve-view=true&view=azuresqldb-mi-current)

## <a name="resource-governance"></a>资源调控

在 SQL 托管实例中，不可能通过 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true) 来限制 R 资源，并且不支持外部资源池。

默认情况下，启用扩展性后，R 资源最多设置为可用 SQL 托管实例资源的 20%。 若要更改此默认百分比，请在 [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) 中创建 Azure 支持票证。

扩展性通过以下 SQL 命令启用（SQL 托管实例将重启，并在几秒钟内不可用）：

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

若要禁用扩展性并将 100% 的内存和 CPU 资源还原到 SQL Server，请使用以下命令：

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

可供 SQL 托管实例使用的总资源数取决于你选择的服务层级。 有关详细信息，请参阅 [Azure SQL 数据库购买模型](../database/purchasing-models.md)。

### <a name="insufficient-memory-error"></a>内存不足错误

内存使用情况取决于 R 脚本中的使用量，以及正在执行的并行查询数。 如果没有足够的内存用于 R，你会收到一条错误消息。 常见的错误消息包括：

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

如果收到其中一项错误，可以通过将数据库缩放到更高的服务层级来解决该错误。

## <a name="sql-managed-instance-pools"></a>SQL 托管实例池

[Azure SQL 托管实例池（预览版）](instance-pools-overview.md)目前不支持机器学习服务。

## <a name="next-steps"></a>后续步骤

- 请参阅概述：[Azure SQL 托管实例中的机器学习服务](machine-learning-services-overview.md)。
- 若要了解如何在机器学习服务中使用 Python，请参阅[运行 Python 脚本](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。
- 若要了解如何在机器学习服务中使用 R，请参阅[运行 R 脚本](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。