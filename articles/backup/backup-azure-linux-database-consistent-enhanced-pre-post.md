---
title: 使用增强的前后脚本框架创建数据库一致性快照
description: 了解 Azure 备份如何使用 Azure VM 备份和打包的前后脚本创建数据库一致性快照
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa9f4ba3dc344e07a3383c6f8081c9304e3ebbeb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092198"
---
# <a name="enhanced-pre-post-scripts-for-database-consistent-snapshot"></a>用于创建数据库一致性快照的增强的前后脚本

Azure 备份服务已提供 [_前后_ 脚本框架](./backup-azure-linux-app-consistent.md)，使用 Azure 备份实现 Linux VM 中的应用程序一致性。 这包括在创建磁盘快照之前调用前脚本（以静止应用程序），并在快照完成后调用后脚本（用于取消冻结应用程序的命令），使应用程序恢复到正常模式。

编写、调试和维护前后脚本可能非常困难。 为了消除这种复杂性，Azure 备份为 marquee 数据库提供了简化的前后脚本体验，从而以最小的开销获得应用程序一致性快照。

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/linux-application-consistent-snapshot.png" alt-text="显示 Azure 备份提供的 Linux 应用程序一致性快照的示意图。":::

新的增强版前后脚本框架具有以下主要优点：

- 这些前后脚本与备份扩展一起直接安装在 Azure VM 中。 这有助于减少创作工作并从外部位置下载它们。
- 可以在 [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) 中查看前后脚本的定义和内容，甚至还可以提交建议和更改。 你甚至可以通过 GitHub 提交建议和更改，这些建议和更改在经过会审后会添加到库中，从而使更广泛的社区受益。
- 你甚至可以通过 [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) 为其他数据库添加新的前后脚本，这些脚本在经过会审后会添加到库中，从而使更广泛的社区受益。
- 这个框架现在非常可靠，可以高效处理前脚本执行失败或崩溃等情况。 在任何情况下，后脚本都会自动运行以回滚在前脚本中完成的所有更改。
- 该框架还为外部工具提供了一个消息传送通道，用于获取更新和准备它们自己针对任何消息/事件的操作计划。

## <a name="solution-flow-preview"></a>解决方案流（预览）

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/solution-flow.png" alt-text="显示解决方案流的示意图。":::

## <a name="support-matrix"></a>支持矩阵

增强的框架下涵盖了以下数据库列表：

