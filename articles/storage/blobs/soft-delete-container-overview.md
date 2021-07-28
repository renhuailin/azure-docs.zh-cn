---
title: 容器软删除（预览版）
titleSuffix: Azure Storage
description: 容器软删除可以保护数据，以便在应用程序或其他存储帐户用户错误地修改或删除数据后可以更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 2efd673d26231e83d820f7971a740d06e9b2a1d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785408"
---
# <a name="soft-delete-for-containers-preview"></a>容器软删除（预览版）

容器软删除（预览版）可防止意外或恶意删除数据。 在为存储帐户启用容器软删除后，在指定的时间段内，已删除的容器及其内容会保留在 Azure 存储中。 在这段保留期内，可以还原以前删除的容器。 还原容器会还原容器在被删除时其中原有的任何 Blob。

为实现对 Blob 数据的端到端保护，Microsoft 建议启用以下数据保护功能：

- 容器软删除，用于还原已删除的容器。 若要了解如何启用容器软删除，请参阅[启用和管理容器的软删除](soft-delete-container-enable.md)。
- Blob 版本控制，用于自动维护 blob 的先前版本。 启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原 blob 的先前版本以恢复数据。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。
- Blob 软删除，用于还原已删除的 blob 或版本。 若要了解如何启用 blob 软删除，请参阅[启用和管理 blob 的软删除](soft-delete-blob-enable.md)。

> [!IMPORTANT]
> 容器软删除目前处于预览阶段。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-container-soft-delete-works"></a>容器软删除的工作原理

启用容器软删除后，可以为已删除的容器指定保持期，介于 1 到 365 天之间。 默认保持期为 7 天。 在保持期内，可以通过调用还原容器操作来恢复已删除的容器。

还原容器时，容器的 blob 和任何 blob 版本也会一同还原。 但是，如果容器本身已被删除，则只能使用容器软删除来还原 blob。 要在尚未删除其父容器时还原已删除的 blob，必须使用 blob 软删除或 blob 版本控制。

> [!WARNING]
> 容器软删除只能还原整个容器及其在删除时的内容。 无法使用容器软删除还原容器中已删除的 blob。 Microsoft 建议同时启用 blob 软删除和 blob 版本控制，以保护容器中的各个 blob。

下图显示了在启用容器软删除后，如何还原已删除的容器：

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="显示如何还原软删除容器的示意图":::

还原容器时，可以将其还原为原始名称（前提是该名称未被重用）。 如果原始容器名称已被使用，则可以使用新名称还原容器。

保持期到期后，容器将从 Azure 存储永久删除，并且无法恢复。 保持期自删除容器的那一刻开始计算。 你可以随时更改保持期，但要记住，更新的保持期仅适用于新删除的容器。 先前删除的容器将以它被删除时生效的保持期为准永久删除。

禁用容器软删除后，并不会永久删除先前软删除的容器。 任何软删除的容器将在它被删除时生效的保持期到期后被永久删除。

> [!IMPORTANT]
> 容器软删除并不能防止删除存储帐户。 它只能防止删除该帐户中的容器。 若要防止删除存储账户，请在存储帐户资源上配置锁定。 有关锁定存储帐户的详细信息，请参阅[将 Azure 资源管理器锁定应用于存储帐户](../common/lock-account-resource.md)。

## <a name="about-the-preview"></a>关于此预览版

容器软删除在所有 Azure 区域中均以预览版提供。

Azure 存储 REST API 2019-12-12 版或更高版本支持容器软删除。

### <a name="storage-account-support"></a>存储帐户支持

容器软删除可用于以下类型的存储帐户：

- 常规用途 v2 和 v1 存储帐户
- 块 Blob 存储帐户
- Blob 存储帐户

还支持启用了分层命名空间的可以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。

### <a name="register-for-the-preview"></a>注册预览版

若要注册容器软删除预览版，请使用 PowerShell 或 Azure CLI 提交请求以向订阅注册此功能。 批准请求后，可以使用任何新的或现有的常规用途 v2、Blob 存储或高级块 Blob 存储帐户启用容器软删除。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要与 PowerShell 注册，请调用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令。

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 注册，请调用 [az feature register](/cli/azure/feature#az_feature_register) 命令。

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>检查注册状态

若要查看注册状态，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查注册状态，请调用 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 检查注册状态，请调用 [az feature](/cli/azure/feature#az_feature_show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>定价和计费

启用容器软删除无需额外付费。 软删除容器中的数据按与活动数据相同的费率计费。

## <a name="next-steps"></a>后续步骤

- [配置容器软删除](soft-delete-container-enable.md)
- [blob 的软删除](soft-delete-blob-overview.md)
- [Blob 版本控制](versioning-overview.md)
