---
title: Azure 角色分配条件（预览版）的先决条件
description: Azure 角色分配条件（预览版）的先决条件。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: e0da284f7ce9ac1c8699a6f22f2144ff6f3f8c80
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489556"
---
# <a name="prerequisites-for-azure-role-assignment-conditions-preview"></a>Azure 角色分配条件（预览版）的先决条件

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

若要添加或编辑 Azure 角色分配条件，必须满足以下先决条件。

## <a name="storage-accounts"></a>存储帐户

对于那些使用 blob 索引标记的条件，你必须使用与 blob 索引功能兼容的存储帐户。 例如，当前仅支持禁用了分层命名空间 (HNS) 的常规用途 v2 (GPv2) 存储帐户。 有关详细信息，请参阅[使用 blob 索引标记（预览版）管理和查找 Azure Blob 数据](../storage/blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)

## <a name="azure-powershell"></a>Azure PowerShell

使用 Azure PowerShell 添加或更新条件时，必须使用以下版本：

- [Az 模块 5.5.0 或更高版本](https://www.powershellgallery.com/packages/Az/5.5.0)
- [Az.Resources 模块 3.2.1 或更高版本](https://www.powershellgallery.com/packages/Az.Resources/3.2.1)
    - 随 Az 模块 v5.5.0 及更高版本提供，但可以通过 PowerShell 库手动安装
- [Az.Storage 预览版模块 2.5.2-preview 或更高版本](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview)

## <a name="azure-cli"></a>Azure CLI

使用 Azure CLI 添加或更新条件时，必须使用以下版本：

- [Azure CLI 2.18 或更高版本](/cli/azure/install-azure-cli)

## <a name="permissions"></a>权限

就像角色分配一样，若要添加或更新条件，你必须使用具有 `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限的用户（例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)）登录到 Azure。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
- [教程：使用 Azure 门户添加角色分配条件来限制对 Blob 的访问（预览版）](../storage/common/storage-auth-abac-portal.md)
