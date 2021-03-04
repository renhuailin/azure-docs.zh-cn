---
title: 迁移 Azure Monitor 预警规则
description: 了解如何使用自愿性迁移工具迁移经典警报规则。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fa487bec49ab9faa0f7c3dce752a30e4440fb873
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037670"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自愿性迁移工具迁移经典警报规则

如前所述 [，对于](monitoring-classic-retirement.md)公有云用户，Azure Monitor 中的经典警报将会停用，但在31年 **5 月31日之前仍2021会** 受到限制。 Azure 政府云和 Azure 中国世纪互联的经典警报将于 **2024 年2月29日** 停用。

在 Azure 门户向使用经典警报规则的客户和想要触发迁移的客户提供迁移工具。 本文介绍如何使用迁移工具。

## <a name="before-you-migrate"></a>迁移之前

迁移过程会将经典警报规则转换为新的等效警报规则，并创建操作组。 在准备期间，请注意以下几点：

- 通知负载格式和用于创建和管理新警报规则的 Api 不同于经典警报规则，因为它们支持更多功能。 [了解如何准备迁移](alerts-prepare-migration.md)。

- 使用该工具无法迁移某些经典警报规则。 [了解无法迁移哪些规则及其解决方法](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 在完成迁移并且新的警报规则生效之前，这些经典规则会继续运行并发送警报。

## <a name="how-to-use-the-migration-tool"></a>如何使用迁移工具

若要在 Azure 门户中触发经典警报规则的迁移，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中选择“监视”。

1. 选择“警报”，然后选择“管理警报规则”或“查看经典警报”。

1. 选择“迁移到新规则”转到迁移登陆页。 此页显示所有订阅的列表及其迁移状态：

    ![屏幕截图显示 "迁移警报规则" 页。](media/alerts-using-migration-tool/migration-landing.png "迁移规则")

    可以使用该工具迁移的所有订阅已标记为“准备好迁移”。

    > [!NOTE]
    > 迁移工具将分阶段推出到使用经典警报规则的所有订阅。 在推出的早期阶段，可能会看到某些订阅标记为未准备好迁移。

1. 选择一个或多个订阅，然后选择“预览迁移”。

    生成的页将显示每次要为一个订阅迁移的经典警报规则的详细信息。 还可以选择“下载此订阅的迁移详细信息”，以获取 CSV 格式的详细信息。

    ![屏幕截图显示 "迁移警报规则" 页，其中包含用于下载此订阅的迁移详细信息的链接，你可以指定用于迁移通知的电子邮件。](media/alerts-using-migration-tool/migration-preview.png "预览迁移")

1. 指定用于接收迁移状态通知的一个或多个电子邮件地址。 迁移完成或者需要你采取任何措施时，你会收到电子邮件。

1. 选择 " **开始迁移**"。 阅读确认对话框中显示的信息，并确认已准备好开始迁移。

    > [!IMPORTANT]
    > 启动订阅迁移后，无法编辑或创建该订阅的经典警报规则。 这种限制可以确保在迁移到新规则期间，不会丢失对经典警报规则所做的任何更改。 尽管无法更改经典警报规则，但在完成迁移之前，这些规则仍会继续运行并提供警报。 完成订阅的迁移后，不再可以使用经典警报规则。

    ![屏幕截图显示了有关迁移的确认提示，其中包含重要信息，其中包含链接以了解详细信息，然后再继续。](media/alerts-using-migration-tool/migration-confirm.png "确认开始迁移")

1. 迁移完成或者需要你采取措施时，前面提供的地址会收到电子邮件。 也可以在门户中的迁移登陆页上定期检查状态。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>我的订阅为何列为未准备好迁移？

迁移工具分阶段向客户推出。 在早期阶段，你的大部分或所有订阅可能标记为“尚未准备好迁移”。 

当某个订阅已准备好迁移时，订阅所有者会收到一封电子邮件，指出该工具可用。 请密切关注此邮件。

### <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

在订阅级别向其分配了 "监视参与者" 角色的用户可以触发迁移。 [详细了解 Azure 基于角色的访问控制的迁移过程](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-will-the-migration-take"></a>迁移需要多长时间？

大多数订阅在一小时内即可完成迁移。 可以在迁移登陆页上跟踪迁移进度。 在迁移期间，请确保警报仍在经典警报系统或新的系统中运行。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果在迁移期间遇到问题，该怎么办？

请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)来解决迁移期间可能遇到的问题。 如果需要你采取某种措施才能完成迁移，设置该工具时提供的电子邮件地址会收到相关的通知。

## <a name="next-steps"></a>后续步骤

- [迁移准备](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)