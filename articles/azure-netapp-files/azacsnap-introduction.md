---
title: 什么是适用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具 | Microsoft Docs
description: 介绍可与 Azure NetApp 文件配合使用的 Azure 应用程序一致性快照工具。
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
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 86783df03a395e287a6656b52099cffc579ee31f
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226275"
---
# <a name="what-is-azure-application-consistent-snapshot-tool"></a>什么是 Azure 应用程序一致性快照工具

Azure 应用程序一致性快照工具 (AzAcSnap) 是一个命令行工具，可实现对第三方数据库的数据保护，方式是在执行存储快照之前处理所有所需的业务流程以将第三方数据库置于应用程序一致状态，完成快照之后将它们返回到操作状态。

## <a name="supported-platforms-and-os"></a>支持的平台和 OS

- **数据库**
  - SAP HANA（有关详细信息，请参阅[支持矩阵](azacsnap-get-started.md#snapshot-support-matrix-from-sap)）

- **操作系统**
  - SUSE Linux Enterprise Server 12+
  - Red Hat Enterprise Linux 7+

## <a name="benefits-of-using-azacsnap"></a>使用 AzAcSnap 的好处

AzAcSnap 会使用 Azure NetApp 文件和 Azure 大型实例中的卷快照和复制功能。  它提供以下优势：

- **应用程序一致性数据保护** AzAcSnap 是一种用于备份关键数据库文件的集中式解决方案。 它在执行存储卷快照前会确保数据库的一致性。 因此，它可确保存储卷快照可用于数据库恢复。
- **数据库目录管理** 将 AzAcSnap 用于包含内置备份目录的数据库时，目录中的记录会随存储快照更新。  该功能允许数据库管理员查看备份活动。
- **临时卷保护** 对于在执行存储快照之前不需要停止应用程序的非数据库卷，该功能很有用。  示例包括 SAP HANA 日志备份卷或 SAPTRANS 卷。
- **存储卷克隆** 此功能提供高效空间存储卷克隆以用于开发和测试目的。
- **支持灾难恢复** AzAcSnap 利用存储卷复制，在远程站点恢复复制的应用程序一致性快照。

AzAcSnap 是单个二进制文件。  它不需要额外的代理或插件来与数据库或存储交互（Azure NetApp 文件需要通过 Azure 资源管理器来访问，Azure 大型实例则需要通过 SSH 来访问）。  AzAcSnap 必须安装在已连接到数据库和存储的系统上。  但是，其在安装和配置上的灵活性允许进行单一集中式安装（仅限 Azure NetApp 文件），也允许在每个数据库安装副本的完全分布式安装（Azure NetApp 文件和 Azure 大型实例）。

## <a name="architecture-overview"></a>体系结构概述

AzAcSnap 可以安装在与数据库 (SAP HANA) 相同的主机上，也可以集中安装在一个系统上。  但是，必须与数据库服务器和存储后端建立网络连接（针对 Azure NetApp 文件的 Azure 资源管理器或针对 Azure 大型实例的 SSH）。

AzAcSnap 是一个轻量级应用程序，通常从外部计划程序执行。  在大多数 Linux 系统上，此操作为 `cron`，本文档将重点对其进行介绍。  但是，只要计划程序可以导入 `azacsnap` 用户的 shell 配置文件，就可以作为替代工具。  导入用户的环境设置可以确保正确初始化文件路径和权限。

## <a name="command-synopsis"></a>命令摘要

命令的一般格式如下：`azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]`。

## <a name="command-options"></a>命令选项

命令选项如下所示，其中主项目符号为命令，缩进项目符号为关联的子命令：

- `-h` 通过 AzAcSnap 使用情况示例提供扩展命令行帮助。
- `-c configure` 此命令提供交互式 Q&A 样式接口来创建或修改 `azacsnap` 配置文件（默认为 `azacsnap.json`）。
  - `--configuration new` 将创建新的配置文件。
  - `--configuration edit` 将编辑现有的配置文件。
  - 请参阅[配置命令参考](azacsnap-cmd-ref-configure.md)。
- `-c test` 验证配置文件并测试连接。
  - `--test hana` 测试与 SAP HANA 实例的连接。
  - `--test storage` 会在所有配置的 `data` 卷上创建一个临时存储快照，然后再删除这些快照，以此来测试与基础存储接口的通信。
  - `--test all` 将按顺序执行 `hana` 和 `storage` 测试。
  - 请参阅[测试命令参考](azacsnap-cmd-ref-test.md)。
- `-c backup` 是用于为数据（SAP HANA 数据卷）和其他（例如共享、日志备份或引导）卷执行数据库一致性存储快照的主命令。
  - `--volume data` 对配置文件 `dataVolume` stanza 中的所有卷执行快照。
  - `--volume other` 对配置文件 `otherVolume` stanza 中的所有卷执行快照。
  - 请参阅[备份命令参考](azacsnap-cmd-ref-backup.md)。
- `-c details` 提供有关快照或复制的信息。
  - `--details snapshots` 提供有关已配置的每个卷的快照的基本详细信息列表。
  - `--details replication` 提供有关生产站点到灾难恢复站点的复制状态的基本详细信息。
  - 请参阅[详细信息命令参考](azacsnap-cmd-ref-details.md)。
- `-c delete` 此命令删除一个存储快照或一组快照。 可以使用 HANA Studio 中的 SAP HANA 备份 ID，或使用存储快照名称。 备份 ID 仅绑定到为数据和共享卷创建的 `hana` 快照。 否则，如果输入快照名称，会搜索所有与输入的快照名称匹配的快照。
  - 请参阅[删除](azacsnap-cmd-ref-delete.md)。
- `-c restore` 提供两种方法将快照还原到卷，一种是基于快照创建新卷，另一种是将卷回滚到先前状态。
  - `--restore snaptovol` 基于目标卷上的最新快照创建新卷。
  - `-c restore --restore revertvolume` 根据最新的快照，将目标卷还原到先前的状态。
  - 请参阅[还原命令参考](azacsnap-cmd-ref-restore.md)。
- `[--configfile <configfilename>]` 可选的命令行参数，用于提供不同的 JSON 配置文件名。  这对于为每个 SID 创建单独的配置文件特别有用（例如 `--configfile H80.json`）。

## <a name="next-steps"></a>后续步骤

- [Azure 应用程序一致性快照工具入门](azacsnap-get-started.md)
