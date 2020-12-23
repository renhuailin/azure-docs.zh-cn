---
title: '机器学习服务 (预览版的主要差异) '
description: 本文介绍 Azure SQL 托管实例和 SQL Server 机器学习服务中机器学习服务之间的主要差异。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324527"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL 托管实例和 SQL Server 中机器学习服务之间的主要差异

[AZURE SQL 托管实例 (预览版) 中机器学习服务](machine-learning-services-overview.md)的功能与[SQL Server 机器学习服务](/sql/advanced-analytics/what-is-sql-server-machine-learning)几乎完全相同。 下面是一些重要的差异。

> [!IMPORTANT]
> Azure SQL 托管实例中的机器学习服务目前为公共预览版。 若要注册，请参阅 [注册预览](machine-learning-services-overview.md#signup)。

## <a name="preview-limitations"></a>预览版限制

在预览版中，服务存在以下限制：

- 环回连接不起作用 (参阅 [从 Python 或 R 脚本) 中 SQL Server 的环回连接](/sql/machine-learning/connect/loopback-connection) 。
- 不支持外部资源池。
- 仅支持 Python 和 R。 无法添加 Java 等外部语言。
- 不支持使用 [消息传递接口](/message-passing-interface/microsoft-mpi) (MPI) 的方案。

如果服务级别目标 (SLO) 更新，请更新 SLO 并发出支持票证，以重新启用 R/Python 的专用资源限制。

## <a name="language-support"></a>语言支持

机器学习服务 SQL 托管实例和 SQL Server 支持 Python 和 R [扩展性框架](/sql/advanced-analytics/concepts/extensibility-framework)。 主要区别包括：

- 版本的 Python 和 R 的初始版本不同于 SQL 托管实例和 SQL Server 中机器学习服务：

  | 系统               | Python | R     |
  |----------------------|--------|-------|
  | SQL 托管实例 | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- 无需通过 `sp_configure` 配置 `external scripts enabled`。 [注册](machine-learning-services-overview.md#signup)预览版后，会为 Azure SQL 托管实例启用机器学习。

## <a name="packages"></a>包

在 SQL 托管实例和 SQL Server 中，Python 和 R 包管理的工作方式有所不同。 差异为：

- 不支持依赖于外部运行时（例如 Java）的程序包，也不支持需要访问 OS API 才能安装或使用的程序包。
- 包可以执行 (从预览) 之前的更改的出站网络调用。 可以在 [网络安全组](../../virtual-network/network-security-groups-overview.md) 级别设置正确的出站安全规则，以启用出站网络调用。

有关管理 Python 和 R 包的详细信息，请参阅：

- [获取 Python 包信息](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [获取 R 包信息](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>资源调控

不能通过 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 和外部资源池来限制 R 资源。

在提供公共预览版期间，R 资源最多可设置为 SQL 托管实例资源的 20%，并且取决于所选的服务层级。 有关详细信息，请参阅 [Azure SQL 数据库购买模型](../database/purchasing-models.md)。

### <a name="insufficient-memory-error"></a>内存不足错误

如果没有足够的内存可用于 R，将收到一条错误消息。 常见的错误消息包括：

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

内存使用情况取决于 R 脚本中的使用量，以及正在执行的并行查询数。 如果收到上述错误，可以将数据库扩展到更高的服务层级以解决此问题。

## <a name="next-steps"></a>后续步骤

- 请参阅 [AZURE SQL 托管实例中](machine-learning-services-overview.md)的概述机器学习服务。
- 若要了解如何在机器学习服务中使用 Python，请参阅 [运行 python 脚本](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。
- 若要了解如何在机器学习服务中使用 R，请参阅 [运行 r 脚本](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。