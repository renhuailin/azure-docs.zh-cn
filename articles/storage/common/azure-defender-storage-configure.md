---
title: 配置适用于存储的 Azure Defender
titleSuffix: Azure Storage
description: 配置用于存储的 Azure Defender，以便检测帐户活动中的异常情况，在他人对你的帐户进行可能有害的访问尝试时获得通知。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063179"
---
# <a name="configure-azure-defender-for-storage"></a>配置适用于存储的 Azure Defender

用于存储的 Azure Defender 提供额外的安全智能层，可检测是否存在访问或攻击存储帐户的异常和潜在有害尝试行为。 借助此保护层，用户无需成为安全专家，也无需管理安全监视系统便可解决威胁。

当活动出现异常时，会触发安全警报。 这些安全警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，并通过电子邮件发送给订阅管理员，内容包括可疑活动的详细信息以及如何调查和修正威胁的建议。

该服务会引入对 Blob 存储和 Azure 文件存储的读取、写入和删除请求的资源日志，用于威胁检测。 若要调查来自 Azure Defender 的警报，可以使用存储分析日志记录来查看相关的存储活动。 有关详细信息，请参阅[在 Azure 门户中监视存储帐户](./manage-storage-analytics-logs.md#configure-logging)中的“配置日志记录”。

## <a name="availability"></a>可用性

用于存储的 Azure Defender 目前可用于 Blob 存储、Azure 文件存储和 Azure Data Lake Storage Gen2。 支持 Azure Defender 的帐户类型包括常规用途 v2、块 Blob 和 Blob 存储帐户。 用于存储的 Azure Defender 可用于所有公有云和美国政府云，但不可用于其他主权或 Azure 政府云区域。

为 Data Lake Storage 启用分层命名空间的帐户支持使用 Azure Blob 存储 API 和 Data Lake Storage API 的事务。 Azure 文件共享通过 SMB 支持事务。

如需定价详细信息（包括 30 天免费试用版的信息），请参阅 [Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

以下列表汇总了用于存储的 Azure Defender 的可用性：

- 发布状态：
  - [Blob 存储](https://azure.microsoft.com/services/storage/blobs/)（正式发布）
  - [Azure 文件存储](../files/storage-files-introduction.md)（正式发布）
  - Azure Data Lake Storage Gen2（正式发布）
- 云：<br>
    ✔ 商业云<br>
    ✔ US Gov<br>
    ✘ China Gov、其他 Gov

## <a name="set-up-azure-defender"></a>设置 Azure Defender

可以通过多种方式配置用于存储的 Azure Defender，如以下部分所述。

### <a name="azure-security-center"></a>[Azure 安全中心](#tab/azure-security-center)

Azure Defender 内置于 Azure 安全中心。 在你的订阅上启用 Azure Defender 时，就会对所有存储帐户自动启用 Azure Defender for Azure Storage。 可以在某个特定订阅下为存储帐户启用或禁用 Azure Defender，如下所示：

1. 在 [Azure 门户](https://portal.azure.com)中启动 Azure 安全中心。
1. 从主菜单的“管理”下，选择“定价和设置” 。
1. 选择要为其启用或禁用 Azure Defender 的订阅。
1. 选择“打开 Azure Defender”，为该订阅启用 Azure Defender。
1. 在“按资源类型选择 Azure Defender 计划”下，找到“存储”行，然后在“计划”列中选择“启用”   。
1. 保存所做更改。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="屏幕截图，显示如何在安全中心启用用于存储的 Azure Defender":::

现在已为此订阅中的所有存储帐户启用了 Azure Defender。

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 启动 [Azure 门户](https://portal.azure.com/)。
1. 导航到自己的存储帐户。 在“设置”下，选择“高级安全”。
1. 选择“启用用于存储的 Azure Defender”。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="显示如何为 Azure 存储帐户启用 Azure Defender 的屏幕截图":::

现在已为此存储帐户启用了 Azure Defender。

### <a name="template"></a>[模板](#tab/template)

使用 Azure 资源管理器模板来部署启用了 Azure Defender 的 Azure 存储帐户。 有关详细信息，请参阅[具有高级威胁防护的存储帐户](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

使用 Azure Policy 在某个特定订阅或资源组下的存储帐户中启用 Azure Defender。

1. 启动“Azure Policy - 定义”页。
1. 搜索“在存储帐户上部署 Azure Defender”策略。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="应用策略以便为存储帐户启用 Azure Defender":::

1. 选择 Azure 订阅或资源组。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="选择与策略范围对应的订阅或资源组":::

1. 分配策略。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="分配策略以启用用于存储的 Azure Defender":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 对存储帐户启用 Azure Defender，请先确保已安装 [Az.Security](https://www.powershellgallery.com/packages/Az.Security) 模块。 接下来，调用 [AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) 命令。 请注意将尖括号中的值替换为你自己的值：

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

若要使用 PowerShell 检查存储帐户的 Azure Defender 设置，请调用 [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) 命令。 请注意将尖括号中的值替换为你自己的值：

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 对存储帐户启用 Azure Defender，请调用 [az security atp storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update) 命令。 请注意将尖括号中的值替换为你自己的值：

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

若要使用 Azure CLI 检查存储帐户的 Azure Defender 设置，请调用 [az security atp storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) 命令。 请注意将尖括号中的值替换为你自己的值：

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>了解安全异常

存储活动出现异常时，用户会收到一封电子邮件通知，其中包含有关可疑安全事件的信息。 事件详细信息包括：

- 异常性质
- 存储帐户名称
- 事件时间
- 存储类型
- 可能的原因
- 调查步骤
- 修正步骤

电子邮件还包含有关可能原因的详细信息以及用于调查和缓解潜在威胁的建议操作。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="用于存储的 Azure Defender 警报电子邮件":::

可从 Azure 安全中心的[“安全警报”磁贴](../../security-center/security-center-managing-and-responding-alerts.md)查看和管理当前安全警报。 单击特定警报可提供详细信息以及用于调查当前威胁和解决潜在威胁的操作。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="用于存储的 Azure Defender 警报":::

## <a name="security-alerts"></a>安全警报

警报是因访问或攻击存储帐户的异常且可能有害的尝试而生成的。 有关 Azure 存储的警报列表，请参阅 [Azure 存储的警报](../../security-center/alerts-reference.md#alerts-azurestorage)。

## <a name="next-steps"></a>后续步骤

- [用于存储的 Azure Defender 简介](../../security-center/defender-for-storage-introduction.md)
- [Azure 安全中心](../../security-center/security-center-introduction.md)
- [登录 Azure 存储帐户](/rest/api/storageservices/About-Storage-Analytics-Logging)
