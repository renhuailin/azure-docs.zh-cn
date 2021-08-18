---
title: Azure SQL 托管实例中的机器学习服务
description: 本文概述 Azure SQL 托管实例中的机器学习服务。
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
ms.openlocfilehash: 0d24da8044cc4306f65f235a85092593610e33f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751222"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Azure SQL 托管实例中的机器学习服务

机器学习服务是 Azure SQL 托管实例的一项功能，可提供支持 Python 和 R 脚本的数据库内机器学习。 该功能包括用于高性能预测分析和机器学习的 Microsoft Python 以及 R 包。 关系数据可以通过存储过程、包含 Python 或 R 语句的 T-SQL 脚本或包含 T-SQL 的 Python 或 R 代码在脚本中使用。

## <a name="what-is-machine-learning-services"></a>什么是机器学习服务？

使用 Azure SQL 托管实例中的机器学习服务，你可以在数据库中执行 Python 和 R 脚本。 可以使用它来准备和清理数据、执行特征工程以及在数据库中定型、评估和部署机器学习模型。 此功能在数据所在的位置运行脚本，无需通过网络将数据传输到其他服务器。

使用 Azure SQL 托管实例中支持 R/Python 的机器学习服务，你可以：

- **运行 R 和 Python 脚本以准备数据和处理常规用途的数据** - 现在可以将 R/Python 脚本引入数据所在的 Azure SQL 托管实例，而无需将数据移出到其他服务器来运行 R 和 Python 脚本。 无需移动数据，避免发生延迟、安全性和合规性相关的问题。

- **在数据库中训练机器学习模型** - 可以使用任何开源算法来训练模型。 可以轻松地将训练扩展到整个数据集，而不用依赖于数据库中提取的示例数据集。

- **将模型和脚本部署到存储过程的生产环境中** - 只需将脚本和已训练的模型嵌入 T-SQL 存储过程，便可对它们进行操作。 连接到 Azure SQL 托管实例的应用只需调用存储过程，便可受益于这些模型中的预测和智能功能。 此外，还可以使用本机 T-SQL PREDICT 函数来操作模型，以便在高度并发的实时评分方案中进行快速评分。

Python 和 R 的基本分发包含在机器学习服务中。 除了用于 Python 的 [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) 和 [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) Microsoft 包，以及用于 R 的 [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler)、[MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml)、[olapR](/sql/machine-learning/r/ref-r-olapr) 和 [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) 外，还可以安装和使用开源包和框架，如 PyTorch、TensorFlow 和 scikit-learn。

## <a name="how-to-enable-machine-learning-services"></a>如何启用机器学习服务

利用以下 SQL 命令的扩展性，你可以启用 Azure SQL 托管实例中的机器学习服务（SQL 托管实例将重启，并在几秒钟内不可用）：

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

有关此命令如何影响 SQL 托管实例资源的详细信息，请参阅[资源调控](machine-learning-services-differences.md#resource-governance)。

### <a name="enable-machine-learning-services-in-a-failover-group"></a>在故障转移组中启用机器学习服务

在[故障转移组](failover-group-add-instance-tutorial.md)中，系统数据库不会复制到辅助实例（有关详细信息，请参阅[故障转移组的限制](../database/auto-failover-group-overview.md#limitations-of-failover-groups)）。

如果正在使用的托管实例属于故障转移组，请执行以下操作：

- 在故障转移组的每个实例上运行 `sp_configure` 和 `RECONFIGURE` 命令，以启用机器学习服务。

- 在用户数据库而不是 master 数据库上安装 R/Python 库。

## <a name="next-steps"></a>后续步骤

- 请参阅[与 SQL Server 机器学习服务的主要区别](machine-learning-services-differences.md)。
- 若要了解如何在机器学习服务中使用 Python，请参阅[运行 Python 脚本](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。
- 若要了解如何在机器学习服务中使用 R，请参阅[运行 R 脚本](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。
- 有关其他 SQL 平台上的机器学习的详细信息，请参阅 [SQL 机器学习文档](/sql/machine-learning/index)。