- [Oracle（公开发布）](../virtual-machines/workloads/oracle/oracle-database-backup-azure-backup.md) - [链接到支持矩阵](backup-support-matrix-iaas.md#support-matrix-for-managed-pre-post-scripts-for-linux-databases)
- MySQL（预览版）

## <a name="prerequisites"></a>必备条件

只需在 `/etc/azure` 中修改配置文件 workload.conf 以提供连接详细信息。 这使得 Azure 备份连接到相关应用程序并执行前脚本和后脚本。 配置文件具有以下参数。

```json
[workload]
# valid values are mysql, oracle
workload_name =
command_path = 
linux_user =
credString = 
ipc_folder = 
timeout =
```

下表描述了参数：

|参数  |必需  |说明  |
|---------|---------|---------|
|workload_name     |   是      |     这包含需要应用程序一致性备份的数据库的名称。 当前支持的值为 `oracle` 或 `mysql`。    |
|command_path/configuration_path     |         |   这包含指向工作负载二进制文件的路径。 如果工作负载二进制文件设置为路径变量，则这不是必填字段。      |
|linux_user     |    是     |   这将包含有权访问数据库用户登录名的 linux 用户的用户名。 如果未设置此值，则根目录会被视为默认用户。      |
|credString     |         |     这表示用于连接到数据库的凭据字符串。 这将包含整个登录名字符串。    |
|ipc_folder     |         |   工作负载只能写入某些文件系统路径。 需要在此处提供此文件夹路径，以便前脚本可以将状态写入此文件夹路径。      |
|timeout     |    是     |     这是数据库处于静止状态的最长时间限制。 默认值为 90 秒。 不建议设置小于 60 秒的值。    |

> [!NOTE]
> JSON 定义是 Azure 备份服务可根据特定数据库进行修改的模板。 若要了解每个数据库的配置文件，请参阅[每个数据库的手册](#support-matrix)。

总体来说，使用增强的前后脚本框架的客户体验如下所示：

- 准备数据库环境
- 编辑配置文件
- 触发 VM 备份
- 按需要从应用程序一致性恢复点还原 VM 或磁盘/文件。

## <a name="build-a-database-backup-strategy"></a>生成数据库备份策略

### <a name="using-snapshots-instead-of-streaming"></a>使用快照，而不使用流式处理

通常，数据库管理员在他们的备份策略中使用流式处理备份（例如完整备份、差异备份或增量备份）和日志。 下面是设计中的一些关键要点。

- 性能和成本：每日完整备份 + 日志在还原期间将是最快的，但涉及的成本会很高。 包括差异/增量流式处理备份类型可降低成本，但可能会影响还原性能。 但快照提供了性能和成本的最佳组合。  快照在本质上是递增的，在备份过程中对性能的影响最小，同时也可以节省成本。
- 对数据库/基础结构的影响：流式处理备份的性能取决于基础存储 IOPS 以及当流面向远程位置时可用的网络带宽。 快照没有这种依赖关系，并大大降低了 IOPS 和网络带宽方面的需求。
- 可重用性：用于触发不同流式处理备份类型的命令对于每个数据库都是不同的。 因此，脚本不容易被重复使用。 此外，如果使用不同的备份类型，请确保评估依赖关系链来维护生命周期。 对于快照，编写脚本非常简单，因为没有依赖关系链。
- 长期保留：完整备份始终有利于长期保留，因为可以对它进行单独移动和恢复。 但对于保留时间较短的操作备份，快照更为有利。

因此，对于长期保留，每日快照 + 日志并偶尔进行完整备份是数据库的最佳备份策略。

### <a name="log-backup-strategy"></a>日志备份策略

增强的前后脚本框架建立在每天执行一次计划备份的 Azure VM 备份的基础之上。 因此，RPO 为 24 小时的数据丢失时间段不适用于生产数据库。 此解决方案由日志备份策略进行补充，在该策略中，日志备份是显式流式传输出来的。

[blob 上的 NFS](../storage/blobs/network-file-system-protocol-support.md) 和 [AFS 上的 NFS（预览版）](../storage/files/files-nfs-protocol.md)使直接在数据库 VM 上装载卷并使用数据库客户端传输日志备份变得更加容易。 数据丢失时间段（即 RPO）会降到日志备份的频率。 此外，NFS 目标不需要具有很高的性能，因为在获取数据库一致性快照后，可能不需要为操作备份触发常规流式处理（完整和增量备份）。

>[!NOTE]
>增强的前脚本通常会注意在将数据库静止以创建快照之前，将传输中的所有日志事务刷新到日志备份目标。 因此，快照在恢复期间具备数据库一致性和可靠性。

### <a name="recovery-strategy"></a>恢复策略

创建数据库一致性快照并将日志备份流式传输至 NFS 卷后，数据库的恢复策略可以使用 Azure VM 备份的恢复功能。 另外，还可以使用数据库客户端向其应用日志备份的功能。 下面是恢复策略的一些选项：

- 从数据库一致性恢复点创建新的 VM。 VM 应已连接日志装入点。 使用数据库客户端运行恢复命令，进行时点还原。
- 从数据库一致性恢复点创建磁盘，并将其附加到另一个目标 VM。 然后，装载日志目标并使用数据库客户端运行恢复命令以进行时点恢复
- 使用文件恢复选项并生成脚本。 在目标 VM 上运行脚本，并将恢复点附加为 iSCSI 磁盘。 然后，使用数据库客户端在附加磁盘上运行特定于数据库的验证函数，并验证备份数据。 此外，使用数据库客户端导出/恢复几个表/文件，而不是恢复整个数据库。
- 使用跨区域还原功能，在区域性灾难期间从次要配对区域执行上述操作。

## <a name="summary"></a>摘要

结合使用数据库一致性快照和采用自定义解决方案备份的日志，可以利用 Azure VM 备份的优势，并重复使用数据库客户端的功能，构建一个性能良好且经济高效的数据库备份解决方案。
