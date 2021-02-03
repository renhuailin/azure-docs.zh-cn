---
title: Azure Cosmos DB 中的联机备份和按需数据还原。
description: 本文介绍自动备份、按需数据还原的工作原理。 它还说明了连续和定期备份模式之间的差异。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525409"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB 中的联机备份和按需数据还原
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 会定期自动备份数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份都单独存储在存储服务中。 如果意外删除或更新了 Azure Cosmos 帐户、数据库或容器，而稍后需要恢复数据，那么在这种情况下自动备份非常有用。 有两种备份模式：

* **定期备份模式** -对于所有现有帐户，此模式是默认的备份模式。 在此模式下，将定期执行备份，并通过创建支持团队请求来还原数据。 在此模式下，你将为你的帐户配置备份间隔和保留期。 最长保留期为一个月。 最小备份间隔可以是一小时。  若要了解详细信息，请参阅 [定期备份模式](configure-periodic-backup-restore.md) 一文。

* **连续备份模式** (当前在公共预览版) –在创建 Azure Cosmos DB 帐户时选择此模式。 此模式允许您在过去的30天内还原到任何时间点。 若要了解详细信息，请参阅 [连续备份模式简介](continuous-backup-restore-introduction.md)，配置 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md)、 [CLI](continuous-backup-restore-command-line.md)和 [资源管理器](continuous-backup-restore-template.md) 文章的连续备份。

  > [!NOTE]
  > 如果使用连续备份配置新帐户，可以通过 Azure 门户、PowerShell 或 CLI 执行自助服务还原。 如果你的帐户配置为连续模式，则无法将其切换回定期模式。 当前具有定期备份模式的现有帐户不能更改为连续模式。  

## <a name="next-steps"></a>后续步骤

接下来，你可以了解如何配置和管理帐户的定期和连续备份模式：

* [配置和管理定期备份](configure-periodic-backup-restore.md) 策略。
* 什么是 [连续备份](continuous-backup-restore-introduction.md) 模式？
* 使用 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md)、 [CLI](continuous-backup-restore-command-line.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。
