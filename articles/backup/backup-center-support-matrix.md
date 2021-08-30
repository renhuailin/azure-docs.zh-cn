---
title: 备份中心的支持矩阵
description: 本文汇总了备份中心支持的每种工作负载类型的方案
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: b922f2e320f9b41186f5a6397af5cd5f4b3f7750
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181362"
---
# <a name="support-matrix-for-backup-center"></a>备份中心的支持矩阵

备份中心提供单一虚拟管理平台，使企业能够[大规模治理、监视、操作和分析备份](backup-center-overview.md)。 本文汇总了备份中心支持的每种工作负载类型的方案。

## <a name="supported-scenarios"></a>支持的方案

| **类别** | **方案**  | **支持的工作负荷**  | **限制** |
| -------------| ------------- | ----------------------- |------------|
| 监视   | 查看所有作业 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | <li> 7 天的作业立即可用。 <br> <li> 每个筛选器/下拉列表最多支持 1000 项。 因此，备份中心可用于跨租户监视最多 1000 个订阅和 1000 个保管库。 |
| 监视 | 查看所有备份实例 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 同上 |
| 监视 | 查看所有备份策略 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 同上 |
| 监视 | 查看所有保管库 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 同上 |
| 操作 | 配置备份 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅 [Azure VM 备份](./backup-support-matrix-iaas.md)和 [Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 操作 | 还原备份实例 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅 [Azure VM 备份](./backup-support-matrix-iaas.md)和 [Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 操作 | 创建保管库 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅[恢复服务保管库](./backup-support-matrix.md#vault-support)的支持矩阵 |
| 操作 | 创建备份策略 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅[恢复服务保管库](./backup-support-matrix.md#vault-support)的支持矩阵 |
| 操作 | 为备份实例执行按需备份 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅 [Azure VM 备份](./backup-support-matrix-iaas.md)和 [Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 操作 | 停止为备份实例备份 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅 [Azure VM 备份](./backup-support-matrix-iaas.md)和 [Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 洞察力 | 查看备份报告 | <li> Azure 虚拟机 <br><br> <li> Azure 虚拟机中的 SQL <br><br> <li> Azure 虚拟机中的 SAP HANA <br><br> <li> Azure 文件 <br><br> <li> 系统中心数据保护管理器 <br><br> <li> Azure 备份代理 (MARS) <br><br> <li> Azure 备份服务器 (MABS) | 请参阅[备份报告支持的方案](./configure-reports.md#supported-scenarios) |
| 调控 | 在“备份”类别下查看并分配内置和自定义 Azure 策略 | 空值 | 空值 |
| 调控 | 查看没有配置备份的数据源 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 | 空值 |
| 监视 | 大规模查看 Azure Monitor 警报 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件<br/><br/> <li>Azure Blob<br/><br/> <li>Azure 托管磁盘 | 请参阅[警报](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview)文档 |
| 操作 | 从备份中心执行跨区域还原作业 | <li> Azure 虚拟机 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA | 请参阅[跨区域还原](./backup-create-rs-vault.md#set-cross-region-restore)文档 |

## <a name="unsupported-scenarios"></a>不支持的方案

| **类别** | **方案**  |
|--------------|---------------|
| 操作 | 备份中心当前不支持大规模配置保管库设置 |
| 可用性 | 备份中心当前在国家/地区云中不可用 | 

## <a name="next-steps"></a>后续步骤

* [查看 Azure 备份的支持矩阵](./backup-support-matrix.md)
* [查看 Azure VM 备份的支持矩阵](./backup-support-matrix-iaas.md)
* [查看 Azure Database for PostgreSQL 服务器备份的支持矩阵](backup-azure-database-postgresql.md#support-matrix)