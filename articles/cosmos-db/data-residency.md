---
title: 如何满足 Azure Cosmos DB 的数据驻留要求
description: 了解满足 Azure Cosmos DB 的数据驻留要求，在单个区域中保留数据和备份。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 869b28c8c65c402936dcb7b7641a6e4af8db3f47
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100882"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>如何满足 Azure Cosmos DB 的数据驻留要求
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

通过 Azure Cosmos DB 可以将数据和备份配置并保留到单个区域中，满足[驻留要求。](https://azure.microsoft.com/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>数据驻留要求

在 Azure Cosmos DB，必须显式配置跨区域数据复制。 了解如何使用 [Azure 门户](how-to-manage-database-account.md#addremove-regions-from-your-database-account)和 [Azure CLI](scripts/cli/common/regions.md) 配置异地复制数据。 为了满足数据驻留需求，可以创建 Azure Policy 定义，以允许某些区域阻止数据复制到不需要的区域。

## <a name="residency-requirements-for-backups"></a>备份的驻留要求

连续模式备份：默认情况下，这些备份存储在本地冗余或区域冗余存储中。 了解更多信息，请参阅[连续备份](provision-account-continuous-backup.md)一文。

周期模式备份：默认情况下，周期模式备份会存储到异地冗余存储中。 对于周期备份模式，可以在帐户级别配置数据冗余。 备份存储有三个冗余选项。 分别是本地冗余、区域冗余或异地冗余。 了解更多信息，请参阅如何使用门户[配置备份冗余](configure-periodic-backup-restore.md#configure-backup-interval-retention)。

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>使用 Azure Policy 强制实施驻留要求

如果你有将所有数据保留在单个 Azure 区域中的数据冗余要求，可以使用 Azure Policy 为帐户强制实施零冗余或本地冗余备份。  还可以强制执行策略：不将 Azure Cosmos DB 帐户异地复制到其他区域。

Azure Policy 是一项服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure Policy 可帮助你确保这些资源始终符合公司标准和服务级别协议。 更多信息，请参阅如何使用 [Azure Policy](policy.md) 实现 Azure Cosmos DB 资源的治理和控制

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](configure-periodic-backup-restore.md)配置和管理周期备份
* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原连续备份帐户。
* [将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。

