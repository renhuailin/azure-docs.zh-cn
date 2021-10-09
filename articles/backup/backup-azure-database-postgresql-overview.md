---
title: 关于 Azure Database for PostgreSQL 备份
description: Azure Database for PostgreSQL 备份（预览版）概述
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: 3740d4c7d149181638da93a3a5ad713c2c230f29
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154406"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>关于 Azure Database for PostgreSQL 备份（预览版）

Azure 备份和 Azure 数据库服务共同致力于为 Azure Database for PostgreSQL 服务器生成可将备份保留长达 10 年的企业级备份解决方案。 除了长期保留，该解决方案还提供以下功能：

- 客户在单个数据库级别控制计划备份和按需备份。
- 数据库级还原到任何 PostgreSQL 服务器或任何 Blob 存储。
- 集中监视所有操作和作业。
- 备份存储在单独的安全域和容错域中。 如果源服务器或订阅在任何情况下受到损坏，备份在[备份保管库](/azure/backup/backup-vault-overview)中（在 Azure 备份托管存储帐户中）都会保持安全。
- 使用 pg_dump 可以更灵活地进行复原。 这有助于跨数据库版本进行还原 

你可以单独使用此解决方案，也可以与 [Azure PostgreSQL 提供的本机备份解决方案](/azure/postgresql/concepts-backup)（最多提供 35 天的保留期）配合使用。 本机解决方案适用于操作恢复，例如想要从最新备份中恢复时。 Azure 备份解决方案可帮助满足合规性需求以及提供更精细、更灵活的备份/还原。

## <a name="support-matrix"></a>支持矩阵

