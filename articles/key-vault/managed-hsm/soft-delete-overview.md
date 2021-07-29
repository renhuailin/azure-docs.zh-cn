---
title: Azure 密钥保管库管理的 HSM 软删除 | Microsoft Docs
description: 通过在托管 HSM 中进行软删除，可以恢复已删除的 HSM 实例和密钥。
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 06/01/2021
ms.openlocfilehash: 673260fb8b78cb08ccd8581862238dc2e0341362
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414912"
---
# <a name="managed-hsm-soft-delete-overview"></a>托管 HSM 软删除概述

> [!IMPORTANT]
> 默认情况下，已为托管 HSM 资源启用软删除。 无法关闭。

> [!IMPORTANT]
> 软删除的托管 HSM 资源将继续按其完整的每小时费率计费，直到被清除为止。

托管 HSM 的软删除功能可以恢复已删除的 HSM 和密钥。 具体而言，此保护机制提供以下保护：

- 一旦 HSM 或密钥被删除，它将在 7 到 90 个日历日（可配置的周期）保持可恢复。 可在创建 HSM 期间设置保留期。 如果未指定值，默认保留期将会被设置为 90 天。 这样，用户就有充足的时间来注意到意外的密钥或 HSM 删除并做出响应。
- 若要永久删除密钥，必须执行两个操作。 首先，用户必须删除该密钥，使其处于软删除状态。 其次，用户必须清除处于软删除状态的密钥。 清除操作要求用户分配“托管 HSM 加密管理人员”角色。 这些额外的保护措施减少了用户意外或恶意删除密钥或 HSM 的风险。


## <a name="soft-delete-behavior"></a>软删除行为

对于托管 HSM，默认开启软删除。 在禁用了软删除的情况下，无法创建托管 HSM 资源。

启用软删除后，标记为“已删除资源”的资源将保留指定的时间（默认为 90 天）。 服务进一步提供了用于恢复已删除的 HSM 或密钥的机制，允许撤消删除操作。

默认保持期为 90 天，但在 HSM 资源创建期间，可将保留策略间隔设置为介于 7 到 90 天之间的值。 清除保护保留策略使用相同的间隔。 设置保留策略间隔后，将无法更改。

在保留期结束并且 HSM 资源被清除之前，不能重复使用已软删除的 HSM 资源的名称。

## <a name="purge-protection"></a>清除保护

清除保护是一种可选行为，**默认情况下不启用**。 可以通过 [CLI](./recovery.md?tabs=azure-cli) 或 [PowerShell](./recovery.md?tabs=azure-powershell) 来启用它。

启用清除保护后，在保留期结束之前，无法清除处于已删除状态的 HSM 或密钥。 软删除的 HSM 和密钥仍可恢复，这可以确保遵循保留策略。

默认保持期为 90 天，但可以在创建 HSM 时将保留策略间隔设置为介于 7 到 90 天之间的值。 保留策略间隔只能在创建 HSM 时设置。 之后无法进行更改。

请参阅[如何通过 CLI 来使用托管 HSM 软删除](./recovery.md?tabs=azure-cli#managed-hsm-cli)或[如何通过 PowerShell 来使用托管 HSM 软删除](./recovery.md?tabs=azure-powershell#managed-hsm-powershell)。

## <a name="managed-hsm-recovery"></a>托管 HSM 恢复

删除 HSM 后，服务会在订阅下创建代理资源，添加足够的元数据以用于恢复。 代理资源是一个存储对象，位于与已删除 HSM 相同的位置。 

## <a name="key-recovery"></a>密钥恢复

删除密钥时，服务会将该对象置于已删除状态，使其不可供任何操作访问。 在此状态下，密钥只能被列出、恢复或清除（永久删除）。 要查看对象，请使用 Azure CLI `az keyvault key list-deleted` 命令（如[使用 CLI 进行托管 HSM 软删除和清理保护](./recovery.md?tabs=azure-cli#keys-cli)）或 Azure PowerShell `-InRemovedState` 参数（如[使用 PowerShell 进行托管 HSM 软删除和清理保护](./recovery.md?tabs=azure-powershell#keys-powershell)）。  

同时，托管 HSM 将计划删除对应于已删除 HSM 或密钥的底层数据，以便在预定的保留间隔之后执行。 在保留间隔期间，还会保留与 HSM 相对应的 DNS 记录。

## <a name="soft-delete-retention-period"></a>软删除保留期

软删除的资源将保留设定的一段时间（90 天）。 在软删除保留间隔内，以下情况属实：

- 可列出订阅中处于软删除状态的所有 HSM 和密钥，并可访问与之有关的删除和恢复信息。
  - 只有具有托管 HSM 参与者角色的用户才能列出已删除的 HSM。 我们建议用户创建一个具有这些特殊权限的自定义角色来处理已删除的保管库。
- 无法在同一位置创建具有相同名称的托管 HSM；相应地，如果给定 HSM 中包含具有相同名称且处于已删除状态的对象，则无法在给定 HSM 中创建密钥。
- 只有具有“托管 HSM 参与者”角色的用户才能列出、查看、恢复和清除托管的 HSM。
- 只有具有“托管 HSM 加密管理人员”角色的用户才能列出、查看、恢复和清除密钥。
  
除非托管 HSM 或密钥恢复，否则在保留时间间隔结束时，服务将对软删除的 HSM 或密钥执行清除。 资源删除不可重新计划。

### <a name="billing-implications"></a>计费影响

托管 HSM 是单租户服务。 当你创建托管 HSM 时，该服务将保留分配给 HSM 的基础资源。 即使 HSM 处于已删除状态，这些资源仍然处于被分配状态。 因此，在 HSM 处于已删除状态时，仍将向你收费。

## <a name="next-steps"></a>后续步骤

以下两个指南提供有关使用软删除的主要使用方案。

- [如何将托管 HSM 软删除与 PowerShell 配合使用](./recovery.md?tabs=azure-powershell) 
- [如何通过 CLI 使用托管 HSM 软删除](./recovery.md?tabs=azure-cli)
