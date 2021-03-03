---
title: 了解 Azure Monitor 经典警报的自动迁移过程是如何工作的
description: 了解自动迁移过程的工作方式。
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734853"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>了解经典警报规则的自动迁移过程

如前所述 [，对于](monitoring-classic-retirement.md)公有云用户，Azure Monitor 中的经典警报将会停用，但在31年 **5 月31日之前仍2021会** 受到限制。 Azure 政府云和 Azure 中国世纪互联的经典警报将于 **2024 年2月29日** 停用。

Azure 门户中提供[了一个迁移工具](alerts-using-migration-tool.md)，供客户自行触发迁移。 本文介绍了公有云中的自动迁移过程，该过程将在31年5月2021后开始。 它还详细介绍了你可能会遇到的问题和解决方案。

## <a name="important-things-to-note"></a>需要注意的重要事项

迁移过程会将经典警报规则转换为新的等效警报规则，并创建操作组。 在准备期间，请注意以下几点：

- 新警报规则的通知负载格式不同于经典警报规则的负载，因为它们支持更多功能。 如果有使用逻辑应用、runbook 或 webhook 的经典警报规则，则在迁移后，它们可能会因负载不同而停止按预期方式工作。 [了解如何准备迁移](alerts-prepare-migration.md)。

- 不能使用工具迁移某些经典警报规则。 [了解无法迁移哪些规则以及](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)如何处理它们。

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>在公有云中的自动迁移过程中将发生什么情况？

- 从31年5月 2021 5 日开始，你将无法创建任何新的经典警报规则，并将成批触发经典警报的迁移。
- 监视已删除目标资源或 [不再受支持的度量值](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) 的任何经典警报规则都被视为无效。
- 在31月 2021 5 日后，将删除无效的经典警报规则。
- 一旦迁移开始后，就会在一小时内完成。 客户可以在 [Azure Monitor 的迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)上监视迁移状态。
- 如果迁移成功或失败，订阅所有者将收到一封电子邮件。

    > [!NOTE]
    > 如果你不想等待自动迁移过程开始，你仍可以使用迁移工具主动触发迁移。

## <a name="what-if-the-automatic-migration-fails"></a>如果自动迁移失败，该怎么办？

当自动迁移过程失败时，订阅所有者将收到一封电子邮件，通知他们此问题。 你可以使用 Azure Monitor 中的迁移工具来查看问题的完整详细信息。 请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)来解决迁移期间可能遇到的问题。

  > [!NOTE]
  > 如果客户需要执行操作（如暂时禁用资源锁定或更改策略分配），则客户需要解决此类问题。 如果未解决问题，则无法保证经典警报的成功迁移。

## <a name="next-steps"></a>后续步骤

- [迁移准备](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)