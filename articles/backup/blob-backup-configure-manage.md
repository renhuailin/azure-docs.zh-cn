---
title: 为 Azure Blob 配置操作备份
description: 了解如何为 Azure Blob 配置和管理操作备份。
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: b437aa0df073267b07b3e74bc3a778e7aa274031
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722677"
---
# <a name="configure-operational-backup-for-azure-blobs"></a>为 Azure Blob 配置操作备份

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
- 如果通过调用“删除容器”操作从存储帐户中删除了容器，将无法使用还原操作来还原该容器。 如果要在以后还原它们，请删除单个 blob，而不是删除整个容器。 此外，Microsoft 还建议为容器启用软删除，以及执行操作备份，以防止意外删除容器。
- 请确保已为订阅注册 Microsoft.DataProtection 提供程序。
- 若要详细了解支持的方案、限制和可用性，请参阅[支持矩阵](blob-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

[备份保管库](backup-vault-overview.md)是一个管理实体，它会存储一段时间内创建的恢复点，并提供用于执行备份相关操作的接口。 其中包括按需备份、执行还原和创建备份策略。 尽管 blob 的操作备份是本地备份，不会将数据“存储”在保管库中，不过各种管理操作需要保管库。

>[!NOTE]
>备份保管库是用于备份受支持的新工作负载的新资源，与现有恢复服务保管库不同。

有关如何创建备份保管库的说明，请参阅[备份保管库文档](backup-vault-overview.md#create-a-backup-vault)。

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>向备份保管库授予对存储帐户的权限

操作备份还可通过应用备份拥有的删除锁，来保护存储帐户（包含要保护的 blob）不会遭受意外删除。 这要求备份保管库对需要保护的存储帐户拥有某些权限。 为方便使用，这些最低权限已合并到“存储帐户备份参与者”角色下。 

建议在配置备份之前，将此角色分配给备份保管库。 但是，也可以在配置备份时执行角色分配。 [详细了解](#using-backup-center)使用备份中心配置备份。 

要为需要保护的存储帐户分配所需的角色，请执行以下步骤：

>[!NOTE]
>还可以根据自己的方便，在“订阅”或“资源组”级别将角色分配给保管库。

1. 在需要保护的存储帐户中，在左侧导航窗格上导航到“访问控制 (IAM)”选项卡。
1. 选择“添加角色分配”以分配所需角色。

    ![添加角色分配](./media/blob-backup-configure-manage/add-role-assignments.png)

1. 在“添加角色分配”窗格中：

    1. 在“角色”下，选择“存储帐户备份参与者” 。
    1. 在“分配访问权限”下，选择“用户、组或服务原则” 。
    1. 搜索要用于备份此存储帐户中的 Blob 的备份保管库，然后从搜索结果中选择此保管库。
    1. 选择“保存”。

        ![角色分配选项](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >角色分配最多可能需要 10 分钟才能生效。

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

可以使用备份中心为多个存储帐户配置备份。 你还可以使用存储帐户的“数据保护”属性为存储帐户配置备份。 本部分介绍了配置备份的两种方式。

### <a name="using-backup-center"></a>使用备份中心

若要开始配置备份，请执行以下操作：

1. 在搜索栏中搜索“备份中心”。

1. 导航到“概述” -> “+备份”。

    ![备份中心概述](./media/blob-backup-configure-manage/backup-center-overview.png)

1. 在“启动: 配置备份”选项卡中，选择“Azure Blob (Azure 存储)”作为数据源类型 。

    ![“启动: 配置备份”选项卡](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. 在“基本信息”选项卡中，指定“Azure Blob (Azure 存储)”作为“数据源”类型，并选择要将存储帐户关联到的备份保管库 。<br></br>可以在同一窗格中查看所选保管库的详细信息。

    ![“基本信息”选项卡](./media/blob-backup-configure-manage/basics-tab.png)

    >[!NOTE]
    >只会为 Blob 启用操作备份，Blob 将备份存储在源存储帐户中（而不是备份保管库）。 因此，为保管库选择的备份存储冗余类型不适用于 Blob 的备份。

1. 接下来，选择要用于指定保留期的备份策略。<br></br>可以在屏幕的底部查看所选策略的详细信息。 操作数据存储列显示策略中定义的保留期。 “操作”意味着数据在源存储帐户中进行本地维护。
    
    ![选择备份策略](./media/blob-backup-configure-manage/choose-backup-policy.png)

    还可以创建新备份策略。 为此，请选择“新建”，然后执行以下步骤：
    
    1. 为要创建的策略提供名称。<br></br>确保其他框显示正确的数据源类型和保管库名称。
    
    1. 在“备份策略”选项卡上，选择保留规则的“编辑保留规则”图标，以修改数据保留的持续时间。 <br></br>可以设置最长 360 天的保留期。 
    
        >[!NOTE]
        >虽然备份不受保留期的影响，但还原较旧备份的还原操作可能需要更长时间才能完成。

       ![创建新的备份策略](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. 选择“查看 + 创建”以创建备份策略。

1. 选择用于配置 Blob 保护所需的存储帐户。 可以一次性选择多个存储帐户，然后选择“选择”。<br></br>但是，请确保已选择的保管库已经被分配了必需的 RBAC 角色，以便在存储帐户上配置备份。 详细了解[向备份保管库授予对存储帐户的权限](#grant-permissions-to-the-backup-vault-on-storage-accounts)。<br></br>如果未分配角色，你仍可以在配置备份时分配角色。 请参阅步骤 7。

    ![验证保管库的权限](./media/blob-backup-configure-manage/verify-vault-permissions.png)

    备份会验证保管库是否有足够权限来允许在所选存储帐户中配置备份。 此操作需要一段时间才能完成验证。
    
    ![允许配置备份的权限](./media/blob-backup-configure-manage/permissions-for-configuring-backup.png)

1. 验证完成后，“备份就绪情况”列将通知备份保管库是否有足够的权限来配置每个存储帐户的备份。

   ![备份保管库权限的信息](./media/blob-backup-configure-manage/information-of-backup-vault-permissions.png)

    如果验证显示错误（对于上图中列出的两个存储帐户），则尚未为这些[存储帐户](#grant-permissions-to-the-backup-vault-on-storage-accounts)分配存储帐户备份参与者角色。 此外，你还可以根据自己的当前权限在此分配所需角色。 错误消息可帮助你了解你是否具有所需的权限，并采取适当的操作：

    - 角色分配未完成：此错误（如上图中 blobbackupdemo3 项所示）表明你（用户）有权将“存储帐户备份参与者”角色和存储帐户所需的其他角色分配给保管库。 选择角色，然后单击工具栏上的“分配缺少的角色”。 这会自动将所需的角色分配到备份保管库，还会触发自动重新验证。<br><br>有时，角色传播可能需要一段时间（最多 10 分钟），这会导致重新验证失败。 在这种情况下，请等待几分钟，然后单击“重新验证”按钮重试验证。
    
    - 角色分配权限不足：此错误（如上图中 blobbackupdemo4 项所示）表明保管库没有配置备份所需的角色，你（用户）没有足够的权限分配所需的角色。 为了简化角色分配，备份允许你下载角色分配模板，你可以与有权为存储帐户分配角色的用户共享此模板。 为此，请选择此类存储帐户，然后单击“下载角色分配模板”以下载模板。<br><br>分配角色后，可以将角色与相应的用户共享。 成功分配角色后，单击“重新验证”以再次验证权限，然后配置备份。
        >[!NOTE]
        >模板将仅包含所选存储帐户的详细信息。 因此，如果有多个用户需要为不同的存储帐户分配角色，可以相应地选择和下载不同的模板。
1. 验证对所有所选存储帐户都成功后，继续“审阅和配置备份”。<br><br>你会收到通知，告知配置保护及其完成的状态。

### <a name="using-data-protection-settings-of-the-storage-account"></a>使用存储帐户的数据保护设置

可以直接从存储帐户的“数据保护”设置为存储帐户中的 Blob 配置备份。 

1. 转到要配置 Blob 备份的存储帐户，然后导航到左窗格中的“数据保护”（在“数据管理”下面）。 

1. 在可用的数据保护选项中，第一个选项允许使用 Azure 备份来启用操作备份。

    ![使用 Azure Backup 进行操作备份](./media/blob-backup-configure-manage/operational-backup-using-azure-backup.png)

1. 选中与“使用 Azure 备份启用操作备份”相对应的复选框。 然后选择要关联的备份保管库和备份策略。<br><br>可以选择现有的保管库和策略，也可以创建新的保管库和策略（如果需要）。

    >[!IMPORTANT]
    >应该已将“存储帐户备份参与者”角色分配给所选保管库。 详细了解[向备份保管库授予对存储帐户的权限](#grant-permissions-to-the-backup-vault-on-storage-accounts)。
    
    - 如果已分配所需的角色，请单击“保存”以完成备份配置。 关注门户通知以跟踪配置备份的进度。
    - 如果尚未分配，请单击“管理标识”，然后按照以下步骤分配角色。 

        ![使用 Azure 备份启用操作备份](./media/blob-backup-configure-manage/enable-operational-backup-with-azure-backup.png)


        1. 单击“管理标识”后，会转到存储帐户的“标识”边栏选项卡。 
        
        1. 单击“添加角色分配”以启动角色分配。

            ![添加角色分配以启动角色分配。](./media/blob-backup-configure-manage/add-role-assignment-to-initiate-role-assignment.png)


        1. 选择要分配给该角色的作用域、订阅、资源组或存储帐户。<br><br>如果要为多个存储帐户配置 blob 的操作备份，则建议在资源组级别分配角色。

        1. 从“角色”下拉菜单中，选择“存储帐户备份参与者”角色。 

            ![选择“存储帐户备份参与者”角色](./media/blob-backup-configure-manage/select-storage-account-backup-contributor-role.png)


        1. 单击“保存”以完成角色分配。<br><br>成功完成后，将通过门户通知你。 你还可以看到新角色已经添加到所选保管库的现有角色列表中。

            ![完成角色分配](./media/blob-backup-configure-manage/finish-role-assignment.png)

        1. 单击右上角的取消图标 (x) 返回存储帐户的“数据保护”边栏选项卡。 <br><br>返回后，继续配置备份。

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

有关详细信息，请参阅[备份中心概述](backup-center-overview.md)。

## <a name="stopping-protection"></a>停止保护

你可以根据需要为存储帐户停止操作备份。

>[!NOTE]
>停止保护只会将存储帐户与备份保管库（以及备份工具，如备份中心）取消关联，而不会禁用配置的 blob 时间点还原、版本控制和更改源。

要停止存储帐户的备份，请执行以下步骤：

1. 导航到要备份的存储帐户的备份实例。<br><br>可以从存储帐户中通过“存储帐户” -> “数据保护” -> “管理备份设置“导航到此处，也可以直接从备份中心通过“备份中心” -> “备份实例”并搜索存储帐户名称导航到此处。    

    ![存储帐户位置](./media/blob-backup-configure-manage/storage-account-location.png)

    ![通过备份中心的存储帐户位置](./media/blob-backup-configure-manage/storage-account-location-through-backup-center.png)


1. 在备份实例中，单击“删除”以停止特定存储帐户的操作备份。 
 
    ![停止操作备份](./media/blob-backup-configure-manage/stop-operational-backup.png)

停止备份后，可以从存储帐户的“数据保护”边栏选项卡中禁用其他存储数据保护功能（为配置备份而启用的功能）。


## <a name="next-steps"></a>后续步骤

[还原 Azure Blob](blob-restore.md)