|支持  |详细信息  |
|---------|---------|
|支持的部署   |  [Azure Database for PostgreSQL - 单一服务器](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|支持的 Azure 区域 |  美国东部、美国东部 2、美国中部、美国中南部、美国西部、美国西部 2、美国中西部、巴西南部、加拿大中部、北欧、西欧、英国南部、英国西部、德国中西部、瑞士北部、瑞士西部、东亚、东南亚、日本东部、日本西部、韩国中部、韩国南部、印度中部、澳大利亚东部、澳大利亚中部、澳大利亚中部 2、阿联酋北部  |
|支持的 Azure PostgreSQL 版本    |   9.5、9.6、10、11      |

## <a name="feature-considerations-and-limitations"></a>功能注意事项和限制

- 仅 Azure 门户支持所有操作。 
- 建议的最大数据库大小限制为 400 GB。
- 不支持跨区域备份。 因此，你无法将 Azure PostgreSQL 服务器备份到另一个区域中的保管库。 同样，只能将备份还原到与保管库位于同一区域内的服务器。 但是，我们支持跨订阅备份和还原。 
- 在还原过程中仅恢复数据；角色不会被还原。
- 在预览版中，我们建议仅在测试环境中运行解决方案。

## <a name="backup-process"></a>备份过程

1. 作为备份管理员，你可以指定要备份的 Azure PostgreSQL 数据库。 此外，还可以指定存储了连接到指定数据库所需凭据的 Azure 密钥保管库的详细信息。 这些凭据由 Azure 密钥保管库中的数据库管理员安全设定种子。
1. 然后，备份服务会验证它是否具有[适当的权限来对指定的 PostgreSQL 服务器进行身份验证](#azure-backup-authentication-with-the-postgresql-server)并备份其数据库。
1. Azure 备份启动一个辅助角色 (VM)，其中安装了备份扩展，以与受保护的 PostgreSQL 服务器进行通信。 此扩展包含协调器和 PostgreSQL 插件。 协调器触发各种操作（如备份和还原）的工作流，并且该插件管理实际数据流。
1. 在计划的时间，协调器与插件通信，并开始使用 pg_dump（自定义）从 PostgreSQL 服务器流式传输备份数据。
1. 该插件将数据直接发送到 Azure 备份托管存储帐户（由备份保管库屏蔽），无需临时位置。 数据使用 Microsoft 托管密钥加密，并由 Azure 备份服务存储在存储帐户中。

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="显示备份过程的示意图。":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>Azure 备份向 PostgreSQL 服务器进行的身份验证

Azure 备份遵循 Azure 制定的严格安全准则；要备份的资源的权限不是假定的，需要由用户明确授予。 

### <a name="key-vault-based-authentication-model"></a>基于密钥保管库的身份验证模型

每次备份时，Azure 备份服务都需要连接到 Azure PostgreSQL。 虽然与数据库对应的“用户名 + 密码”（或称为连接字符串）用于建立此连接，但这些凭据不会与 Azure 备份一起存储。 相反，这些凭据需要由数据库管理员在 [Azure Key Vault 中作为机密](/azure/key-vault/secrets/about-secrets)安全地设定种子。 工作负荷管理员负责管理和轮换凭据；Azure 备份从密钥保管库中调用最新的机密详细信息以进行备份。
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="显示工作负荷或数据库流的示意图。":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Azure PostgreSQL 数据库备份所需的一组权限

1. 向备份保管库的 MSI 授予以下访问权限：

   - 对 Azure PostgreSQL 服务器的 _读者_ 访问权限。
   - Azure Key Vault 的 _密钥保管库机密用户_（或者获取和列出机密）。

1. 对以下各项的网络查看访问权限：

   - Azure PostgreSQL 服务器 – 将“允许访问 Azure 服务”标志设置为“是”。 
   - 密钥保管库 – 将“允许信任的 Microsoft 服务”标志设置为“是”。 

1. 数据库用户对数据库的备份权限

>[!Note]
>如果你（备份管理员）对目标资源具有“写入”访问权限，则可以在[配置备份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)流通过一键式操作授予这些权限，如果没有必需的权限（涉及到多个角色的情况下），则可以使用 ARM 模板。 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Azure PostgreSQL 数据库复原所需的一组权限

还原的权限类似于备份所需的权限，你需要授予对目标 PostgreSQL 服务器及其对应的密钥保管库的权限。 与配置备份流不同，当前无法提供以内联方式授予这些权限的体验。 因此，你需要[手动授予对 Postgres 服务器和相应密钥库的访问权限](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)。

此外，请确保数据库用户（与密钥保管库中存储的凭据）对数据库具有以下还原权限：

- ALTER USER username CREATEDB;
- 为数据库用户分配角色 _azure_pg_admin_。

### <a name="azure-active-directory-based-authentication-model"></a>基于 Azure Active Directory 的身份验证模型

我们早些时候推出了一种完全基于 Azure Active Directory (Azure AD) 的不同身份验证模型。 但是，我们现在提供新的基于密钥保管库的身份验证模型（如上所述）作为替代选项，这简化了配置过程。 

[下载此文档](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)获取使用此身份验证模型的自动化脚本和相关说明。 它将向 Azure PostgreSQL 服务器授予一组适当的权限，以便进行备份和还原。

>[!Note]
>所有新的配置保护将仅使用新的 Key Vault 身份验证模型进行。 但是，使用基于 Azure AD 的身份验证配置保护的所有现有备份实例将继续存在并进行定期备份。 若要还原这些备份，需要遵循基于 Azure AD 的身份验证。

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>手动授予对 Azure PostgreSQL 服务器和密钥保管库的访问权限

若要授予 Azure 备份所需的所有访问权限，请参阅以下各节：

### <a name="access-permissions-on-the-azure-postgresql-server"></a>对 Azure PostgreSQL 服务器的访问权限

1. 设置备份保管库的 MSI 对 Azure PostgreSQL 服务器的 **读者** 访问权限。

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="显示用于设置备份保管库的 MSI 对 Azure PostgreSQL 服务器的读者访问权限的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. 对 Azure PostgreSQL 服务器的网络视线访问权限：将“允许访问 Azure 服务”标志设置为“是”。

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="显示用于设置对 Azure PostgreSQL 服务器的网络视觉访问权限的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>对 Azure Key Vault 的访问权限（与 PostgreSQL 服务器关联）

1. 设置备份保管库的 MSI 对 Azure Key Vault 的密钥保管库机密用户（或获取或列出机密)访问权限。   若要分配权限，你可以使用角色分配或访问策略。 无需同时使用这两个选项来添加权限，因为这样做并没有益处。

   - 使用 Azure 基于角色的访问控制 (Azure RBAC) 授权（即权限模型设置为“Azure 基于角色的访问控制”）：

     - 在“访问控制”下面，授予备份保管库的 MSI 密钥保管库机密用户对密钥保管库的访问权限。 该角色的持有者将能够读取机密。
     - [使用 Azure RBAC 授予应用程序访问 Azure Key Vault 的权限。](/azure/key-vault/general/rbac-guide?tabs=azure-cli)

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="显示为机密用户提供访问权限的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="显示了授予备份保管库的 MSI 密钥保管库机密用户对密钥保管库的访问权限的选项。" lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - 使用访问策略（即，权限模型设置为保管库访问策略）：

     - 设置获取和列出机密的权限。
     - 了解如何[分配 Azure Key Vault 访问策略](/azure/key-vault/general/assign-access-policy?tabs=azure-portal)

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="此屏幕截图显示了使用权限模型授予权限的选项设置为“保管库访问策略模型”。" lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. 对密钥保管库的网络视线访问：将“允许信任的 Microsoft 服务”标志设置为“是”。 

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="此屏幕截图显示针对密钥保管库的网络视线访问权限，将“允许信任的 Microsoft 服务”标志设置为“是”" lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>数据库用户对数据库的备份权限

在 [PG 管理](#using-the-pg-admin-tool)工具中运行以下工具（将 _username_ 替换为数据库用户 ID）：

```
DO $do$
DECLARE
sch text;
BEGIN
EXECUTE format('grant connect on database %I to %I', current_database(), 'username');
FOR sch IN select nspname from pg_catalog.pg_namespace
LOOP
EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO username $$, sch);
END LOOP;
END;
```

## <a name="using-the-pg-admin-tool"></a>使用 PG 管理工具

[下载 PG 管理工具](https://www.pgadmin.org/download/)（如果还没有）。 可以通过此工具连接到 Azure PostgreSQL 服务器。 此外，还可以将数据库和新用户添加到此服务器。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="显示使用 PG 管理工具连接到 Azure PostgreSQL 服务器的过程的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

使用你选择的名称创建新服务器。 输入与 Azure 门户中 Azure PostgreSQL 资源视图中显示的服务器名称相同的主机名/地址名称。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="显示使用 PG 管理工具创建新服务器的选项的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="显示了输入与服务器名称相同的主机名或地址名的选项。" lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

确保将 _当前客户端 ID 地址_ 添加到防火墙规则，使连接通过。

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="显示将当前客户端 ID 地址添加到防火墙规则的过程的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

可以向服务器添加新数据库和数据库用户。 对于数据库用户，添加一个新的登录名/组角色。 确保“可以登录?”设置为“是”。 

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="显示向服务器添加新数据库和数据库用户的过程的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="显示为数据库用户添加新登录名或组角色的过程的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="显示验证“可以登录?”选项设置为“是”的屏幕截图。" lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>后续步骤

[Azure Database for PostgreSQL 备份](backup-azure-database-postgresql.md)
