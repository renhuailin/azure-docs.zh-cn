---
title: 了解 Azure Monitor 经典警报的自动迁移过程的工作原理
description: 了解自动迁移过程的工作原理。
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 92aaffcea7a7c96cd77aade318520b093eed3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045456"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>了解经典警报规则的自动迁移过程

如[以前公告](monitoring-classic-retirement.md)的那样，Azure Monitor 中的经典警报已对公有云用户停用，但在 2021 年 5 月 31 日之前仍可有限制地使用它。 针对 Azure 政府云和 Azure 中国世纪互联的经典警报将于 2024 年 2 月 29 日停用。

Azure 门户中提供了一个可供客户自行触发迁移的[迁移工具](alerts-using-migration-tool.md)。 本文介绍了公有云中的自动迁移过程，该过程将于 2021 年 5 月 31 日之后启动。 还详细介绍了你可能会遇到的问题及其解决方案。

## <a name="important-things-to-note"></a>需要注意的重要事项

迁移过程会将经典警报规则转换为新的等效警报规则，并创建操作组。 在准备期间，请注意以下几点：

- 新警报规则的通知有效负载格式不同于经典警报规则的有效负载，因为它们支持更多的功能。 如果你对逻辑应用、Runbook 或 Webhook 有一个经典警告规则，该规则可能会在迁移后如预期的那样停止工作，因为有效负载存在差异。 [了解如何准备迁移](alerts-prepare-migration.md)。

- 一些经典警报规则无法通过使用工具进行迁移。 [了解无法迁移哪些规则及其解决方法](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)。

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>在公有云中的自动迁移过程期间会发生什么情况？

- 从 2021 年 5 月 31 日开始，你将无法创建任何新的经典警报规则，经典警报的迁移将分批触发。
- 监视已删除的目标资源或针对[不再受支持的指标](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)的任何经典警报规则都被视为无效。
- 在 2021 年 5 月 31 日之后，无效的经典警报规则将被删除。
- 对订阅的迁移开始后，它应该在一个小时内完成。 客户可在 [Azure Monitor 中的迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)上监视迁移的状态。
- 订阅所有者将收到有关迁移成功或失败的电子邮件。

    > [!NOTE]
    > 如果不想等待自动迁移过程启动，仍可使用迁移工具自行触发迁移。

## <a name="what-if-the-automatic-migration-fails"></a>如果自动迁移失败，怎么办？

当自动迁移过程失败时，订阅所有者将收到一封电子邮件，让他们知道这个问题。 可使用 Azure Monitor 中的迁移工具查看问题的完整详细信息。 请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)来解决迁移期间可能遇到的问题。

  > [!NOTE]
  > 如果需要客户采取操作（例如暂时禁用资源锁或更改策略分配），客户需要解决任何此类问题。 如果到那时还未解决问题，则无法保证成功迁移经典警报。

## <a name="next-steps"></a>后续步骤

- [迁移准备](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)