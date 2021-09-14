---
title: SAP HANA 备份支持矩阵
description: 本文介绍了使用 Azure 备份来备份 Azure VM 上的 SAP HANA 数据库时的支持方案和限制。
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: 09dab8a35c5ed06ec9680b2cb57ca6ac0d27109f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427932"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>针对备份 Azure VM 上的 SAP HANA 数据库的支持矩阵

Azure 备份支持将 SAP HANA 数据库备份到 Azure。 本文总结了在使用 Azure 备份来备份 Azure VM 上的 SAP HANA 数据库时的支持方案和限制。

> [!NOTE]
> 日志备份的频率现在可以设置为至少 15 分钟。 日志备份只能在数据库的完整备份成功后开始。

## <a name="scenario-support"></a>方案支持

| **方案**               | **支持的配置**                                | **不支持的配置**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓扑**               | 仅在 Azure Linux VM 中运行的 SAP HANA                    | HANA 大型实例 (HLI)                                   |
| **区域**                   | **GA：**<br> **美洲** - 美国中部、美国东部 2、美国东部、美国中北部、美国中南部、美国西部 2、美国中西部、美国西部、加拿大中部、加拿大东部、巴西南部 <br> **亚太** - 澳大利亚中部、澳大利亚中部 2、澳大利亚东部、澳大利亚东南部、日本东部、日本西部、韩国中部、韩国南部、东亚、东南亚、印度中部、印度南部、印度西部、中国东部、中国北部、中国东部 2、中国北部 2 <br> **欧洲** - 西欧、北欧、法国中部、英国南部、英国西部、德国北部、德国中西部、瑞士北部、瑞士西部、瑞士中北部、挪威东部、挪威西部 <br> **非洲/ME** - 南非北部、南非西部、阿拉伯联合酋长国北部、阿拉伯联合酋长国中部  <BR>  **Azure 政府区域** | 法国南部、德国中部、德国东北部、US Gov 爱荷华州 |
| **OS 版本**            | 带 SP2、SP3、SP4 和 SP5 的 SLES 12；带 SP0、SP1、SP2 和 SP3 的 SLES 15 <br><br>  RHEL 7.4、7.6、7.7、7.9、8.1、8.2 和 8.4                |                                             |
| **HANA 版本**          | SDC on HANA 1.x、MDC on HANA 2.x SPS04、SPS05 Rev <= 55（对启用了加密的方案也进行了验证）      |                                                            |
| **HANA 部署**       | 基于单个 Azure VM 的 SAP HANA - 仅纵向扩展 <br><br> 进行高可用性部署时，两个不同计算机上的两个节点均被视为具有单独数据链的单个节点。               | 横向扩展 <br><br> 在高可用性部署中，备份不会自动故障转移到辅助节点。 应为每个节点单独进行备份配置。                                           |
| **HANA 实例**         | 单个 Azure VM 上的单个 SAP HANA 实例 - 仅限纵向扩展 | 单个 VM 上的多个 SAP HANA 实例。 一次只能保护这些多个实例中的一个。                  |
| **HANA 数据库类型**    | 1\.x 上的单一数据库容器 (SDC)、2.x 上的多数据库容器 (MDC) | HANA 1.x 中的 MDC                                              |
| **HANA 数据库大小**     | 大小不超过 8 TB 的 HANA 数据库（这不是 HANA 系统的内存大小）               |                                                              |
| **备份类型**           | 完整备份、差异备份、增量备份和日志备份                          |  快照                                       |
| **还原类型**          | 请参阅 SAP HANA 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148)，了解支持的还原类型 |                                                              |
| **备份限制**          | 每个 SAP HANA 实例最多可以进行 8 TB 的完整备份（软限制）         |                                                              |
| **特殊配置** |                                                              | SAP HANA + 动态分层 <br>  通过 LaMa 进行克隆        |

------

>[!NOTE]
>备份在 Azure VM 中运行的 SAP HANA 数据库时，Azure 备份不会针对夏令时更改自动进行调整。
>
>请根据需要手动修改策略。

> [!NOTE]
> 现在可以在 Azure 门户中[监视到同一计算机的备份和还原](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal)作业，这些作业是从 HANA 原生客户端 (SAP HANA Studio/Cockpit/DBA Cockpit) 触发的。

## <a name="next-steps"></a>后续步骤

* 了解如何[备份在 Azure VM 上运行的 SAP HANA 数据库](./backup-azure-sap-hana-database.md)
* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](./sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](sap-hana-db-manage.md)
* 了解[关于 SAP HANA 数据库备份常见问题的疑难解答](./backup-azure-sap-hana-database-troubleshoot.md)
