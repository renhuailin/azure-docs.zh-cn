---
title: 不可变 Blob 数据的基于时间的保留策略
titleSuffix: Azure Storage
description: 基于时间的保留策略在指定的间隔内以“一次写入，多次读取”(WORM) 状态存储 Blob 数据。 可以配置范围限定为某个 Blob 版本（预览版）或容器的基于时间的保留策略。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ed76c271590b4f5fbc79c7713b70186a8d9eb426
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552059"
---
# <a name="time-based-retention-policies-for-immutable-blob-data"></a>不可变 Blob 数据的基于时间的保留策略

基于时间的保留策略在指定的间隔内以“一次写入，多次读取”(WORM) 格式存储 Blob 数据。 设置基于时间的保留策略后，客户端可以创建和读取 Blob，但无法修改或删除 Blob。 保留间隔过期后，可以删除 Blob，但无法覆盖 Blob。

有关 Blob 存储的不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)。

## <a name="retention-interval-for-a-time-based-policy"></a>基于时间的策略的保留间隔

基于时间的保留策略的保留间隔最小为 1 天，最大为 146,000 天（400 年）。

配置基于时间的保留策略时，受影响的对象将在有效保留期的持续时间内保持不可变状态。 对象的有效保留期是 Blob 创建时间和用户指定的保留间隔之差。 由于策略的保留间隔可以延长，不可变存储使用用户指定的保留间隔的最新值来计算有效保留期。

例如，假设用户创建了一个基于时间的保留策略，将保留时间间隔设置为五年。 一年前，在该容器中创建了现有的 Blob testblob1，因此，testblob1 的有效保留期为四年 。 将新 Blob testblob2 上传到容器时，testblob2 的有效保留期为其创建时间开始算起的五年 。

## <a name="locked-versus-unlocked-policies"></a>已锁定策略与已解锁策略

首次配置基于时间的保留策略时，将出于测试目的解锁该策略。 完成测试后，可以锁定该策略，使其完全符合 SEC 17a-4(f) 和其他法规标准。

已锁定策略和已解锁策略都可以防止删除和覆盖。 但是，可以通过缩短或延长保留期来修改已解锁的策略。 还可以删除已解锁的策略。

无法删除已锁定的基于时间的保留策略。 可以延长但不能缩短保留期。 在容器级别定义的已锁定策略的整个生命周期内，最多可以延长有效保留期五次。 针对 Blob 版本配置的策略的有效期延长次数没有限制。

> [!IMPORTANT]
> 根据 SEC 17a-4(f) 和其他法规符合性要求，基于时间的保留策略必须处于锁定状态才能确保 Blob 既兼容又不可变（不可写入和删除）。 Microsoft 建议将策略锁定合理的时间，通常不到 24 小时。 虽然已解锁状态提供不可变性保护，但不建议将已解锁状态用于除短期测试以外的其他目的。

## <a name="time-based-retention-policy-scope"></a>基于时间的保留策略范围

可在以下任一范围配置基于时间的保留策略：

- 版本级策略（预览版）：可以配置应用于 Blob 版本的基于时间的保留策略，以便对敏感数据进行精细管理。 可将策略应用于单个版本，或者为容器配置默认策略，该策略默认将应用于上传到该容器的所有 Blob。
- 容器级策略：在容器级配置的基于时间的保留策略将应用于该容器中的所有对象。 无法为单个对象配置其自己的不可变性策略。

审核日志是针对版本级和容器级基于时间的保留策略在容器上提供的。 对于范围限定为 Blob 版本的策略，不会提供审核日志。

### <a name="version-level-policy-scope-preview"></a>版本级策略范围（预览版）

若要配置版本级保留策略，首先必须针对父容器启用版本级不可变性。 版本级不可变性一经启用便不可禁用，不过已解锁的策略可被删除。 有关详细信息，请参阅[对容器启用版本级不可变性支持](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container)。

