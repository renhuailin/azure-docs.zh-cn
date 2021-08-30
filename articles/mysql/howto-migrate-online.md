---
title: 最短的停机时间迁移 - Azure Database for MySQL
description: 本文介绍如何执行 MySQL 数据库到 Azure Database for MySQL 的最短停机时间迁移。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: ''
ms.date: 6/19/2021
ms.openlocfilehash: 728c1d76539eb34efe5e5fc475837810ea5a5111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088577"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>在最短的停机时间内迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

可以通过使用数据传入复制以最少故障时间执行 MySQL 到 Azure Database for MySQL 的迁移，从而限制应用程序导致的故障时间。

还可参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)，获取有关将数据库迁移到 Azure Database for MySQL 的详细信息和用例。 本指南提供的指导将引导成功规划和执行到 Azure 的 MySQL 迁移。

## <a name="overview"></a>概述

使用数据传入复制，可以将源服务器配置为主服务器，将目标服务器配置为副本服务器，这样就可以在应用程序继续运行的同时将任何新事务持续同步到 Azure。 在目标 Azure 端的数据已与本地端数据保持同步后，可暂停应用程序一段时间（最短停机时间），等待最后一批数据（从停止应用程序时起，直到应用程序实际不可用于接收任何新流量）同步到目标，然后更新连接字符串使其指向 Azure。 完成以后，应用程序将留存在 Azure 中！

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将数据库迁移到 Azure Database for MySQL，请参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。
