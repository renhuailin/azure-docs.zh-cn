---
title: 为 Azure Blob 配置操作备份
description: '了解如何在预览版中配置和管理 Azure Blob 的操作备份 () '
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7fe302de0e93575e7bb62f7c5cdefd7acc0720c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744486"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>在预览版中配置 Azure Blob 的操作备份 () 

Azure 备份使你可以轻松地配置操作备份，以保护存储帐户中的块 blob。 本文介绍如何使用 Azure 门户在一个或多个存储帐户上配置操作备份。 本文讨论以下内容：

- 开始之前要了解的问题
- 创建备份保管库
- 向要保护的存储帐户上的备份保管库授予权限
- 创建备份策略
- 在一个或多个存储帐户上配置操作备份
- 备份存储帐户的影响

## <a name="before-you-start"></a>准备工作

- Blob 的操作备份是本地备份解决方案，它在源存储帐户本身中维护指定持续时间的数据。 此解决方案不会在保管库中保留数据的另一个副本。
- 此解决方案可让你将数据还原到最多360天。 不过，长保留期可能会导致在还原操作期间花费较长的时间。
- 解决方案可用于仅对源存储帐户执行还原，并且可能会导致数据被覆盖。
- 如果通过调用删除容器操作从存储帐户中删除容器，则无法使用还原操作来还原该容器。 如果要在以后还原它们，请删除单个 blob，而不是删除整个容器。 此外，Microsoft 还建议为容器启用软删除，以防止意外删除容器。
- 若要详细了解支持的方案、限制和可用性，请参阅 [支持矩阵](blob-backup-support-matrix.md) 。

## <a name="create-a-backup-vault"></a>创建备份保管库

[备份保管库](backup-vault-overview.md)是存储一段时间内创建的恢复点的管理实体，并提供用于执行与备份相关的操作的接口。 其中包括按需备份、执行还原和创建备份策略。 尽管 blob 的操作备份是本地备份，不会将数据存储在保管库中，但管理操作需要保管库。

>[!NOTE]
>备份保管库是一个新资源，用于备份新的受支持的工作负荷，并且不同于现有的恢复服务保管库。

有关如何创建备份保管库的说明，请参阅 [备份保管库文档](backup-vault-overview.md#create-a-backup-vault)。

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>向存储帐户上的备份保管库授予权限

操作备份还可通过应用备份拥有的删除锁定，来保护包含要保护) 的 blob 的存储帐户 (。 这要求备份保管库具有对需要保护的存储帐户的某些权限。 为方便使用，这些权限已合并到 "存储帐户" 备份参与者角色下。 对于需要保护的存储帐户，请按照以下说明进行操作：

1. 在要保护的存储帐户中，导航到左侧导航窗格中的 " **访问控制 (" IAM) "选项卡** 。
1. 选择 " **添加角色分配** " 来分配所需的角色。

    ![添加角色分配](./media/blob-backup-configure-manage/add-role-assignments.png)

