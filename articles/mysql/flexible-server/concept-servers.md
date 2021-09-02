---
title: 服务器概念 - Azure Database for MySQL 灵活服务器
description: 本主题提供使用 Azure Database for MySQL 灵活服务器的注意事项和指南
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a0ce6658168bd86188875d9d89f218ee26ef7941
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652677"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL 灵活服务器（预览版）中的服务器概念

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文提供使用 Azure Database for MySQL 灵活服务器的注意事项和指南。

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>什么是 Azure Database for MySQL 灵活服务器？

Azure Database for MySQL 灵活服务器是运行社区版 MySQL 的完全托管式数据库服务。 通常，该服务旨在根据用户要求自定灵活性和配置。 它的 MySQL 服务器构造与本地环境中用户可能比较熟悉的构造相同。 具体而言，灵活服务器是托管式服务器，提供立即可用的性能以及更好的服务器可管理性和控制能力，并公开访问和服务器级别的功能。

Azure Database for MySQL 灵活服务器：

- 在 Azure 订阅中创建。
- 是数据库的父资源。
- 允许通过服务器参数（服务器参数概念的链接）公开 MySQL 配置。
- 执行自动备份并支持时间点还原。
- 为数据库提供了一个命名空间。
- 是具有强生存期语义的容器 - 删除服务器时会删除所包含的数据库。
- 并置区域中的资源。
- 支持客户提供的服务器维护计划
- 能够在区域冗余设置中部署灵活服务器，以改善高可用性
- 为数据库服务器访问提供虚拟网络集成
- 提供节约成本的方法，即在不使用灵活服务器时将其暂停
- 提供应用于其数据库的管理策略的作用域：登录名、防火墙、用户、角色、配置等。
- 支持主要版本 MySQL 5.7 和 MySQL 8.0。 有关详细信息，请参阅[支持的 Azure Database for MySQL 引擎版本](./../concepts-supported-versions.md)。

在 Azure Database for MySQL 灵活服务器中，可创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，也可以选择创建多个数据库来共享资源。 按服务器根据计算层、vCore 和存储 (GB) 的配置采用结构化定价。 有关详细信息，请参阅[计算和存储](./concepts-compute-storage.md)。

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>停止/启动 Azure Database for MySQL 灵活服务器

借助 Azure Database for MySQL 灵活服务器，可以在不使用服务器时停止该服务器，在恢复活动时启动该服务器 。 这样做的目的主要是为了节省数据库服务器的成本，只有在使用时才为资源付费。 这对于开发测试工作负载以及仅在一天中部分时间使用服务器的情况尤为重要。 停止服务器时，将删除所有活动连接。 稍后，当你想要使服务器重新联机时，可以使用 [Azure 门户](how-to-stop-start-server-portal.md)或 CLI。

当服务器处于“已停止”状态时，服务器的计算不会产生费用。 但是，由于仍要保留服务器的存储空间以确保再次启动服务器时数据文件可用，因此存储会继续产生费用。

> [!IMPORTANT]
> 当你停止服务器时，它将在接下来的 7 天中一直保持该状态。 如果在此期间未手动启动服务器，则服务器将在 7 天结束时自动启动。 如果不使用服务器，则可以选择再次停止。

在服务器停止期间，无法在服务器上执行任何管理操作。 若要更改服务器上的任何配置设置，需要[启动服务器](how-to-stop-start-server-portal.md)。 请参阅[停止/启动限制](./concepts-limitations.md#stopstart-operation)。

## <a name="how-do-i-manage-a-server"></a>如何管理服务器？

你可以通过使用 [Azure 门户](./quickstart-create-server-portal.md)或 [Azure CLI](./quickstart-create-server-cli.md) 来管理 Azure Database for MySQL 灵活服务器的创建、删除、服务器参数配置 (my.cnf)、缩放、网络、安全性、高可用性、备份与还原和监视活动。 此外，以下存储过程在 Azure Database for MySQL 中提供，用于执行某些所需的数据库管理任务，因为超级用户权限在服务器上不受支持。

|**存储过程名称**|**输入参数**|**输出参数**|**用法说明**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|不适用|等效于 [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 命令。 在终止连接正在执行的任何语句之后，将终止与提供的 process list_id 关联的连接。|
|*mysql.az_kill_query*|processlist_id|空值|等效于 [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 命令。 将终止连接当前正在执行的语句。 使连接本身保持活动状态。|
|*mysql.az_load_timezone*|空值|不适用|加载[时区表](../howto-server-parameters.md#working-with-the-time-zone-parameter)以允许将 `time_zone` 参数设置为命名值（例如， “US/Pacific”）。|


## <a name="next-steps"></a>后续步骤

-   了解[创建服务器](./quickstart-create-server-portal.md)
-   了解[监视和警报](./how-to-alert-on-metric.md)

