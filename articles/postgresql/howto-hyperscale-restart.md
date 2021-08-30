---
title: 重启服务器 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 如何在 Azure Database for PostgreSQL - 超大规模 (Citus) 中重启数据库
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 7/9/2021
ms.openlocfilehash: 0de8108cde0b5017221bf3ab038d98c98bb94546
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598570"
---
# <a name="restart-azure-database-for-postgresql---hyperscale-citus"></a>重启 Azure Database for PostgreSQL - 超大规模 (Citus)

如果要重启超大规模 (Citus) 服务器组，可以通过 Azure 门户中组的“概述”页执行此操作。 选择顶部栏中的“重启”按钮。 将显示确认对话框。 选择“全部重启”以继续。

> [!NOTE]
> 如果服务器组中尚不存在“还原”按钮，请打开 Azure 支持请求以重启服务器组。

重启服务器组适用于所有节点；不能有选择地重启各个节点。 重启将应用于节点的整个虚拟机，而不仅仅应用于 PostgreSQL 服务器实例。 尝试使用数据库的任何应用程序在重新启动时都会遇到连接中断。

**后续步骤**

- 更改某些服务器参数需要重启。 请参阅超大规模 (Citus) 上可配置的[所有服务器参数](reference-hyperscale-parameters.md)的列表。
