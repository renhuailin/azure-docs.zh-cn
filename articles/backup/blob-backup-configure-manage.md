---
title: 为 Azure Blob 配置操作备份
description: 了解如何为 Azure Blob 配置和管理操作备份（预览版）
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051066"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>为 Azure Blob 配置操作备份（预览版）

Azure 备份使你可以轻松地配置操作备份，以保护存储帐户中的块 blob。 本文介绍如何使用 Azure 门户对一个或多个存储帐户配置操作备份。 本文讨论以下内容：

- 开始之前要了解的事项
- 创建备份保管库
- 向备份保管库授予对要保护的存储帐户的权限
- 创建备份策略
- 对一个或多个存储帐户配置操作备份
- 对备份存储帐户的影响

## <a name="before-you-start"></a>开始之前

- Blob 的操作备份是一种本地备份解决方案，可在源存储帐户本身中于指定持续时间内维护数据。 此解决方案不会在保管库中维护数据的另一个副本。
- 此解决方案使你可以将数据保留最多 360 天以用于还原。 不过，较长的保留持续时间可能会导致在还原操作期间花费较长的时间。
- 该解决方案只能用于对源存储帐户执行还原，可能会导致覆盖数据。
- 如果通过调用“删除容器”操作从存储帐户中删除容器，将无法使用还原操作来还原该容器。 如果要在以后还原它们，请删除单个 blob，而不是删除整个容器。 此外，Microsoft 还建议为容器启用软删除，以及执行操作备份，以防止意外删除容器。
- 若要详细了解支持的方案、限制和可用性，请参阅[支持矩阵](blob-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

[备份保管库](backup-vault-overview.md)是一个管理实体，它会存储一段时间内创建的恢复点，并提供用于执行备份相关操作的接口。 其中包括按需备份、执行还原和创建备份策略。 尽管 blob 的操作备份是本地备份，不会将数据“存储”在保管库中，不过各种管理操作需要保管库。

>[!NOTE]
>备份保管库是用于备份受支持的新工作负载的新资源，与现有恢复服务保管库不同。

有关如何创建备份保管库的说明，请参阅[备份保管库文档](backup-vault-overview.md#create-a-backup-vault)。

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>向备份保管库授予对存储帐户的权限

操作备份还可通过应用备份拥有的删除锁，来保护存储帐户（包含要保护的 blob）不会遭受意外删除。 这要求备份保管库对需要保护的存储帐户拥有某些权限。 为方便使用，这些权限已合并到存储帐户备份参与者角色下。 对于需要保护的存储帐户，请按照以下说明进行操作：

1. 在要保护的存储帐户中，在左侧导航窗格上导航到“访问控制(IAM)”选项卡。
1. 选择“添加角色分配”以分配所需角色。

    ![添加角色分配](./media/blob-backup-configure-manage/add-role-assignments.png)

1. 在“添加角色分配”窗格中：

    1. 在“角色”下，选择“存储帐户备份参与者” 。
    1. 在“分配访问权限”下，选择“用户、组或服务原则” 。
    1. 键入要用于保护此存储帐户中 blob 的备份保管库的名称，并在搜索结果中选择相同名称。
    1. 完成后，选择“保存”。

        ![角色分配选项](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >请等待 10 分钟，以便角色分配生效。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略通常控制备份的保留和计划。 由于 blob 的操作备份在本质上是连续的，因此无需计划即可执行备份。 实质上是需要策略来指定保留期。 可以使用和重复使用备份策略来配置多个存储帐户到保管库的备份。

下面是为 blob 的操作备份创建备份策略的步骤：

1. 在备份保管库中，导航到“备份策略”，然后选择“+添加”以开始创建备份策略 。

    ![备份策略](./media/blob-backup-configure-manage/backup-policies.png)

1. 在“基本信息”选项卡中，提供备份策略的名称，并选择“Azure Blob”作为数据源类型 。 还可以查看所选保管库的详细信息。

    ![创建备份策略](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >虽然会看到保管库的备份存储冗余，但冗余并不实际应用于 blob 的操作备份，因为该备份在本质上是本地的，不会有任何数据存储在备份保管库中。 此处的备份保管库是管理实体，可帮助你管理存储帐户中块 blob 的保护。

1. “备份策略”选项卡用于指定保留详细信息。 你会看到已有一个名为“默认”的保留规则，其保留期为 30 天。 如果要编辑保留持续时间，请使用“编辑保留规则”图标编辑和指定希望保留数据的持续时间。 可以指定最多 360 天的保留期。

    ![“备份策略”选项卡](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >对较长持续时间进行还原可能会导致还原操作需要较长时间才能完成。 而且，还原一组数据所用的时间取决于在还原期间执行的写入和删除操作的次数。 例如，如果一个帐户有 1 百万个对象，每天增加 3,000 个对象，每天删除 1,000 个对象，将需要大约两个小时才能还原到过去 30 天的点。 对于具有此变化率的帐户，不建议在过去保留时间和还原超过90天。

1. 在“查看 + 创建”窗格中，验证策略的所有详细信息，并在完成后选择“创建”以完成策略创建 。 备份策略已创建并准备好进行使用后，会有一个通知进行确认。

    ![查看和创建策略](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>配置备份

Blob 的备份在存储帐户级别进行配置。 因此，存储帐户中的所有 blob 都受操作备份保护。

若要开始配置备份，请执行以下操作：

1. 在搜索栏中搜索“备份中心”。
1. 导航到“概述” -> “+备份”。

    ![备份中心概述](./media/blob-backup-configure-manage/backup-center-overview.png)

1. 在“启动: 配置备份”选项卡中，选择“Azure Blob (Azure 存储)”作为数据源类型 。

    ![“启动: 配置备份”选项卡](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. 在“基本信息”选项卡中，指定“Azure Blob (Azure 存储)”作为“数据源”类型，并选择要将存储帐户关联到的备份保管库  。 可以在窗格中查看所选保管库的详细信息。

    ![“基本信息”选项卡](./media/blob-backup-configure-manage/basics-tab.png)

1. 接下来，选择要用于指定保留期的备份策略。 可以在屏幕的下半部分中查看所选策略的详细信息。 操作数据存储列显示策略中定义的保留期。 “操作”表示数据在源存储帐户本身中进行本地维护。

    ![选择备份策略](./media/blob-backup-configure-manage/choose-backup-policy.png)

    还可以创建新备份策略。 为此，请选择“新建”，然后执行以下步骤：

    1. 为要创建的策略提供名称。 确保其他框显示正确的数据源类型和保管库名称。
    1. 在“备份策略”选项卡中，选择编辑保留规则图标进行编辑，并指定希望保留数据的持续时间。 可以指定最多 360 天的保留期。 对较长持续时间进行还原可能会导致还原操作需要较长时间才能完成。

        ![创建新备份策略](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. 完成后，选择“查看 + 创建”以创建备份策略。

1. 接下来，需要选择要为其配置 blob 保护的存储帐户。 可以一次性选择多个存储帐户，然后选择“选择”。

    但是，请确保所选保管库具有对存储帐户配置备份所需的权限（如上文的[向备份保管库授予对存储帐户的权限](#grant-permissions-to-the-backup-vault-on-storage-accounts)中所述）。

    ![选择要备份的资源](./media/blob-backup-configure-manage/select-resources.png)

    备份会检查保管库是否有足够权限来允许对所选存储帐户配置备份。

    ![备份验证权限](./media/blob-backup-configure-manage/validate-permissions.png)

    如果验证导致错误（与屏幕截图中的某个存储帐户一样），请转到所选存储帐户并分配适当的角色（如[此处](#grant-permissions-to-the-backup-vault-on-storage-accounts)所述），并选择“重新验证”。 新角色分配最多可能需要 10 分钟才能生效。

1. 验证对所有所选存储帐户都成功后，继续“审阅和配置”以配置备份。 你会看到通知，向你告知配置保护及其完成的状态。

## <a name="effects-on-backed-up-storage-accounts"></a>对备份的存储帐户的影响

在配置备份后，系统会跟踪存储帐户中块 blob 上进行的更改，并根据备份策略保留数据。 你会在为其配置了备份的存储帐户中注意到以下更改：

- 在存储帐户上启用了以下功能。 可以在存储帐户的“数据保护”选项卡中查看这些功能。
  - 容器的时间点还原：具有在备份策略中指定的保留期
  - Blob 的软删除：具有在备份策略中指定的保留期 + 5 天
  - blob 版本控制
  - Blob 更改源

  如果配置了备份的存储帐户已启用了“容器的时间点还原”或“blob 的软删除”（在配置备份之前），则备份可确保保留期至少如备份策略中所定义 。 因此，对于每个属性：

  - 如果备份策略中的保留期大于存储帐户中最初存在的保留期：根据备份策略修改存储帐户的保留期
  - 如果备份策略中的保留期小于存储帐户中最初存在的保留期：存储帐户的保留期保持不变，仍为最初设置的持续时间。

  ![数据保护选项卡](./media/blob-backup-configure-manage/data-protection.png)

- 删除锁由备份应用于受保护的存储帐户。 该锁旨在防范意外删除存储帐户的情况。 这可以在“存储帐户” > “锁”下进行查看。

    ![删除锁](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>管理操作备份

可以使用备份中心作为单一虚拟管理平台来管理所有备份。 对于 Azure Blob 的操作备份，可以使用备份中心执行以下操作：

- 如上所示，可以使用它创建备份保管库和策略。 还可以查看所选订阅下的所有保管库和策略。
- 利用备份中心可轻松监视受保护存储帐户的状态，以及当前未配置备份的存储帐户。
- 可以使用“+备份”按钮为任何存储帐户配置备份。
- 可以使用“还原”按钮启动还原，并使用“备份作业”跟踪还原 。 有关执行还原的详细信息，请参阅[还原 Azure Blob](blob-backup-support-matrix.md)。
- 使用备份报告分析备份使用情况。

    ![备份中心](./media/blob-backup-configure-manage/backup-center.png)

有关详细信息，请参阅[备份中心概述（预览版）](backup-center-overview.md)。

## <a name="next-steps"></a>后续步骤

- [还原 Azure Blob](blob-restore.md)
