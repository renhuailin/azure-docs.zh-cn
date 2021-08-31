---
title: 不可变 Blob 数据的法定保留
titleSuffix: Azure Storage
description: 法定保留以“一次写入，多次读取 (WORM)”的格式来存储 Blob 数据，直到将其显式清除为止。 如果数据必须保持为 WORM 状态的时间是未知的，请使用法定保留。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1434f689f8f629fd04ec0c0b059a1298b32d5ac9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779774"
---
# <a name="legal-holds-for-immutable-blob-data"></a>不可变 Blob 数据的法定保留

法定保留是一种临时不可变策略，可用于法律调查目的或常规保护策略。 法定保留以“一次写入，多次读取 (WORM)”的格式来存储 Blob 数据，直到将其显式清除为止。 法定保留生效时，Blob 可以创建和读取，但不能修改或删除。 如果数据必须保持为 WORM 状态的时间是未知的，请使用法定保留。

有关 Blob 存储不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)。

## <a name="legal-hold-scope"></a>法定保留范围

可在以下任一范围配置法定保留策略：

- 版本级策略（预览版）：可以对单个 Blob 版本级别配置法定保留，以便对敏感数据进行精细化管理。
- 容器级策略：在容器级配置的法定保留适用于该容器中的所有 Blob。 无法为单个 Blob 配置其自己的不可变策略。

### <a name="version-level-policy-scope-preview"></a>版本级策略范围（预览版）

若要为 Blob 版本配置法定保留，必须先对父容器启用版本级不可变性。 启用后，不能禁用版本级不可变性。 有关详细信息，请参阅[对容器启用版本级不可变性支持](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container)。

为容器启用版本级不可变性后，将无法再在容器级设置法定保留。 必须将法定保留应用于单个 Blob 版本。 可以为当前版本或以前的 Blob 版本配置法定保留。

版本级法定保留策略要求为存储帐户启用 Blob 版本控制。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。 请记住，启用版本控制可能会影响计费。 有关详细信息，请参阅 [Blob 版本控制](versioning-overview.md#pricing-and-billing)中的“定价和计费”部分。

若要详细了解如何启用版本级法定保留，请参阅[配置或清除法定保留](immutable-policy-configure-version-scope.md#configure-or-clear-a-legal-hold)。

### <a name="container-level-scope"></a>容器级范围

为容器配置法定保留时，该保留适用于容器中的所有对象。 清除法定保留后，客户端可以再次在容器中写入和删除对象，除非该容器还启用了基于时间的保留策略。

将法定保留应用于容器时，所有现有 Blob 将在 30 秒内转为不可变 WORM 状态。 上传到受策略保护的该容器的所有新 Blob 也会转为不可变状态。 所有 Blob 处于不可变状态后，不允许在不可变容器中执行覆盖或删除操作。 对于具有分层命名空间的帐户，无法重命名 Blob 或将其移到其他目录。

若要了解如何配置具有容器级作用域的法定保留，请参阅[配置或清除法定保留](immutable-policy-configure-container-scope.md#configure-or-clear-a-legal-hold)。

#### <a name="legal-hold-tags"></a>法定保留标记

容器级法定保留必须与一个或多个用作标识符字符串的用户定义的字母数字标记相关联。 例如，标记可以包含事例 ID 或事件名称。

#### <a name="audit-logging"></a>审核日志

每个具有有效法定保留的容器都提供一个策略审核日志。  该日志包含用户 ID、命令类型、时间戳和法定保留标记。 审核日志将根据 SEC 17a-4(f) 法规准则保留与策略生存期相同的时间。

[Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)提供有关所有管理服务活动的更全面日志。 [Azure 资源日志](../../azure-monitor/essentials/platform-logs-overview.md)保留有关数据操作的信息。 由用户负责根据法规要求或其他要求永久存储这些日志。

#### <a name="limits"></a>限制

以下限制适用于容器级法定保留：

- 对于存储帐户，使用法定保留设置的最大容器数为 10,000。
- 对于某个容器而言，最大法定保留标记数为 10。
- 法定保留标记的最小长度为三个字母数字字符。 最大长度为 23 个字母数字字符。
- 对于容器而言，将根据策略的持续时间最多保留 10 个法定保留策略审核日志。

## <a name="next-steps"></a>后续步骤

- [数据保护概述](data-protection-overview.md)
- [使用不可变的存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)
- [不可变 Blob 数据基于时间的保留策略](immutable-time-based-retention-policy-overview.md)
- [为 Blob 版本配置不可变性策略（预览版）](immutable-policy-configure-version-scope.md)
- [为容器配置不可变性策略](immutable-policy-configure-container-scope.md)
