---
title: 如何满足 Azure Cosmos DB 的数据驻留要求
description: 了解满足 Azure Cosmos DB 的数据驻留要求，在单个区域中保留数据和备份。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491859"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>如何满足 Azure Cosmos DB 的数据驻留要求

通过 Azure Cosmos DB 可以将数据和备份配置并保留到单个区域中，满足[驻留要求。](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>数据驻留要求

在 Azure Cosmos DB，必须显式配置跨区域数据复制。 了解如何使用 [Azure 门户](how-to-manage-database-account.md#addremove-regions-from-your-database-account)和 [Azure CLI](scripts/cli/common/regions.md) 配置异地复制数据。 为满足数据驻留要求，可以创建一个 Azure 策略，阻止某些区域向不需要的区域复制数据。

## <a name="residency-requirements-for-backups"></a>备份的驻留要求

连续模式备份：默认情况下，这些备份存储在本地冗余或区域冗余存储中。 了解更多信息，请参阅[连续备份](continuous-backup-restore-portal.md)一文。

周期模式备份：默认情况下，周期模式备份会存储到异地冗余存储中。 对于周期备份模式，可以在帐户级别配置数据冗余。 备份存储有三个冗余选项。 分别是本地冗余、区域冗余或异地冗余。 了解更多信息，请参阅如何使用门户[配置备份冗余](configure-periodic-backup-restore.md#configure-backup-interval-retention)。

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>使用 Azure Policy 强制实施驻留要求

如果你有将所有数据保留在单个 Azure 区域中的数据冗余要求，可以使用 Azure Policy 为帐户强制实施零冗余或本地冗余备份。  还可以强制执行策略：不将 Azure Cosmos DB 帐户异地复制到其他区域。

Azure Policy 是一项服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure Policy 可帮助你确保这些资源始终符合公司标准和服务级别协议。 更多信息，请参阅如何使用 [Azure Policy](policy.md) 实现 Azure Cosmos DB 资源的治理和控制

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](configure-periodic-backup-restore.md)配置和管理周期备份
* 使用 [Azure 门户](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md)、[CLI](continuous-backup-restore-command-line.md) 或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
