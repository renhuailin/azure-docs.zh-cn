---
title: Azure 密钥保管库管理的 HSM 软删除 | Microsoft Docs
description: 通过在托管 HSM 中进行软删除，可以恢复已删除的 HSM 实例和密钥。 本文概述了此功能。
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: b832aa066e1d31bfc064f988c722d2503ff96507
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195048"
---
# <a name="managed-hsm-soft-delete-overview"></a>托管 HSM 软删除概述

> [!IMPORTANT]
> 无法为托管 HSM 资源关闭软删除。

> [!IMPORTANT]
> 已软删除的托管 HSM 资源将继续按其完整的每小时费率计费，直到被清除为止。

托管 HSM 的软删除功能可以恢复已删除的 HSM 和密钥。 具体而言，此功能提供以下安全措施：

- 删除 HSM 或密钥后，它仍然可以在 7 到 90 个日历日的可配置周期内恢复。 可以在创建 HSM 时设置保持期。 如果未指定值，则使用默认保持期 90 天。 此时间段使用户有充足的时间来注意到意外的密钥或 HSM 删除，并做出响应。
- 若要永久删除密钥，用户需要执行两个操作。 首先，必须删除该密钥，使其处于软删除状态。 其次，必须清除处于软删除状态的密钥。 清除操作需要“托管 HSM 加密管理人员”角色。 这些额外的保护措施减少了用户意外或恶意删除密钥或 HSM 的风险。


## <a name="soft-delete-behavior"></a>软删除行为

无法为托管 HSM 资源关闭软删除。

标记为已删除的资源将在指定时间段内保留。 还有一种用于恢复已删除的 HSM 或密钥的机制，因此可以撤消删除。

默认保持期为 90 天。 创建 HSM 资源时，可以将保留策略间隔设置为 7 到 90 天之间的值。 清除保护保留策略使用相同的间隔。 设置保留策略后，无法更改它。

在保留期结束并且 HSM 资源被清除之前，不能重复使用已软删除的 HSM 资源的名称。

## <a name="purge-protection"></a>清除保护

清除保护是一种可选行为。 默认情况下不启用此功能。 可以使用 [Azure CLI](./recovery.md?tabs=azure-cli) 或 [PowerShell](./recovery.md?tabs=azure-powershell) 启用它。

启用清除保护后，在保持期结束之前，无法清除处于已删除状态的 HSM 或密钥。 已软删除的 HSM 和密钥仍可恢复，这确保了保留策略的有效性。

默认保持期为 90 天。 创建 HSM 资源时，可以将保留策略间隔设置为 7 到 90 天之间的值。 只有在创建 HSM 时，才能设置保留策略间隔。 之后无法对此进行更改。

请参阅[如何通过 CLI 来使用托管 HSM 软删除](./recovery.md?tabs=azure-cli#managed-hsms-cli)或[如何通过 PowerShell 来使用托管 HSM 软删除](./recovery.md?tabs=azure-powershell#managed-hsms-powershell)。

## <a name="managed-hsm-recovery"></a>托管 HSM 恢复

删除 HSM 时，服务在订阅中创建代理资源，以添加足够的元数据来启用恢复。 代理资源是一个存储对象。 它与已删除的 HSM 在同一位置提供。 

## <a name="key-recovery"></a>密钥恢复

删除密钥时，服务会将该对象置于已删除状态，使其不可供任何操作访问。 在此状态下，密钥只能被列出、恢复或清除。 要查看对象，请使用 Azure CLI `az keyvault key list-deleted` 命令（如[使用 CLI 进行托管 HSM 软删除和清除保护](./recovery.md?tabs=azure-cli#keys-cli)中所述）或 Azure PowerShell `-InRemovedState` 参数（如[使用 PowerShell 进行托管 HSM 软删除和清除保护](./recovery.md?tabs=azure-powershell#keys-powershell)中所述）。  

删除密钥时，托管 HSM 将计划删除对应于已删除 HSM 或密钥的基础数据，以便在预定的保留间隔之后执行。 在保留间隔期间，还会保留与 HSM 相对应的 DNS 记录。

## <a name="soft-delete-retention-period"></a>软删除保留期

软删除的资源将保留一段时间：90 天。 在软删除保留间隔期间，以下条件适用：

- 可以列出订阅所有处于软删除状态的 HMS 和密钥。 还可以访问有关删除和恢复的信息。
- 只有具有“托管 HSM 参与者”角色的用户才能列出已删除的 HSM。 建议使用这些权限创建自定义角色来处理已删除的保管库。
- 托管 HSM 名称在给定位置中必须是唯一的。 创建密钥时，如果 HSM 包含一个名称处于已删除状态的密钥，则不能使用该名称。
- 只有具有“托管 HSM 参与者”角色的用户才能列出、查看、恢复和清除托管的 HSM。
- 只有具有“托管 HSM 加密管理人员”角色的用户才能列出、查看、恢复和清除密钥。
  
除非托管 HSM 或密钥恢复，否则在保留时间间隔结束时，服务将对软删除的 HSM 或密钥执行清除。 无法重新计划删除资源。

### <a name="billing-implications"></a>计费影响

托管 HSM 是单租户服务。 在你创建托管 HSM 时，该服务将保留分配给 HSM 的基础资源。 即使 HSM 处于已删除状态，这些资源仍然处于已分配状态。 当 HSM 处于已删除状态时，将收取相应费用。

## <a name="next-steps"></a>后续步骤

这些文章介绍了使用软删除的主要场景：

- [如何将托管 HSM 软删除与 PowerShell 配合使用](./recovery.md?tabs=azure-powershell) 
- [如何将托管 HSM 软删除与 Azure CLI 结合使用](./recovery.md?tabs=azure-cli)
