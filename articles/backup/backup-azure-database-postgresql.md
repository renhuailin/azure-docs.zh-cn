---
title: 备份 Azure Database for PostgreSQL
description: 了解具有长期保留功能的 Azure Database for PostgreSQL 备份（预览版）
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 25304aee2a759b55b8b3139aa2ae57511c967595
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389867"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>具有长期保留功能的 Azure Database for PostgreSQL 备份（预览版）

本文介绍如何备份 Azure Database for PostgreSQL 服务器。

## <a name="configure-backup-on-azure-postgresql-databases"></a>在 Azure PostgreSQL 数据库上配置备份

可以跨多个 Azure PostgreSQL 服务器在多个数据库上配置备份。 若要使用 Azure 备份在 Azure PostgreSQL 数据库上配置备份，请执行以下步骤：

1. 转到“备份保管库” -> “+备份” 。

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="显示用于添加备份的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="显示用于添加备份信息的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   也可以从[备份中心](/azure/backup/backup-center-overview)导航到此页。 

1. 选择/[创建一个备份策略](#create-backup-policy)，用于定义备份计划和保留期。

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="显示用于添加备份策略的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. 选择要备份的 Azure Postgres 数据库：跨订阅选择 Azure PostgreSQL 服务器之一（如果这些服务器位于保管库所在的区域）。 展开箭头以查看服务器中数据库的列表。

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="显示用于选择 Azure PostgreSQL 数据库的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="显示如何选择 Azure PostgreSQL 服务器的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. 分配 Azure 密钥保管库，用于存储在连接到所选数据库时所用的凭据。 若要在单个行的级别分配密钥保管库，请单击“选择密钥保管库和机密”。 还可以通过选择多个行并在网格顶部的菜单中单击“分配密钥保管库”来分配密钥保管库。 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="显示如何分配 Azure 密钥保管库的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. 若要指定机密信息，请使用以下选项之一： 

   1. 输入机密 URI：如果与你共享了/你知道机密 URI，请使用此选项。 可以从“密钥保管库” -> “机密(选择机密)” -> “机密标识符”复制机密 URI  。

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="显示如何输入机密 URI 的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      但是，如果使用此选项，Azure 备份将看不到有关你引用的密钥保管库的信息。 因此，无法以内联方式授予对密钥保管库的访问权限。 备份管理员以及 Postgres 和/或密钥保管库管理员需确保在“配置备份”流之外[手动授予对备份保管库的访问权限](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server)，这样才能使备份操作成功。

   1. 选择密钥保管库：如果你知道密钥保管库和机密名称，请使用此选项。 如果使用此选项，你（对密钥保管库拥有写入访问权限的备份管理员）能够以内联方式授予对密钥保管库的访问权限。 密钥保管库和机密可能已预先存在，也可能正在创建。 确保机密是采用 ADO.net 格式的 PG 服务器连接字符串，可以使用在服务器上拥有“备份”特权的数据库用户的凭据对其进行更新。 详细了解如何[在密钥保管库中创建机密](#create-secrets-in-the-key-vault)。

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="显示如何分配机密存储的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="显示从 Azure 密钥保管库选择机密的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. 机密信息更新完成后，将在更新密钥保管库信息之后开始验证。 在此处，备份服务将验证它是否拥有从密钥保管库读取机密详细信息并连接到数据库所需的所有[访问权限](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model)。 如果发现缺少一个或多个访问权限，它将显示一条错误消息 –“角色分配未完成，或用户无法分配角色”。

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text="显示机密验证的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   1. 用户无法分配角色：当你（备份管理员）对 PostgreSQL 服务器和/或密钥保管库不拥有写入访问权限，因此无法分配“查看详细信息”下列出的缺失权限时，将显示此消息 。 使用操作按钮下载分配模板，并让 PostgreSQL 和/或密钥保管库管理员运行该模板。这是一个 ARM 模板，可帮助你分配对所需资源的必要权限。 成功运行该模板后，在“配置备份”页上单击“重新验证”。

      :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="显示用于下载角色分配模板的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   1. 角色分配未完成：当你（备份管理员）对 PostgreSQL 服务器和/或密钥保管库拥有写入访问权限，因此可以分配“查看详细信息”下列出的缺失权限时，将显示此消息 。 使用顶部操作菜单中的“分配缺少的角色”操作按钮以内联方式授予对 PostgreSQL 服务器和/或密钥保管库的权限。

      :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="显示有关“角色分配未完成”的错误的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. 在顶部菜单中选择“分配缺少的角色”并分配角色。 该过程开始后，所缺少的针对 KV 和/或 PG 服务器的[访问权限](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server)将授予备份保管库。 可以定义访问权限的授予范围。 该操作完成后，将开始重新验证。

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="显示用于分配缺失角色的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="显示定义访问权限范围的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - 备份保管库从密钥保管库访问机密，并运行与数据库的测试连接，以验证是否正确输入了凭据。 还会检查数据库用户的特权，以确定[数据库用户是否对数据库拥有备份相关的权限](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database)。

   - 默认情况下，PostgreSQL 管理员对数据库拥有所有备份和还原权限。 因此验证将会成功。
   - 低特权用户可能对数据库不拥有备份/还原权限。 因此验证将会失败。 系统将动态生成 PowerShell 脚本（为每个记录/选定数据库各生成一个）。 [运行 PowerShell 脚本以向数据库用户授予对数据库的这些特权](#create-secrets-in-the-key-vault)。 或者，可以使用 PG 管理员身份或 PSQL 工具分配这些特权。

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="显示备份保管库从密钥保管库访问机密的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="显示启动测试连接的过程的屏幕截图。":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="显示如何提供用户凭据以运行测试的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. 保留“备份就绪状态”为“成功”的记录，以继续完成最后一步（提交操作）。

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="显示备份就绪状态为成功的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="显示备份配置检查页的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. 提交“配置备份”操作，并在“备份实例”下跟踪进度。

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="显示备份配置提交操作和跟踪进度的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::      

## <a name="create-backup-policy"></a>创建备份策略

可以在执行“配置备份”流期间随时创建备份策略。 或者，可以转到“备份中心” -> “备份策略” -> “添加”  。

1. 输入新策略的“名称”。

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="显示输入新策略名称的过程的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. 定义备份计划。 目前只能使用“每周”备份选项。 但是，可以计划在一周中的多天进行备份。

1. 定义保留设置。 可以添加一个或多个保留规则。 每个保留规则均假定特定备份的输入，以及这些备份的数据存储和保留期限。

1. 若要将备份存储到两个数据存储（或层）中的一个，请选择“备份数据存储”（标准层）或“存档数据存储”（预览版） 。

1. 选择“过期时”以在备份数据存储中的备份过期时将其移动到存档数据存储。

   没有任何其他保留规则时，将应用默认保留规则，其默认值为 3 个月。

   - 备份数据存储中的保留期限从 7 天到 10 年不等。
   - 存档数据存储中的保留期限从 6 个月到 10 年不等。

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="显示步骤 5“选择‘过期时’以在备份数据存储中的备份过期时将其移动到存档数据存储”的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>保留规则按预先确定的优先级顺序进行评估。 每年规则的优先级最高，其次分别是每月规则和每周规则。 没有其他规则符合条件时，将应用默认保留设置。 例如，同一恢复点可能会是每周进行的首次成功备份以及每月进行的首次成功备份。 但是，由于每月规则的优先级高于每周规则的优先级，因此适用与每月进行的首次成功备份相对应的保留期。
## <a name="create-secrets-in-the-key-vault"></a>在密钥保管库中创建机密

机密是采用 ADO.net 格式的 PG 服务器连接字符串，可以使用在服务器上拥有备份特权的数据库用户的凭据对其进行更新。 从 PG 服务器复制连接字符串并在文本编辑器中进行编辑，以更新用户 ID 和密码。 

:::image type="content" source="./media/backup-azure-database-postgresql/pg-server-connection-string-inline.png" alt-text="显示用作机密的 PG 服务器连接字符串的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/pg-server-connection-string-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/create-secret-inline.png" alt-text="显示用于创建机密 PG 服务器连接字符串的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/create-secret-expanded.png":::

## <a name="run-powershell-script-to-grant-privileges-to-database-users"></a>运行 PowerShell 脚本以向数据库用户授予特权

在配置备份期间动态生成的 PowerShell 脚本接受数据库用户（作为输入）和 PG 管理员凭据，以便向数据库用户授予对数据库的备份相关特权。

计算机上必须存在 [PSQL 工具](https://www.enterprisedb.com/download-postgresql-binaries)，并且 PATH 环境变量应已适当地设置为 PSQL 工具路径。

:::image type="content" source="./media/backup-azure-database-postgresql/psql-set-environment-inline.png" alt-text="显示用于搜索应用程序环境设置的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/psql-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/system-properties-to-set-environment-inline.png" alt-text="显示用于在“系统属性”下设置环境的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/system-properties-to-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/adding-environment-variables-inline.png" alt-text="显示默认环境变量的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/adding-environment-variables-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/editing-environment-variables-inline.png" alt-text="显示需要设置的环境变量的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/editing-environment-variables-expanded.png":::

确保 Azure PostgreSQL 实例中的“连接安全性设置”将计算机的 IP 地址列入允许列表，以允许网络连接。

## <a name="generate-an-on-demand-backup"></a>生成按需备份

若要触发不在策略指定的计划中的备份，请转到“备份实例” -> “立即备份”。
从关联的备份策略中定义的保留规则列表中进行选择。

:::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="显示用于导航到在关联备份策略中定义的保留规则列表的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="显示用于选择在关联备份策略中定义的保留规则的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## <a name="next-steps"></a>后续步骤

[排查使用 Azure 备份进行 PostgreSQL 数据库备份的问题](backup-azure-database-postgresql-troubleshoot.md)
