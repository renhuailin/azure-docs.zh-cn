---
title: 备份应用
description: 了解如何在 Azure 应用服务中创建应用备份。 运行手动或计划的备份。 通过包含附加的数据库来自定义备份。
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: f1bd37c1b3557a8106981377f9ed75c50a28c773
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433046"
---
# <a name="back-up-your-app-in-azure"></a>在 Azure 中备份应用

利用 [Azure App Service](overview.md) 中的备份和还原功能，可以轻松地手动或按计划创建应用备份。 可以将备份配置为无限期保留。 通过覆盖现有应用或还原为另一应用可将应用还原为先前状态的快照。

有关从备份中还原应用的信息，请参阅[在 Azure 中还原应用](web-sites-restore.md)。

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>备份的内容

应用服务可将以下信息备份到你配置应用使用的 Azure 存储帐户和容器。

* 应用配置
* 文件内容
* 连接到应用的数据库

备份功能支持以下数据库解决方案：

- [SQL 数据库](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL 应用内产品](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)

> [!NOTE]
> 每个备份都是应用的完整脱机副本，而不是增量更新。
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>要求和限制

* 备份和还原功能要求应用服务计划处于“标准”、“高级”或“隔离”层  。 有关缩放应用服务计划以使用更高层的详细信息，请参阅[增加 Azure 中的应用](manage-scale-up.md)。 与标准层相比，高级层和隔离层每日允许更多备份量  。
* 在与要备份的应用相同的订阅中，需要有一个 Azure 存储帐户和容器。 有关 Azure 存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)。
* 最多可以备份 10 GB 的应用和数据库内容，其中的数据库备份大小最大可为 4 GB。 如果备份大小超过此限制，会出错。
* 不支持备份[启用了 TLS 的 Azure Database for MySQL](../mysql/concepts-ssl-connection-security.md)。 如果已配置备份，备份将失败。
* 不支持备份[启用了 TLS 的 Azure Database for PostgreSQL](../postgresql/concepts-ssl-connection-security.md)。 如果已配置备份，备份将失败。
* 应用内 MySQL 数据库无需任何配置即可自动备份。 如果对应用内 MySQL 数据库进行手动设置，例如添加连接字符串，则备份可能无法正常工作。
* 不支持将[启用了防火墙的存储帐户](../storage/common/storage-network-security.md)用作备份目标。 如果已配置备份，备份将失败。
* 不支持使用[启用了专用终结点的存储帐户](../storage/common/storage-private-endpoints.md)进行备份和还原。

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>创建手动备份

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用的页面，然后选择“备份”。 将显示“备份”页。

    ![“备份”页面](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > 若显示以下消息，请单击该消息升级 应用服务计划，才能继续备份。
    > 有关详细信息，请参阅[增加 Azure 中的应用](manage-scale-up.md)。
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="横幅屏幕截图，上面的消息显示请升级应用服务计划以使用“备份和还原”功能。":::
    >
    >

2. 在“备份”页上，选择“未配置备份”。 单击此处为应用配置备份。

    ![单击“配置”](./media/manage-backup/configure-start.png)

3. 在“备份配置”页中，单击“未配置存储”来配置存储帐户 。

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="“备份存储”部分的屏幕截图，其中已选择“未配置存储”设置。":::

4. 选择“存储帐户”和“容器”来选择备份目标。 该存储帐户必须与要备份的应用属于同一订阅。 也可在各自的页面中新建存储帐户或容器。 完成后，单击“选择”。

5. 在仍处于打开状态的“备份配置”页中，可配置“备份数据库”，接着选择要包含在备份中的数据库（SQL 数据库或 MySQL），然后单击“确定”  。

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="“备份数据库”部分的屏幕截图，其中显示“加入备份”选择。":::

    > [!NOTE]
    > 若要使数据库显示在此列表中，其连接字符串必须位于应用中“应用程序设置”页的“连接字符串”部分中 。 
    >
    > 应用内 MySQL 数据库无需任何配置即可自动备份。 如果对应用内 MySQL 数据库进行手动设置，例如添加连接字符串，则备份可能无法正常工作。
    >
    >

6. 在“备份配置”页中，单击“保存” 。
7. 在“备份”页中，单击“备份” 。

    ![BackUpNow 按钮](./media/manage-backup/manual-backup.png)

    备份过程中会显示进度消息。

配置存储帐户和容器后，可随时启动手动备份。 手动备份会无限期保留。

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>配置自动执行的备份

1. 在“备份配置”页中，将“计划备份”设置为“开”  。

    ![启用自动化的备份](./media/manage-backup/scheduled-backup.png)

2. 根据需要配置备份计划，并选择“确定”。

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>配置部分备份

有时你不想备份应用中的所有内容。 以下是一些示例：

* [设置每周备份](#configure-automated-backups)应用，其中包含永远不会更改的静态内容，例如旧的博客文章或图像。
* 应用的内容超过 10 GB（这是一次可以备份的最大量）。
* 不想备份日志文件。

使用部分备份可以精确选择需要备份的文件。

> [!NOTE]
> 备份中的单个数据库最大可为 4GB，但备份的最大总大小为 10GB

### <a name="exclude-files-from-your-backup"></a>从备份中排除文件

假定应用中包含的日志文件和静态映像已经过备份且不会发生更改。 这种情况下，可排除这些文件夹和文件，以免其存储在将来的备份中。 若要从备份中排除文件和文件夹，请在应用的 `D:\home\site\wwwroot` 文件夹中创建一个 `_backup.filter` 文件。 指定要在此文件中排除的文件和文件夹列表。

可以通过导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 来访问文件。 如果出现系统提示，请登录到你的 Azure 帐户。

确定要从备份中排除的文件夹。 例如，筛选出突出显示的文件夹和文件。

![映像文件夹](./media/manage-backup/kudu-images.png)

创建名为 `_backup.filter` 的文件并将上述列表放在文件中，但删除 `D:\home`。 每行列出一个目录或文件。 文件的内容应为：

```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

使用 [ftp](deploy-ftp.md) 或任何其他方法，将 `_backup.filter` 文件上传到站点的 `D:\home\site\wwwroot\` 目录。 如果需要，可使用 Kudu `DebugConsole` 直接创建此文件，并在其中插入内容。

采用通常使用的相同方式运行备份，即[手动](#create-a-manual-backup)或[自动](#configure-automated-backups)。 现在，`_backup.filter` 中指定的任何文件和文件夹已从计划启动或手动启动的将来备份中排除。

> [!NOTE]
> 采用与[还原定期备份](web-sites-restore.md)相同的方式，还原站点的部分备份。 还原过程会执行正确的操作。
>
> 还原完整备份后，站点上的所有内容都被替换为备份中的任何内容。 如果文件在站点上但不在备份中，则会将其删除。 但在还原部分备份时，位于其中一个限制目录或任何限制文件中的任何内容保持不变。
>

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>如何存储备份

对应用进行了一次或多次备份后，可在存储帐户的“容器”页中看到备份以及应用。 在存储帐户中，每个备份都由一个 `.zip` 文件和一个 `.xml` 文件组成，前者包含备份数据，后者包含 `.zip` 文件内容的清单。 如果想要在无需实际执行应用还原的情况下访问备份，则可以解压缩并浏览这些文件。

应用的数据库备份存储在 .zip 文件的根目录中。 对于 SQL 数据库，这是 BACPAC 文件（无文件扩展名），并且可以导入。 若要基于 BACPAC 导出在 Azure SQL 数据库中创建数据库，请参阅[导入 BACPAC 文件以在 Azure SQL 数据库中创建数据库](../azure-sql/database/database-import.md)。

> [!WARNING]
> 改动 **websitebackups** 容器中的任何文件都导致备份无效，进而无法还原。

## <a name="troubleshooting"></a>故障排除

“备份”页显示每个备份的状态。 如果单击某个失败的备份，可以获取有关失败的日志详细信息。 使用下表来帮助排查备份问题。 如果该表中未提到这种失败，请提交支持票证。

| 错误 | Fix |
| - | - |
| 存储访问失败。 | 删除备份计划，然后重新配置。 或者重新配置备份存储。 |
| 网站和数据库的合计大小超过了备份限制 ({0} GB)。 内容大小为 {1} GB。 | 从备份中[排除某些文件](#configure-partial-backups)，或删除备份的数据库部分，并改用外部提供的备份。 |
| 连接到服务器 {1} 上的数据库 {0} 时出错: 使用方法“mysql_native_password”在主机“{1}”中对用户“\<username>”进行身份验证失败并出现以下消息: 未知的数据库“\<db-name>” | 更新数据库连接字符串。 |
| 无法解析 {0}。 {1} (CannotResolveStorageAccount) | 删除备份计划，然后重新配置。 |
| 用户“{0}”登录失败。 | 更新数据库连接字符串。 |
| 创建 {0} 数据库副本({1})引发了异常。 无法创建数据库副本。 | 在连接字符串中使用管理用户。 |
| 服务器主体“\<name>”无法在当前安全上下文下访问数据库“master”。 无法打开登录名请求的数据库 "master"。 登录失败。 用户“\<name>”登录失败。 | 在连接字符串中使用管理用户。 |
| 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。 （提供程序：命名管道提供程序，错误: 40 - 无法打开到 SQL Server 的连接）。 | 检查连接字符串是否有效。 在数据库服务器设置中允许应用的[出站 IP](overview-inbound-outbound-ips.md)。 |
| 无法打开登录时请求的服务器“\<name>”。 登录失败。 | 检查连接字符串是否有效。 |
| 缺少有效共享访问签名的必需参数。 | 删除备份计划，然后重新配置。 |
| 需要 SSL 连接。 请指定 SSL 选项，然后重试。 尝试连接。 | 改用 Azure MySQL 或 Azure Postgressql 中的内置备份功能。 |

## <a name="automate-with-scripts"></a>使用脚本自动执行

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/) 中使用脚本自动备份管理。

相关示例如下所示：

- [Azure CLI 示例](samples-cli.md)
- [Azure PowerShell 示例](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>后续步骤

有关从备份中还原应用的信息，请参阅[在 Azure 中还原应用](web-sites-restore.md)。
