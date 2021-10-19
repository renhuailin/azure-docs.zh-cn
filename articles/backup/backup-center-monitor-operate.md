---
title: 使用备份中心监视和操作备份
description: 本文介绍如何使用备份中心大规模监视和操作备份
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: cab9e710cfe4bf43b0d225d64e8f64b16c09e3a6
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659842"
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


## <a name="resource-centric-views"></a>以资源为中心的视图

如果组织将多个资源备份到一个公共保管库，且每个资源所有者只想查看他们拥有的资源的备份信息，则可以在备份中心中使用以资源为中心的视图。 若要使用以资源为中心的视图，请选中“仅显示我有权访问的数据源的信息”复选框。 这些选项卡当前支持此选项：“概述”、“备份实例”、“作业”****、“警报”****。 支持的工作负载包括 Azure VM、Azure VM SQL、 Azure VM SAP HANA、Azure Blob 和 Azure 磁盘。

> [!NOTE]
> 即使用户使用的是以资源为中心的视图，他们仍然需要对保管库拥有所需的 RBAC 权限。 此视图可使单个用户避免查看他们并不拥有的资源信息（例如 VM）。

## <a name="next-steps"></a>后续步骤

* [治理备份空间](backup-center-govern-environment.md)
* [使用备份中心执行操作](backup-center-actions.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
