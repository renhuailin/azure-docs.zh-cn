---
title: 为 Blob 版本配置不可变性策略（预览版）
titleSuffix: Azure Storage
description: 了解如何配置范围限定为某个 Blob 版本的不可变性策略（预览版）。 不可变性策略通过以不可擦除、不可修改状态存储数据，为 Blob 存储提供 WORM（一次写入，多次读取）支持。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 45d44df64754234c99571b13059d02372cd26622
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779727"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>为 Blob 版本配置不可变性策略（预览版）

Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据。 在 WORM 状态下，在用户指定的间隔内无法修改或删除数据。 为 Blob 数据配置不可变性策略可以防范数据被覆盖和删除。 不可变性策略包括基于时间的保留策略和法定保留。 有关 Blob 存储的不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)。

不可变性策略的范围可以限定为单个 Blob 版本（预览版）或容器。 本文介绍如何配置版本级不可变性策略。 若要了解如何配置容器级不可变性策略，请参阅[为容器配置不可变性策略](immutable-policy-configure-container-scope.md)。

配置版本级不可变性策略的过程包括两个步骤：

1. 首先，对新容器或现有容器启用版本级不可变性支持。 有关详细信息，请参阅[对容器启用版本级不可变性支持](#enable-support-for-version-level-immutability-on-a-container)。
1. 接下来，配置应用于该容器中一个或多个 Blob 版本的基于时间的保留策略或法定保留。

> [!IMPORTANT]
> 版本级不可变性策略目前为预览版。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

若要配置版本级基于时间的保留策略，必须为存储帐户启用 Blob 版本控制。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。

有关版本级不可变性策略支持的存储帐户配置的信息，请参阅[支持的帐户配置](immutable-storage-overview.md#supported-account-configurations)。

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>对容器启用版本级不可变性支持

在将基于时间的保留策略应用于 Blob 版本之前，必须启用版本级不可变性支持。 新容器和现有容器都可配置为支持版本级不可变性。 但是，现有容器必须经历迁移过程才能为其启用此项支持。

请记住，为某个容器启用版本级不可变性支持并不会使该容器中的数据不可变。 此外，必须为容器配置默认的不可变性策略，或者对特定的 Blob 版本配置不可变性策略。

### <a name="enable-version-level-immutability-for-a-new-container"></a>为新容器启用版本级不可变性

若要使用版本级不可变性策略，首先必须显式对容器启用版本级 WORM 支持。 可以在创建容器时或者在向现有容器添加版本级不可变性策略时启用版本级 WORM 支持。

若要在 Azure 门户中创建支持版本级不可变性的容器，请执行以下步骤：

1. 在 Azure 门户中导航到你的存储帐户的“容器”页，然后选择“添加” 。
1. 在“新建容器”对话框中提供容器的名称，然后展开“高级”部分 。
1. 选择“启用版本级不可变性支持”以便为容器启用版本级不可变性。

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="该屏幕截图显示如何创建启用了版本级不可变性的容器":::

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>迁移现有容器以支持版本级不可变性

若要为现有容器配置版本级不可变性策略，必须迁移该容器以支持版本级不可变存储。 容器迁移可能需要一段时间，此操作不可逆。

对于现有的容器，无论是否为其配置了容器级基于时间的保留策略，都必须迁移该容器。 如果容器具有现有的容器级法定保留，则只有在删除法定保留之后才能迁移该容器。

若要在 Azure 门户中迁移容器以支持版本级不可变存储，请执行以下步骤：

1. 导航到所需的容器。
1. 选择右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“不可变 Blob 存储”下，选择“添加策略” 。
1. 对于“策略类型”字段，请选择“基于时间的保留”并指定保留间隔。
1. 选择“启用版本级不可变性”。
1. 选择“确定”  ，开始迁移。

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="该屏幕截图显示如何迁移现有容器以支持版本级不可变性":::

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>针对容器配置基于时间的保留策略

为容器启用版本级不可变性后，可为容器指定默认的版本级基于时间的保留策略。 除非替代单个版本的策略，否则默认策略将应用于容器中的所有 Blob 版本。

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>针对容器配置默认的基于时间的保留策略

若要在 Azure 门户中将默认的版本级不可变性策略应用于容器，请执行以下步骤：

1. 在 Azure 门户中导航到“容器”页，找到要对其应用该策略的容器。
1. 选择容器名称右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“访问策略”对话框中的“不可变 Blob 存储”部分下，选择“添加策略”  。
1. 选择“基于时间的保留策略”并指定保留间隔。
1. 如果需要，请选择“允许其他受保护的追加”以便能够写入到不可变性策略保护的追加 Blob。 有关详细信息，请参阅[允许受保护的追加 Blob 写入](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)。
1. 选择“确定”以将默认策略应用于容器。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="该屏幕截图显示如何为容器配置默认的版本级保留策略":::

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>确定针对容器的保留策略的范围

若要在 Azure 门户中确定基于时间的保留策略的范围，请执行以下步骤：

1. 导航到所需的容器。
1. 选择右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“不可变 Blob 存储”下，找到“范围”字段 。 如果为容器配置了默认的版本级保留策略，则范围将设置为“版本”，如下图所示：

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="该屏幕截图显示针对容器配置的默认版本级保留策略":::

1. 如果为容器配置了容器级保留策略，则范围将设置为“容器”，如下图所示：

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="该屏幕截图显示针对容器配置的容器级保留策略":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>针对现有版本配置基于时间的保留策略

基于时间的保留策略在指定的间隔内将 Blob 数据保持为 WORM 状态。 有关基于时间的保留策略的详细信息，请参阅[不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)。

可使用三个选项为 Blob 版本配置基于时间的保留策略：

- 选项 1：可以配置一个范围限定为容器并默认应用于该容器中所有对象的默认策略。 除非你通过针对单个 Blob 版本配置策略来显式替代默认策略，否则容器中的对象将继承默认策略。 有关更多详细信息，请参阅[针对容器配置默认的基于时间的保留策略](#configure-a-default-time-based-retention-policy-on-a-container)。
- 选项 2：可以针对 Blob 的当前版本配置策略。 此策略可以替代针对容器配置的默认策略（如果默认策略存在且已解锁）。 默认情况下，在配置策略后创建的所有先前版本将继承针对当前 Blob 版本的策略。 有关更多详细信息，请参阅[针对当前 Blob 版本配置保留策略](#configure-a-retention-policy-on-the-current-version-of-a-blob)。
- 选项 3：可以针对先前的 Blob 版本配置策略。 此策略可以替代针对当前版本配置的默认策略（如果默认策略存在且已解锁）。 有关更多详细信息，请参阅[针对先前的 Blob 版本配置保留策略](#configure-a-retention-policy-on-a-previous-version-of-a-blob)。

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>针对当前 Blob 版本配置保留策略

导航到某个容器时，Azure 门户会显示 Blob 列表。 显示的每个 Blob 表示当前 Blob 版本。 有关 Blob 版本控制的详细信息，请参阅 [Blob 版本控制](versioning-overview.md)。

若要针对当前 Blob 版本配置基于时间的保留策略，请执行以下步骤：

1. 导航到包含目标 Blob 的容器。
1. 选择 Blob 名称右侧的“更多”按钮，然后选择“访问策略” 。 如果已为先前版本配置了基于时间的保留策略，该策略会出现在“访问策略”对话框中。
1. 在“访问策略”对话框中的“不可变 Blob 版本”部分下，选择“添加策略”  。
1. 选择“基于时间的保留策略”并指定保留间隔。
1. 选择“确定”以将策略应用于当前 Blob 版本。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="该屏幕截图显示如何为当前 Blob 版本配置保留策略":::

可以查看 Blob 的属性，以确定是否对当前版本启用了某个策略。 选择该 Blob，然后导航到“概述”选项卡并找到“版本级不可变性策略”属性 。 如果启用了某个策略，“保留期”属性将显示该策略的过期日期和时间。 请记住，可为当前版本配置策略，也可以从 Blob 的父容器继承策略（如果默认策略已生效）。

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="该屏幕截图显示 Azure 门户中 Blob 版本的不可变性策略属性":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>针对先前的 Blob 版本配置保留策略

还可以针对先前的 blob 版本配置基于时间的保留策略。 先前的版本始终是不可变的，因为无法修改它。 不过，可以删除先前的版本。 基于时间的保留策略在生效的情况下可以防止被删除。

若要针对先前的 Blob 版本配置基于时间的保留策略，请执行以下步骤：

1. 导航到包含目标 Blob 的容器。
1. 选择该 Blob，然后导航到“版本”选项卡。
1. 找到目标版本，然后依次选择“更多”按钮和“访问策略” 。 如果已为先前版本配置了基于时间的保留策略，该策略会出现在“访问策略”对话框中。
1. 在“访问策略”对话框中的“不可变 Blob 版本”部分下，选择“添加策略”  。
1. 选择“基于时间的保留策略”并指定保留间隔。
1. 选择“确定”以将策略应用于当前 Blob 版本。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="该屏幕截图显示如何在 Azure 门户中为先前的 Blob 版本配置保留策略":::

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>上传 Blob 时配置基于时间的保留策略

使用 Azure 门户将 Blob 上传到支持版本级不可变性的容器时，可通过多个选项为新 Blob 配置基于时间的保留策略：

- 选项 1：如果为容器配置了默认保留策略，则可以上传具有容器策略的 Blob。 如果存在针对容器的保留策略，则默认会选择此选项。
- 选项 2：如果为容器配置了默认保留策略，则可以选择替代默认策略，方法是为新 Blob 定义自定义保留策略，或上传没有策略的 Blob。
- 选项 3：如果未为容器配置默认策略，则可以上传具有自定义策略或没有任何策略的 Blob。

若要在上传 Blob 时配置基于时间的保留策略，请执行以下步骤：

1. 导航到所需的容器，选择“上传”。
1. 在“上传 Blob”对话框中，展开“高级”部分 。
1. 在“保留策略”字段中，为新 Blob 配置基于时间的保留策略。 如果为容器配置了默认策略，则默认会选择该策略。 还可为 Blob 指定自定义策略。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="该屏幕截图显示用于在 Azure 门户中上传 Blob 时配置保留策略的选项":::

## <a name="modify-an-unlocked-retention-policy"></a>修改已解锁的保留策略

可以修改已解锁的基于时间的保留策略以缩短或延长保留间隔。 还可以删除已解锁的策略。 编辑或删除针对某个 Blob 版本的已解锁基于时间的保留策略不影响针对任何其他版本生效的策略。 如果存在针对容器生效的默认基于时间的保留策略，则使用已修改或已删除策略的 Blob 版本将不再从容器继承。

若要修改已解锁的基于时间的保留策略，请执行以下步骤：

1. 找到目标版本，这可能是 Blob 的当前版本或先前版本。 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，找到现有的已解锁策略。 选择“更多”按钮，然后从菜单中选择“编辑” 。

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="该屏幕截图显示如何在 Azure 门户中编辑现有版本级基于时间的保留策略":::

1. 提供新的策略过期日期和时间。

若要删除已解除的策略，请执行步骤 1 到 4，然后从菜单中选择“删除”。

## <a name="lock-a-time-based-retention-policy"></a>锁定基于时间的保留策略

测试完基于时间的保留策略后，可以锁定该策略。 已锁定的策略符合 SEC 17a-4(f) 和其他法规标准。 最多可将已锁定策略的保留间隔延长五倍，但不能缩短保留间隔。

锁定策略后无法删除该策略。 但是，可以在保留间隔过期后删除 Blob。

若要锁定策略，请执行以下步骤：

1. 找到目标版本，这可能是 Blob 的当前版本或先前版本。 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，找到现有的已解锁策略。 选择“更多”按钮，然后从菜单中选择“锁定策略” 。
1. 确认要锁定该策略。

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="该屏幕截图显示如何在 Azure 门户中锁定基于时间的保留策略":::

## <a name="configure-or-clear-a-legal-hold"></a>配置或清除法定保留

在显式清除法定保留之前，该法定保留会一直存储不可变数据。 若要详细了解法定保留策略，请参阅[不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)。

若要对 Blob 版本配置法定保留，请执行以下步骤：

1. 找到目标版本，这可能是 Blob 的当前版本或先前版本。 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，选择“添加策略” 。
1. 选择“法定保留”作为策略类型，然后选择“确定”以应用法定保留 。

下图显示了一个当前 Blob 版本，为其配置了基于时间的保留策略和法定保留。

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="该屏幕截图显示针对 Blob 版本配置的法定保留":::

若要清除法定保留，请导航到“访问策略”对话框，选择“更多”按钮，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

- [使用不可变的存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)
- [不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)
- [不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)
