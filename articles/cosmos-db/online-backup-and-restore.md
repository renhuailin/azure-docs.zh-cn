---
title: Azure Cosmos DB 中的联机备份和按需数据还原。
description: 本文介绍了自动备份和按需数据还原的工作原理。 还说明了连续和定期备份模式之间的差异。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9441cb178769b7d8cad6e60d0bf411c4c38710c5
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473452"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB 中的联机备份和按需数据还原
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 会定期自动备份数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份都单独存储在某个存储服务中。 如果意外删除或更新了 Azure Cosmos 帐户、数据库或容器，而稍后需要恢复数据，那么在这种情况下自动备份非常有用。 有两个备份模式：

* 定期备份模式 - 对于所有现有帐户，此模式是默认备份模式。 在此模式下，将定期执行备份，并通过创建支持团队请求来还原数据。 在此模式下，你将为帐户配置备份间隔和保留期。 最长保留期可延长至一个月。 最小备份间隔可以是一小时。  若要了解详细信息，请参阅[定期备份模式](configure-periodic-backup-restore.md)一文。

* 连续备份模式：创建 Azure Cosmos DB 帐户时选择此模式。 在此模式下，可在过去 30 天内还原到任何时间点。 如要了解详细信息，请参阅[连续备份模式简介](continuous-backup-restore-introduction.md)，通过 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。

  > [!NOTE]
  > 如果为新账户配置连续备份，则可以通过 Azure 门户、PowerShell 或 CLI 进行自助式还原。 如果帐户配置为连续模式，则无法将其切换回定期模式。

对于启用了 Azure Synapse Link 的帐户，备份和还原中不包含分析存储数据。 启用 Synapse Link 后，Azure Cosmos DB 将继续按计划的备份间隔自动备份事务存储中的数据。 目前不支持自动备份和还原分析存储中的数据。

## <a name="next-steps"></a>后续步骤

接下来，你可以了解如何配置和管理帐户的定期和连续备份模式：

* [配置和管理定期备份](configure-periodic-backup-restore.md)策略。
* 什么是[连续备份](continuous-backup-restore-introduction.md)模式？
* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原连续备份帐户。
* [将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
