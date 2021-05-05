---
title: 使用 Azure Key Vault 托管 HSM 的最佳做法
description: 本文档介绍了一些使用密钥保管库的最佳做法
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "90994277"
---
# <a name="best-practices-when-using-managed-hsm"></a>使用托管 HSM 时的最佳做法

## <a name="control-access-to-your-managed-hsm"></a>控制对托管 HSM 的访问权限

托管 HSM 是一种保护加密密钥的云服务。 由于这些密钥很敏感且对业务至关重要，因此只能允许授权的应用程序和用户使用，确保对托管 HSM 的访问安全性。 [此文](access-control.md)简要介绍了访问模型。 它说明了身份验证和授权以及基于角色的访问控制。
- 为 HSM 管理员创建 [Azure Active Directory 安全组](../../active-directory/fundamentals/active-directory-manage-groups.md)（而不是将“管理员”角色分配给个人）。 这可防止在删除单个帐户时出现“管理锁定”的情况。
- 锁定对管理组、订阅、资源组和托管 HSM 的访问 - 使用 Azure RBAC 控制对管理组、订阅和资源组的访问
- 使用[托管 HSM 本地 RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac) 创建按密钥进行的角色分配
- 使用最低特权访问主体来分配角色

## <a name="choose-regions-that-support-availability-zones"></a>选择支持可用性区域 (zone) 的区域 (region)

- 为了确保将高可用性和区域复原能力提升到最佳，请选择支持[可用性区域](../../availability-zones/az-overview.md)的 Azure 区域。 这些区域在 Azure 门户中显示为“建议的区域”。

## <a name="backup"></a>备份

- 请确保定期备份 HSM。 可以在 HSM 级别进行备份，还可以对特定密钥进行备份。 

## <a name="turn-on-logging"></a>启用日志

- 为 HSM [启用日志记录](logging.md)。 同时设置警报。

## <a name="turn-on-recovery-options"></a>启用恢复选项

- 默认启用[软删除](../general/soft-delete-overview.md)。
- 如果要防止强制删除 HSM，即使在启用软删除后，也要启用清除保护。

## <a name="next-steps"></a>后续步骤

- 有关完整 HSM 备份/还原的信息，请参阅[完整备份/还原](backup-restore.md)。
- 若要了解如何使用 Azure Monitor 来配置日志记录，请参阅[托管 HSM 日志记录](logging.md)
- 有关密钥管理，请参阅[管理托管 HSM 密钥](key-management.md)。
- 请参阅[托管 HSM 角色管理](role-management.md)，了解如何管理角色分配。