可以在创建容器时启用版本级不可变性支持。 现有容器也能支持版本级不可变性，但必须先经历迁移过程。 此过程可能需要一段时间，且不可逆。 有关迁移容器以支持版本级不可变性的详细信息，请参阅[迁移现有容器以支持版本级不可变性](immutable-policy-configure-version-scope.md#migrate-an-existing-container-to-support-version-level-immutability)。

版本级基于时间的保留策略要求为存储帐户启用 [Blob 版本控制](versioning-overview.md)。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。 请记住，启用版本控制可能会影响计费。 有关详细信息，请参阅 [Blob 版本控制](versioning-overview.md#pricing-and-billing)中的“定价和计费”部分。

启用版本控制后，首次上传某个 Blob 时，该 Blob 版本就是当前版本。 每次覆盖 Blob 时，都会创建一个新版本用于存储该 Blob 的先前状态。 删除 Blob 的当前版本时，该当前版本将变成先前版本，并保留到被显式删除为止。 先前 Blob 版本拥有在当前版本变成先前版本时即已生效的基于时间的保留策略。

如果默认策略已对容器生效，则当覆盖操作创建先前版本时，新的当前版本将继承容器的默认策略。

对于每个版本，只能配置一个基于时间的保留策略。 一个版本还可以配置有一个法定保留。 有关支持的基于范围的不可变性策略配置的更多详细信息，请参阅[不可变性策略范围](immutable-storage-overview.md#immutability-policy-scope)。

若要了解如何配置版本级基于时间的保留策略，请参阅[为 Blob 版本配置不可变性策略（预览版）](immutable-policy-configure-version-scope.md)。

> [!IMPORTANT]
> 版本级基于时间的保留策略目前为预览版。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 启用版本级不可变性后，最长可能需要经过 30 秒才能配置版本级基于时间的保留策略

#### <a name="configure-a-policy-on-the-current-version"></a>对当前版本配置策略

为容器启用版本级不可变性支持后，可以选择为容器配置默认的基于时间的保留策略。 为容器配置默认的基于时间的保留策略并随后上传某个 Blob 时，该 Blob 默认将继承此策略。 还可以选择在上传时替代任一 Blob 的默认策略，只需为该 Blob 配置自定义策略即可。

如果容器的默认基于时间的保留策略已解锁，则继承默认策略的当前 Blob 版本也将有一个已解锁的策略。 上传单个 Blob 后，可以缩短或延长针对当前 Blob 版本的策略的保留期，或删除当前版本。 还可以锁定当前版本的策略，即使针对容器的默认策略保持已解锁状态。

如果容器的默认基于时间的保留策略已锁定，则继承默认策略的当前 Blob 版本也将有一个已锁定的策略。 但是，如果在上传 Blob 时通过仅为该 Blob 设置策略来替代默认策略，则该 Blob 的策略将保持解锁状态，直到显式锁定该策略。 针对当前版本的策略已锁定时，你可以延长保留间隔，但无法删除该策略或缩短保留间隔。

如果未为容器配置默认策略，则可以上传具有自定义策略或没有任何策略的 Blob。

如果修改了针对容器的默认策略，则针对该容器中的对象的策略将保持不变，即使这些策略是从默认策略继承的。

下表显示了用于在上传时针对 Blob 设置基于时间的保留策略的各种选项：

| 针对容器的默认策略状态 | 上传具有默认策略的 Blob | 上传具有自定义策略的 Blob | 上传没有任何策略的 Blob |
|--|--|--|--|
| 针对容器的默认策略（已解锁） | 上传具有默认已解锁策略的 Blob | 上传具有自定义已解锁策略的 Blob | 上传没有任何策略的 Blob |
| 针对容器的默认策略（已锁定） | 上传具有默认已锁定策略的 Blob | 上传具有自定义已解锁策略的 Blob | 上传没有任何策略的 Blob |
| 没有针对容器的默认策略 | 空值 | 上传具有自定义已解锁策略的 Blob | 上传没有任何策略的 Blob |

#### <a name="configure-a-policy-on-a-previous-version"></a>针对先前的版本配置策略

启用版本控制后，对 Blob 执行的写入或删除操作会事先创建该 Blob 的新先前版本并保存该 Blob 的状态。 默认情况下，先前版本拥有基于时间的保留策略（如果存在），当前版本变成先前版本时，该策略即已针对当前版本生效。 新的当前版本继承针对容器的策略（如果存在）。

如果先前版本继承的策略已解锁，则可以缩短或延长保留间隔，或者可以删除该策略。 即使针对当前版本的策略已解锁，也可以锁定针对先前版本的策略。

如果先前版本继承的策略已锁定，则可以延长保留间隔。 无法删除该策略或缩短保留间隔。

如果未针对当前版本配置策略，则先前版本不会继承任何策略。 可为版本配置自定义策略。

如果修改针对当前版本的策略，则针对现有先前版本的策略将保持不变，即使该策略是从当前版本继承的。

### <a name="container-level-policy-scope"></a>容器级策略范围

容器级基于时间的保留策略应用于容器中的所有对象（包括新对象和现有对象）。 对于具有分层命名空间的帐户，容器级策略还应用于容器中的所有目录。

将基于时间的保留策略应用于容器时，所有现有 Blob 将在 30 秒内转为不可变 WORM 状态。 上传到受策略保护的该容器的所有新 Blob 也会转为不可变状态。 所有 Blob 处于不可变状态后，不允许在不可变容器中执行覆盖或删除操作。 对于具有分层命名空间的帐户，无法重命名 Blob 或将其移到其他目录。

以下限制适用于容器级保留策略：

- 对于存储帐户，具有锁定的基于时间的不可变策略的最大容器数为 10,000。
- 对于容器，为了延长已锁定基于时间的策略的保留间隔而执行的最大编辑次数为 5。
- 对于容器而言，将针对锁定策略最多保留七个基于时间的保留策略审核日志。

若要了解如何针对容器配置基于时间的保留策略，请参阅[为容器配置不可变性策略](immutable-policy-configure-container-scope.md)。

## <a name="allow-protected-append-blobs-writes"></a>允许受保护的追加 Blob 写入

追加 Blob 由数据块组成，并针对审核和日志记录方案所需的数据追加操作进行了优化。 按照设计，追加 Blob 只允许将新块添加到 Blob 末尾。 无论是否不可变，基本上都不允许修改或删除追加 Blob 中的现有块。 若要详细了解追加 Blob，请参阅[关于追加 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)。

只有基于时间的保留策略才能采用允许将新块写入到追加 Blob，同时保持不可变性保护与合规性的 AllowProtectedAppendWrites 属性设置。 如果已启用此设置，则可以直接在受策略保护的容器中创建追加 Blob，然后使用“追加块”操作继续将新数据块添加到追加 Blob 的末尾。 只能添加新块，而不能修改或删除任何现有块。 时间保留不可变性保护仍适用，系统会阻止删除追加 Blob，直到有效的保留期结束。 启用此设置不影响块 Blob 或页 Blob 的不可变性行为。

由于此设置是基于时间的保留策略的一部分，因此追加 Blob 在有效保留期内将保持不可变状态。 由于新数据可以在最初创建追加 Blob 之后追加，因此确定保留期的方式略有不同。 有效保留期是追加 Blob 的上次修改时间和用户指定的保留时间间隔之差。 类似地，当延长保留间隔时，不可变存储将使用用户指定的保留间隔的最新值来计算有效保留期。

例如，假设用户创建了一个基于时间的保留策略，该策略已启用 AllowProtectedAppendWrites 属性，保留间隔为 90 天。 当天在容器中创建了追加 Blob logblob1，而接下来的 10 天，新日志会持续添加到该追加 Blob，因此，logblob1 的有效保留期是从当天开始算起的 100 天（最后一次追加的时间 + 90 天） 。

已解锁的基于时间的保留策略允许随时启用和禁用 AllowProtectedAppendWrites 属性设置。 一旦锁定基于时间的保留策略，便无法更改 AllowProtectedAppendWrites 属性设置。

## <a name="audit-logging"></a>审核日志

每个启用了基于时间的保留策略的容器都会提供策略审核日志。 审核日志包含已锁定基于时间的保留策略的最多七个基于时间的保留命令。 日志条目包括用户 ID、命令类型、时间戳和保留间隔。 审核日志将根据 SEC 17a-4(f) 法规准则保留与策略生存期相同的时间。

[Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)提供有关所有管理服务活动的更全面日志。 [Azure 资源日志](../../azure-monitor/essentials/platform-logs-overview.md)保留有关数据操作的信息。 由用户负责根据法规要求或其他要求永久存储这些日志。

不会审核对版本级基于时间的保留策略所做的更改。

## <a name="next-steps"></a>后续步骤

- [数据保护概述](data-protection-overview.md)
- [使用不可变的存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)
- [不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)
- [为 Blob 版本配置不可变性策略（预览版）](immutable-policy-configure-version-scope.md)
- [为容器配置不可变性策略](immutable-policy-configure-container-scope.md)
