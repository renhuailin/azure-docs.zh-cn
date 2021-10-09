---
title: 使用 Azure 门户进行迁移（从 Data Lake Storage Gen1 到 Gen2）
description: 可以使用 Azure 门户简化在 Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 之间的迁移任务。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3d78ba06112fce8dff64f2091e434d8f502634d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662735"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2-by-using-the-azure-portal-preview"></a>使用 Azure 门户（预览版）将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2

可以使用 Azure 门户减少完成迁移所需的步骤数。 数据和元数据（例如时间戳和 ACL）会自动移动到已启用 Gen2 的帐户。 如果执行完整的迁移，则无需将工作负载指向 Gen2，因为系统会自动重定向请求。

> [!IMPORTANT]
> 使用 Azure 门户从 Gen1 迁移到 Gen2 的功能目前处于预览阶段。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文将指导你完成以下任务：

- 步骤1：注册预览版

- 步骤2：创建具有 Gen2 功能的存储帐户

- 步骤3：验证 RBAC 角色分配

- 步骤4：执行迁移

- 步骤 5。 测试应用程序|

- 步骤6：完成迁移

请务必阅读有关如何从 Gen1 迁移到 Gen2 的常规指南。 有关详细信息，请参阅[将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2](data-lake-storage-migrate-gen1-to-gen2.md)。

> [!NOTE]
> 为了便于阅读，本文使用术语 Gen1 来指代 Azure Data Lake Storage Gen1，并使用术语 Gen2 来指代 Azure Data Lake Storage Gen2 。

## <a name="enroll-in-the-preview"></a>注册预览版

若要注册预览版，请参阅[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4SeyCfCfrtBlHWFupvoz_BUMEFNQzBSQTE0OU1aM0hXMDlBNEwzVTYyRy4u&wdLOR=cBC075B83-9324-4399-B94E-05A919D007C9)。 注册后，请创建具有 Gen2 功能的存储帐户（请参阅下一部分）。

Microsoft 将在大约 7 天内与你联系，以确保新帐户已启用，可以进行迁移。

## <a name="create-a-storage-account-with-gen2-capabilities"></a>创建具有 Gen2 功能的存储帐户

Azure Data Lake Storage Gen2 不是专用的存储帐户或服务类型。 它是一组可以通过启用 Azure 存储帐户的“分层命名空间”功能获得的功能。 若要创建具有 Gen2 功能的帐户，请参阅[创建用于 Azure Data Lake Storage Gen2 的存储帐户](create-data-lake-storage-account.md)。

创建帐户时，请确保使用以下值配置设置。

| 设置 | 值 |
|--|--|
| **存储帐户名称** | 所需的任何名称。 此名称不必与 Gen1 帐户的名称匹配，并且可以位于所选的任何订阅中。 |
| **位置** | Data Lake Storage Gen1 帐户使用的同一区域 |
| **复制** | LRS 或 ZRS |
| **最低 TLS 版本** | 1.0 |
| NFS v3 | 已禁用 |
| **分层命名空间** | 已启用 |

> [!NOTE]
> Azure 门户中的迁移工具不会移动帐户设置。 因此，创建帐户后，必须手动配置设置，例如加密、网络防火墙和数据保护。

## <a name="verify-rbac-role-assignments"></a>验证 RBAC 角色分配

