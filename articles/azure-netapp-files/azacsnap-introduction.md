---
title: 什么是适用于 Azure NetApp 文件 Azure 应用程序一致性快照工具 |Microsoft Docs
description: 提供可与 Azure NetApp 文件一起使用 Azure 应用程序一致快照工具的简介。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632576"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>什么是 Azure 应用程序一致的快照工具 (预览版) 

Azure 应用程序一致性快照工具 (AzAcSnap) 是一个命令行工具，可用于简化 Linux 环境中的第三方数据库的数据保护 (SAP HANA) 例如 SUSE 和 RHEL (。  

## <a name="benefits-of-using-azacsnap"></a>使用 AzAcSnap 的好处

AzAcSnap 利用 Azure NetApp 文件和 Azure 大型实例中的卷快照和复制功能。  它提供以下优势：

- **与应用程序一致的数据保护** AzAcSnap 是用于备份关键数据库文件的集中式解决方案。 它在执行存储卷快照之前确保数据库一致性。 因此，它可确保存储卷快照可用于数据库恢复。
- **数据库目录管理** 将 AzAcSnap 与具有内置备份目录的数据库一起使用时，目录中的记录将与存储快照保持最新。  此功能使数据库管理员可以查看备份活动。
- 即席 **批量保护** 在拍摄存储快照之前，此功能对于不需要应用程序静止的非数据库卷很有用。  示例包括 SAP HANA 日志备份卷或 SAPTRANS 卷。
- **存储卷的克隆** 此功能提供了节省空间的存储卷克隆，以用于开发和测试目的。
- **支持灾难恢复** AzAcSnap 利用存储卷复制来提供用于在远程站点恢复复制的应用程序一致性快照的选项。

AzAcSnap 是单个二进制文件。  它不需要其他代理或插件通过 Azure 资源管理器和 Azure 大型实例通过 SSH) 与数据库或存储 (Azure NetApp 文件进行交互。  必须在已连接到数据库和存储的系统上安装 AzAcSnap。  但是，安装和配置的灵活性允许单个集中式安装或在安装每个数据库时安装了副本的完全分布式安装。

## <a name="architecture-overview"></a>体系结构概述

AzAcSnap 可以与数据库 (SAP HANA) 安装在同一台主机上，也可以安装在集中的系统上。  但是，必须有与数据库服务器的网络连接、azure NetApp 文件的存储后端 (Azure 资源管理器或 Azure 大型实例) 的 SSH。

AzAcSnap 是一种轻型应用程序，通常从外部计划程序执行。  在大多数 Linux 系统上，此操作就是 `cron` 文档将重点放在何处。  但是，只要计划程序可以导入 `azacsnap` 用户的 shell 配置文件，就可以使用替代工具。  导入用户的环境设置可确保正确初始化文件路径和权限。

## <a name="command-synopsis"></a>命令摘要

命令的常规格式如下所示： `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` 。

## <a name="command-options"></a>命令选项

命令选项与作为缩进项目符号的主要项目符号和关联子命令的命令如下所示：

- **`-h`** 提供扩展命令行帮助，提供有关 AzAcSnap 使用情况的示例。
- **`-c configure`** 此命令提供一个交互式 Q&样式界面，用于创建或修改 `azacsnap` 配置文件 (默认值为 `azacsnap.json`) 。
  - **`--configuration new`** 将创建新的配置文件。
  - **`--configuration edit`** 将编辑现有的配置文件。
  - 请参阅 [配置命令参考](azacsnap-cmd-ref-configure.md)。
- **`-c test`** 验证配置文件并测试连接性。
  - **`--test hana`**  测试与 SAP HANA 实例的连接。
  - **`--test storage`** 通过在所有配置的卷上创建一个临时存储快照 `data` ，然后删除它们来测试与基础存储接口的通信。
  - **`--test all`** 将 `hana` 按顺序执行和 `storage` 测试。
  - 请参阅 [测试命令参考](azacsnap-cmd-ref-test.md)。
- **`-c backup`** 是用于对数据 (SAP HANA 数据卷执行数据库一致存储快照) & 其他 (例如，共享、日志备份或启动) 卷的主要命令。
  - **`--volume data`** 来快照配置文件的 stanza 中的所有卷 `dataVolume` 。
  - **`--volume other`** 来快照配置文件的 stanza 中的所有卷 `otherVolume` 。
  - 请参阅 [备份命令参考](azacsnap-cmd-ref-backup.md)。
- **`-c details`** 提供有关快照或复制的信息。
  - **`--details snapshots`** 提供有关已配置的每个卷的快照的基本详细信息列表。
  - **`--details replication`** 提供有关从生产站点到灾难恢复站点的复制状态的基本详细信息。
  - 请参阅 [详细信息命令参考](azacsnap-cmd-ref-details.md)。
- **`-c delete`** 此命令删除存储快照或一组快照。 可以使用 HANA Studio 中找到的 SAP HANA 备份 ID，或使用存储快照名称。 备份 ID 仅与为 `hana` 数据和共享卷创建的快照相关联。 否则，如果输入快照名称，则会搜索与输入的快照名称匹配的所有快照。
  - 请参阅 [删除](azacsnap-cmd-ref-delete.md)。
- **`-c restore`** 提供了两种方法来将快照还原到卷，方法是基于快照创建新卷或将卷回滚到预览状态。
  - **`--restore snaptovol`** 基于目标卷上的最新快照创建新卷。
  - **`-c restore --restore revertvolume`** 根据最新的快照，将目标卷恢复到先前的状态。
  - 请参阅 [还原命令参考](azacsnap-cmd-ref-restore.md)。
- **`[--configfile <configfilename>]`** 可选的命令行参数，用于提供不同的 JSON 配置文件名。  这对于为每个 SID 创建单独的配置文件 (例如) 是特别有用的 `--configfile H80.json` 。

## <a name="next-steps"></a>后续步骤

- [Azure 应用程序一致的快照工具入门](azacsnap-get-started.md)