1. 在 "添加角色分配" 窗格中：

    1. 在 " **角色**" 下，选择 " **存储帐户" "备份参与者**"。
    1. 在 "分配对的 **访问权限**" 下，选择 **用户、组或服务主体**。
    1. 键入要保护此存储帐户中的 blob 的 **备份保管库的名称** ，并在搜索结果中选择相同的名称。
    1. 完成后，选择 " **保存**"。

        ![角色分配选项](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >允许角色分配生效10分钟。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略通常控制备份的保留和计划。 由于 blob 的操作备份在本质上是连续的，因此无需计划即可执行备份。 策略实质上是需要指定保持期。 你可以使用并重复使用备份策略为保管库配置多个存储帐户的备份。

下面是为 blob 的操作备份创建备份策略的步骤：

1. 在备份保管库中，导航到 " **备份策略** "，然后选择 " **+ 添加** "，开始创建备份策略。

    ![备份策略](./media/blob-backup-configure-manage/backup-policies.png)

1. 在 " **基本** 信息" 选项卡中，提供备份策略的名称，并选择 " **Azure blob** " 作为数据源类型。 你还可以查看所选保管库的详细信息。

    ![创建备份策略](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >尽管你将看到保管库的 **备份存储冗余** ，但冗余并不真正适用于 blob 的操作备份，因为备份在本质上是本地的，并且不会将任何数据存储在备份保管库中。 此处的备份保管库是管理实体，可帮助你管理存储帐户中的块 blob 的保护。

1. 在 " **备份策略** " 选项卡中，可以指定保留详细信息。 你将看到，已存在一个名为 " **默认** " 的保留期，保持期为30天。 如果要编辑保留期，请使用 " **编辑保留规则** " 图标来编辑和指定希望保留数据的持续时间。 最多可指定360天的保留期。

    ![备份策略选项卡](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >长时间还原可能会导致还原操作花费较长时间才能完成。 而且，还原一组数据所用的时间取决于在还原期间执行的写入和删除操作的次数。 例如，每日添加了1000000对象的帐户和每天删除3000的1000对象将需要大约两个小时才能还原到过去30天的点。 对于具有此更改率的帐户，不建议在过去保留时间和还原超过90天。

1. 在 " **查看** " 和 "创建" 窗格中，验证策略的所有详细信息，然后选择 "完成后 **创建** " 完成策略的创建。 创建备份策略并准备好使用后，将会确认通知。

    ![查看并创建策略](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>配置备份

Blob 的备份是在存储帐户级别配置的。 因此，存储帐户中的所有 blob 都受操作备份保护。

要开始配置备份：

1. 在搜索栏中搜索 " **备份中心** "。
1. 导航到 "**概述**" 和 "  ->  **备份**"。

    ![备份中心概述](./media/blob-backup-configure-manage/backup-center-overview.png)

1. 在 " **启动：配置备份** " 选项卡中，选择 "azure **Blob (azure 存储)** 作为数据源类型。

    ![启动：配置备份选项卡](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. 在 "**基本** 信息" 选项卡中，以 **数据源** 类型 **(Azure 存储) 指定 azure blob** ，并选择要将存储帐户关联到的备份保管库。 可以在窗格中查看所选保管库的详细信息。

    ![“基本信息”选项卡](./media/blob-backup-configure-manage/basics-tab.png)

1. 接下来，选择要用于指定保留期的备份策略。 你可以在屏幕的下半部分中查看所选策略的详细信息。 "操作数据存储" 列显示策略中定义的保留期。 "操作" 是指在本地保留源存储帐户中的数据。

    ![选择备份策略](./media/blob-backup-configure-manage/choose-backup-policy.png)

    你还可以创建新的备份策略。 为此，请选择 " **新建** "，然后执行以下步骤：

    1. 提供要创建的策略的名称。 确保其他框显示正确的数据源类型和保管库名称。
    1. 在 " **备份策略** " 选项卡中，选择 "编辑保留规则" 图标进行编辑，并指定希望保留数据的持续时间。 最多可指定360天的保留期。 长时间还原可能会导致还原操作花费较长时间才能完成。

        ![创建新的备份策略](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. 完成后，请选择 " **查看 + 创建** " 来创建备份策略。

1. 接下来，需要选择要为其配置 blob 保护的存储帐户。 可以一次性选择多个存储帐户，然后选择 " **选择**"。

    但是，请确保所选保管库具有配置存储帐户上的备份所需的权限，如上文中的向 [存储帐户的备份保管库授予权限](#grant-permissions-to-the-backup-vault-on-storage-accounts)。

    ![选择要备份的资源](./media/blob-backup-configure-manage/select-resources.png)

    备份检查保管库是否有足够的权限允许在所选存储帐户上配置备份。

    ![备份验证权限](./media/blob-backup-configure-manage/validate-permissions.png)

    如果验证导致错误 (与屏幕截图中的某个存储帐户) ，请参阅所选存储帐户并分配适当的角色（如 [此处](#grant-permissions-to-the-backup-vault-on-storage-accounts)所述），并选择 "重新 **验证**"。 新角色分配最多可能需要10分钟才能生效。

1. 所有选定的存储帐户的验证成功后，继续 **查看并配置** 以配置备份。 你会看到通知，通知你有关配置保护及其完成的状态。

## <a name="effects-on-backed-up-storage-accounts"></a>对备份的存储帐户的影响

在配置备份后，将跟踪存储帐户中的块 blob 上发生的更改，并根据备份策略保留数据。 你将注意到为其配置了备份的存储帐户中的以下更改：

- 在存储帐户上启用了以下功能。 可以在存储帐户的 " **数据保护** " 选项卡中查看这些数据。
  - 容器的时间点还原：包含在备份策略中指定的保留期
  - Blob 的软删除：按备份策略中指定的保留期 + 5 天
  - Blob 的版本控制
  - Blob 更改源

  如果为备份配置的存储帐户已为启用 blob 的工作  **时间点还原** (在) 配置备份之前启用了 **blob 的软删除** ，则 backup 将确保保留至少是在备份策略中定义的。 因此，对于每个属性：

  - 如果备份策略中的保留期大于存储帐户中最初存在的保留期，则会根据备份策略修改存储帐户的保留期。
  - 如果备份策略中的保留期小于存储帐户中最初存在的保留期，则存储帐户的保留期在最初设置的持续时间内保持不变。

  ![数据保护选项卡](./media/blob-backup-configure-manage/data-protection.png)

- **删除锁定** 由受保护的存储帐户上的备份应用。 锁定旨在防范意外删除存储帐户的情况。 这可以在 **存储帐户**  >  **锁定** 下查看。

    ![删除锁](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>管理操作备份

你可以使用备份中心作为单一的玻璃窗格来管理所有备份。 对于 Azure Blob 的操作备份，你可以使用备份中心来执行以下操作：

- 如上所示，可以使用它来创建备份保管库和策略。 你还可以查看所选订阅下的所有保管库和策略。
- 利用备份中心，你可以轻松监视受保护的存储帐户的状态，以及当前未配置备份的存储帐户。
- 您可以使用 **+ 备份** 按钮为任何存储帐户配置备份。
- 你可以使用 " **还原** " 按钮启动还原，并使用 **备份作业** 跟踪还原。 有关执行还原的详细信息，请参阅 [还原 Azure blob](blob-backup-support-matrix.md)。
- 使用备份报表分析备份使用情况。

    ![备份中心](./media/blob-backup-configure-manage/backup-center.png)

有关详细信息，请参阅 [备份中心概述 (预览) ](backup-center-overview.md)。

## <a name="next-steps"></a>后续步骤

- [还原 Azure Blob](blob-backup-support-matrix.md)