对于 Gen2，请确保已向 Azure Active Directory (Azure AD) 用户标识分配[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色（在存储帐户、父资源组或订阅的范围内）。

对于 Gen1，请确保已向 Azure AD 标识分配[所有者](../../role-based-access-control/built-in-roles.md#owner)角色（在 Gen1 帐户、父资源组或订阅的范围内）。

## <a name="perform-the-migration"></a>执行迁移

开始之前，请确定是仅复制数据还是要执行完整的迁移。

如果只复制数据，则在迁移完成后，两个帐户都将保持活动状态。 在迁移过程中，Gen1 帐户将变为只读帐户。 然后，你可以更新计算工作负载以使用新的启用了 Gen2 的存储帐户。 验证应用程序和工作负载按预期工作后，可以停用 Gen1 帐户。 Microsoft 建议使用此选项。

如果执行完整迁移，数据会从 Gen1 复制到 Gen2。 然后，Gen1 URI 会重定向到 Gen2 URI。 迁移完成后，你将无法访问 Gen1 帐户，所有 Gen1 请求都将重定向到已启用 Gen2 的帐户。 Gen1 帐户将不能再用于数据操作，并且你将不再为 Gen1 帐户中的数据付费。 管道在启用了 Gen2 的帐户上运行后，便可以删除 Gen1 帐户。

> [!NOTE]
> Gen2 不支持 Azure Data Lake Analytics 应用程序。 如果有此类应用程序，请确保在从 Gen1 迁移到 Gen2 之前，将它们移动到 Azure Synapse Analytics 或其他受支持的工作负载。

### <a name="option-1-copy-data-from-gen1-to-gen2"></a>选项1：将数据从 Gen1 复制到 Gen2

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到 Data Lake Storage Gen1 帐户并显示帐户概述。

3. 选择“迁移数据”按钮。

   > [!div class="mx-imgBorder"]
   > ![用于进行迁移的按钮](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. 选择“将数据复制到新的 Gen2 帐户”。

   > [!div class="mx-imgBorder"]
   > ![复制数据选项](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-data-option.png)

5. 通过选择复选框，向 Microsoft 授予执行数据迁移的许可。 然后单击“应用”按钮。

   > [!div class="mx-imgBorder"]
   > ![用于提供许可的复选框](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   迁移数据时，Gen1 帐户将变为只读帐户，并且启用了 Gen2 的帐户将被禁用。 迁移完成后，你可以向两个帐户进行读取和写入操作。

   可以通过选择“停止迁移”按钮随时停止迁移。

   > [!div class="mx-imgBorder"]
   > ![“停止迁移”选项](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

### <a name="option-2-perform-a-complete-migration"></a>选项2：执行完整的迁移

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到 Data Lake Storage Gen1 帐户并显示帐户概述。

3. 选择“迁移数据”按钮。

   > [!div class="mx-imgBorder"]
   > ![迁移按钮](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. 选择“完整迁移到新的 gen 2 帐户”。

   > [!div class="mx-imgBorder"]
   > ![“完整迁移”选项](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-complete-option.png)

5. 通过选择复选框，向 Microsoft 授予执行数据迁移的许可。 然后单击“应用”按钮。

   > [!div class="mx-imgBorder"]
   > ![“同意”复选框](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   - 迁移数据时，Gen1 帐户将变为只读帐户，并且启用了 Gen2 的帐户将被禁用。
   - 在重定向 Gen1 URI 时，两个帐户都处于禁用状态。
   - 迁移完成后，Gen1 帐户将处于禁用状态，你可以向启用了 Gen2 的帐户进行读取和写入操作。

   在对 URI 进行重定向之前，可以通过选择“停止迁移”按钮随时停止迁移。

   > [!div class="mx-imgBorder"]
   > ![“迁移停止”按钮](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

## <a name="migrate-workloads-and-applications"></a>迁移工作负载和应用程序

1. 将[工作负载中的服务](./data-lake-storage-supported-azure-services.md)配置为指向 Gen2 终结点。

2. 更新应用程序以使用 Gen2 API。 请参阅以下指南：

   | 环境 | 项目 |
   |--------|-----------|
   |Azure 存储资源管理器 |[使用 Azure 存储资源管理器管理 Azure Data Lake Storage Gen2 中的目录、文件](data-lake-storage-explorer.md)|
   |.NET |[使用 .NET 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-dotnet.md)|
   |Java|[使用 Java 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-java.md)|
   |Python|[使用 Python 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-python.md)|
   |JavaScript (Node.js)|[使用 Node.js 中的 JavaScript SDK 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-javascript.md)|
   |REST API |[Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2)|

3. 更新脚本以使用 Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md) [Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。

4. 搜索代码文件、Databricks 笔记本、Apache Hive HQL 文件或其他任何用作工作负载一部分的文件中包含字符串 `adl://` 的 URI 引用。 将这些引用替换为新存储帐户的 [Gen2 格式 URI](data-lake-storage-introduction-abfs-uri.md)。 例如，Gen1 URI：`adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` 可能会变为 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`。

## <a name="leverage-the-gen1-compatibility-layer-optional"></a>利用 Gen1 兼容性层（可选）

Microsoft 提供功能受限的应用程序兼容性，以便应用程序可以继续使用 Gen1 API 与启用了 Gen2 的帐户中的数据进行交互。 兼容性层在服务器上运行，因此无需安装任何内容。

> [!IMPORTANT]
> Microsoft 不建议将此功能作为迁移工作负载和应用程序的替换项。 在 2024 年 2 月 29 日停用 Gen1 时，将结束对 Gen1 兼容性层的支持。

为了在兼容性层中遇到最少的问题，请确保 Gen1 SDK 使用以下版本（或更高版本）。

   | 语言 | SDK 版本 |
   |--|--|
   | **.NET** | [2.3.9](https://github.com/Azure/azure-data-lake-store-net/blob/master/CHANGELOG.md) |
   | **Java** | [1.1.21](https://github.com/Azure/azure-data-lake-store-java/blob/master/CHANGES.md) |
   | **Python** | [0.0 51](https://github.com/Azure/azure-data-lake-store-python/blob/master/HISTORY.rst) |

Gen2 中不支持以下功能，因此也不支持兼容性层。

- 在某个条目前列出的 ListStatus API  选项。

- 包含超过 4000 个文件且没有延续令牌的 ListStatus API。

- 追加操作的区块编码。

- 使用 https://management.azure.com/ 作为 Azure Active Directory (Azure AD) 令牌受众的任何 API 调用。

- 只有空格或制表符的文件或目录名称，以 `.` 结尾，包含 `:`，或者具有多个连续正斜杠 (`//`)。

## <a name="frequently-asked-questions"></a>常见问题

#### <a name="how-much-does-the-data-migration-cost"></a>数据迁移的费用是多少？

在数据迁移期间，系统将对 Gen1 帐户的数据存储和事务计费。 如果选择仅复制数据的选项，则迁移完成后，系统将对这两个帐户的数据存储和事务计费。 若要避免为 Gen1 帐户支付费用，必须删除 Gen1 帐户。 完成应用程序更新后，请删除 Gen1 帐户。 如果选择执行完整迁移，则系统只会在迁移后对已启用 Gen2 的帐户的数据存储和事务计费。

#### <a name="after-the-migration-completes-can-i-choose-to-go-back-to-using-the-gen1-account"></a>迁移完成后，能否选择用回 Gen1 帐户？

不支持此操作。迁移完成后，Gen1 帐户中的数据将无法访问。 你可以继续在 Azure 门户中查看 Gen1 帐户。做好准备后，可以删除该帐户。

#### <a name="i-would-like-to-enable-geo-redundant-storage-grs-on-the-gen2-account-how-do-i-do-that"></a>我想要在 Gen2 帐户上启用异地冗余存储 (GRS)，应该怎么操作？

迁移完成后，无论选择的是“复制数据”还是“完整迁移”选项，只要没有计划使用应用程序兼容性层，都可以继续将冗余选项更改为 GRS。 应用程序兼容性在使用 GRS 冗余的帐户上不起作用。

#### <a name="gen1-doesnt-have-containers-and-gen2-has-them---what-should-i-expect"></a>Gen1 没有容器，但 Gen2 具有容器 - 我应该注意什么？

将数据复制到启用了 Gen2 的帐户后，我们会自动创建名为 `Gen1` 的容器。 如果选择仅复制数据，可以在数据复制完成后重命名该容器。 如果执行完整的迁移，并且计划使用应用程序兼容性层，则应避免更改容器名称。 不再想要使用兼容性层后，可以更改容器的名称。

## <a name="next-steps"></a>后续步骤

- 了解常规的迁移信息。 有关详细信息，请参阅[将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2](data-lake-storage-migrate-gen1-to-gen2.md)。
