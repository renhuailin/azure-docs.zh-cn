---
title: 将数据移入和移出 Azure Blob 存储 - Team Data Science Process
description: 使用 Azure 存储资源管理器、AzCopy、Python 和 SSIS 将数据移入或移出 Azure Blob 存储。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 151375a37b80567aecf100ec3cd576882d06e3f3
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902242"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>将数据移入和移出 Azure Blob 存储

团队数据科学过程要求引入或载入各种不同存储环境中的数据在过程的每个阶段中都以最合适的方式进行处理或分析。

## <a name="different-technologies-for-moving-data"></a>用于移动数据的不同技术

以下文章介绍了如何使用不同技术将数据移入和移出 Azure Blob 存储。

* [Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
  * 存储资源管理器是 Microsoft 免费提供的应用，可用于在 Windows、macOS 和 Linux 上处理 Azure 存储数据。
  * 如果正在使用通过 Azure 中数据科学虚拟机提供的脚本设置的 VM，则 VM 上已安装 Azure 存储资源管理器。
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
  * AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 
* [Python SDK](../../storage/blobs/storage-quickstart-blobs-python.md)
  * 使用适用于 Python 的 Azure Blob 存储客户端库来移动 blob。
* [用于 Azure 的 SQL Server Integration Services (SSIS) 功能包](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)
  * SSIS 提供了可用于连接到 Azure，在 Azure 和本地数据源之间传输数据以及处理存储在 Azure 中的数据的组件。 若要深入了解使用 SSIS 完成混合数据集成方案中常见的业务需求的规范方案讨论，请参阅[“使用用于 Azure 的 SQL Server Integration Services 功能包执行更多操作”博客](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238)。
  * 有关 SSIS 的培训资料，请参阅 [SSIS 培训指导](https://www.microsoft.com/sql-server/training-certification)。
  * 有关如何使用 SISS 生成简单的提取、转换和加载 (ETL) 包进行启动并运行的信息，请参阅 [SSIS 教程：创建简单的 ETL 包](/sql/integration-services/ssis-how-to-create-an-etl-package)。

最合适的方法取决于具体的方案。 [用于 Azure 机器学习中高级分析的方案](plan-sample-scenarios.md)有助于确定用于高级分析过程的各种数据科学工作流所需的资源。

> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../../storage/blobs/storage-quickstart-blobs-dotnet.md)和 [Azure Blob 服务](/rest/api/storageservices/Blob-Service-Concepts)。
> 
> 

## <a name="using-azure-data-factory"></a>使用 Azure 数据工厂

或者，可使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)来执行以下操作： 

* 创建和计划从 Azure Blob 存储下载数据的管道，
* 将其传递到已发布的 Azure 机器学习 Web 服务， 
* 接收预测分析结果，然后 
* 将结果上传到存储。 

有关详细信息，请参阅[使用 Azure 数据工厂和 Azure 机器学习创建预测管道](../../data-factory/transform-data-using-machine-learning.md)。

## <a name="prerequisites"></a>先决条件
本文假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 上传/下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 若要查看存储帐户创建说明并了解如何获取帐户和密钥信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-account-create.md)。