---
title: 使用备份中心监视和操作备份
description: 本文介绍如何使用备份中心大规模监视和操作备份
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506104"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>使用备份中心监视和操作备份

作为备份管理员，你可以将备份中心作为一个统一窗口来集中监视日常作业和备份库存。 还可以使用备份中心来执行常规操作，例如响应按需备份请求、还原备份、创建备份策略等。

## <a name="supported-scenarios"></a>支持的方案

* 备份中心目前支持 Azure VM 备份、Azure VM 中的 SQL 备份、Azure VM 中的 SAP HANA 备份、Azure 文件存储备份、Azure Blobs 备份、Azure 托管磁盘备份和 Azure Database for PostgreSQL 服务器备份。
* 有关支持和不支持的方案的详细列表，请参阅[支持矩阵](backup-center-support-matrix.md)。

## <a name="backup-instances"></a>备份实例

利用备份中心，可以轻松搜索和发现备份空间中的备份实例。

选择备份中心的“备份实例”选项卡，可以查看有权访问的所有备份实例的详细信息。

 可以查看每个备份实例的下列相关信息：

* 数据源订阅
* 数据源资源组
* 最新恢复点
* 与备份实例关联的保管库
* 保护状态

 还可以按以下参数筛选备份实例的列表：

* 数据源订阅
* 数据源资源组
* 数据源位置
* 数据源类型
* 保管库
* 保护状态
* 数据源标记

右键单击网格中的任意项可在给定的备份实例上执行操作，例如，导航到资源、触发按需备份和还原或停止备份。

![备份中心 - 实例](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>备份作业

利用备份中心，可以查看在备份空间中创建的所有作业的详细信息，并对失败的作业执行适当的操作。

选择“备份中心”的“备份作业”菜单项可查看所有作业。 每个作用包含以下信息：

* 与作业关联的备份实例
* 数据源订阅
* 数据源资源组
* 数据源位置
* 作业操作
* 作业状态
* 作业开始时间
* 作业周期

在网格中选择某个项可查看给定作业的更多详细信息。 右键单击某个项可帮助导航到该资源并执行必要操作。

![备份中心 - 作业](./media/backup-center-monitor-operate/backup-center-jobs.png)

使用“备份作业”选项卡，可以查看最近 7 天的作业。 若要查看较旧的作业，请使用[备份报表](backup-center-obtain-insights.md)。

## <a name="vaults"></a>保管库

通过选择“备份中心”的“保管库”菜单项，可以查看有权访问的所有[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)和[备份保管库](backup-vault-overview.md)的列表。 可以使用以下参数筛选列表：

* 保管库订阅
* 保管库资源组
* 保管库名称
* 与策略关联的数据源类型

选择列表中的任意项，可以导航到相应的保管库。

![备份中心 - 保管库](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>备份策略

利用备份中心，可以查看和编辑任何备份策略的关键信息。

选择“备份策略”菜单项可以查看在备份空间中创建的所有策略。 可以按保管库订阅、资源组、数据源类型和保管库来筛选列表。 右键单击网格中的某个项可查看该策略的关联项、编辑策略，甚至在必要时将其删除。

![备份中心 - 策略](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>后续步骤

* [治理备份空间](backup-center-govern-environment.md)
* [使用备份中心执行操作](backup-center-actions.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
