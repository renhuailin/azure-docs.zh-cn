---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：使用 MySQL Workbench 完成数据迁移
description: 按照安装指南中的所有步骤创建环境，以支持以下步骤。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 2b3dc8702251a6fcc53386cb17cbe44a45e59db2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292968"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration-with-mysql-workbench"></a>将本地 MySQL 迁移到 Azure Database for MySQL：使用 MySQL Workbench 完成数据迁移

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[数据迁移](08-data-migration.md)

## <a name="setup"></a>设置

按照安装指南中的所有步骤创建环境，以支持以下步骤。

## <a name="configuring-server-parameters-source"></a>配置服务器参数（源）

根据选择的迁移类型（脱机和联机），需要评估是否要修改服务器参数以支持快速传出数据。 如果联机迁移，可能无需对服务器参数执行任何操作，因为系统可能会执行 `binlog` 复制并自行同步数据。 但是，如果脱机迁移，则在停止应用程序流量后，可以将服务器参数从支持工作负荷中切换为支持导出。

## <a name="configuring-server-parameters-target"></a>配置服务器参数（目标）

在开始向 Azure Database for MySQL 导入进程之前，请查看服务器参数。 可使用 [Azure 门户](../../howto-server-parameters.md)检索和设置服务器参数，或者通过调用 [Azure PowerShell for MySQL cmdlet](../../howto-configure-server-parameters-using-powershell.md) 进行更改。

执行以下 PowerShell 脚本以获取所有参数：

```
\[Net.ServicePointManager\]::SecurityProtocol = \[Net.SecurityProtocolType\]::Tls
12

Install-Module -Name Az.MySql
Connect-AzAccount
$rgName = "{RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";
Get-AzMySqlConfiguration -ResourceGroupName $rgName -ServerName $serverName
```

- 若要使用 mysql 工具执行相同操作，请将 [CA 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)下载到 c:\\temp（创建此目录）。

    > [!NOTE]
    > 此证书可能会更改。 有关证书的最新信息，请参阅[配置应用程序中的 SSL 连接性以安全连接到 Azure Database for MySQL](../../howto-configure-ssl.md)。
    
- 在命令提示符下运行以下命令，确保更新令牌：

```
mysql --host {servername}.mysql.database.azure.com --database mysql --user 
{u sername}@{servername} -p --ssl-ca=c:\\temp\\BaltimoreCyberTrustRoot.crt.cer
-A -e "SHOW GLOBAL VARIABLES;" \> c:\\temp\\settings\_azure.txt
```

在新的“\`settings\_azure.txt\`”文件中，可以看到默认的 Azure Database for MySQL 服务器参数，如[附录](15-appendix.md#environment-setup)中所示。

若要支持迁移，请设置目标 MySQL 实例参数以允许更快传入。 在开始数据迁移之前，应设置下列服务器参数：

- `max\_allowed\_packet` – 将参数设置为 `1073741824`（即 1 GB）或数据库中某行的最大大小，以避免因长行导致溢出问题。 如果有需要拉取（或读取）的大型 BLOB 行，请考虑调整此参数。

- `innodb\_buffer\_pool\_size` – 在迁移期间从门户的定价层纵向扩展服务器到 32 vCore 内存优化 SKU，以增大 innodb\_buffer\_pool\_size。 只能通过纵向扩展 Azure Database for MySQL 服务器的计算来增大 Innodb\_buffer\_pool\_size。 参阅 [Azure Database for MySQL 中的服务器参数](../../concepts-server-parameters.md#innodb_buffer_pool_size)，以了解该层的最大值。 内存优化 32 vCore 系统中的最大值为 `132070244352`。

- `innodb\_io\_capacity` & `innodb\_io\_capacity\_max` - 将该参数更改为 `9000` 以提高 IO 利用率，以从而化迁移速度。

- `max\_connections` - 如果使用生成多个线程的工具来提高吞吐量，请增加连接以支持该工具。 默认值为 `151`，最大值为 `5000`。

    > [!NOTE]
    > 执行缩放时请小心。 某些操作不能撤消，如存储缩放。
    
可以使用以下 Azure PowerShell cmdlet 更新这些设置：

```
Install-Module -Name Az.MySql
$rgName = " {RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";

Select-AzSubscription -Subscription "{SUBSCRIPTION\_ID}"
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName
$rgna me -ServerName $serverName -Value 1073741824  
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName 
$rgna me -ServerName $serverName -Value 9000
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 9000

\#required if You've functions

Update-AzMySqlConfiguration -Name log\_bin\_trust\_function\_creators 
-ResourceGr oupName $rgname -ServerName $serverName -Value ON
```
## <a name="data"></a>数据

### <a name="tool-choice"></a>工具选择

迁移源系统中的数据库对象和用户后，就可以开始迁移。 MySQL 版本 8.0 上运行的数据库无法使用 Azure DMS 来迁移工作负荷。 相反，迁移用户应该使用 MySQL 工作台。

### <a name="manual-import-and-export-steps"></a>手动导入和导出步骤

- 打开 MySQL 工作台并连接为本地数据库的根用户。

- 在\*\*管理\*\*下，选择\*\*数据导出\*\*。 选择 reg\_app 架构。

- 在“导出对象”中，选择“转储存储过程和函数”、“转储事件”和“转储触发器”。

- 在“导出选项”下，选择“导出到独立文件”。

- 同时，选中“包括创建架构”复选框。 请参阅下图以查看正确的 mysqldump 配置。

    ![包括创建架构](./media/image6.jpg)

    **测试**

- 如果这些选项中的任何一个不可用，它们可能是受到“输出”窗格的阻碍。 只需更改编辑器布局即可。

    ![编辑器布局](./media/image7.jpg)

    **测试**

- 选择“导出进度”选项卡。

- 选择“开始导出”，并注意 MySQL Workbench 会调用 `mysqldump` 工具。

- 打开新创建的导出脚本。

- 查找所有 `DEFINER` 语句，并将其更改为有效用户或完全删除。

> [!NOTE]
> 可以通过在 mysqldump 命令中传递 `--skip-definer` 来完成此操作。 这不是 MySQL 工作台中的选项；因此需要在导出命令中手动删除这些行。 尽管我们在此处指出了要删除的四项，但从一个 MySQL 版本迁移到另一个版本（如新保留字）时，会出现其他可能失败的项。

- 查找 `SET GLOBAL` 语句，并将其更改为有效用户或完全删除。

- 确保未将 `sql\_mode` 设置为 `NO\_AUTO\_CREATE\_USER`。

- 删除 `hello\_world` 函数。

- 在 MySQL Workbench 中，创建与 Azure Database for MySQL 的新连接。

    - 对于“主机名”，输入完整服务器 DNS（例如：`servername.mysql.database.azure.com`）。

    - 输入用户名（例如：`sqlroot@servername`）。

    - 选择“SSL”选项卡。

    - 对于 SSL CA 文件，浏览找到“BaltimoreCyberTrustRoot.crt.cer”密钥文件。

    - 选择“测试连接”，确保完成连接。

    - 选择“确定”。

        ![“MySQL 连接”对话框](./media/image8.jpg)

        “MySQL 连接”对话框如上所示。

- 选择“文件 -\> 打开 SQL 脚本”。

- 浏览找到转储文件，然后选择“打开”。

- 选择“执行”。

## <a name="update-applications-to-support-ssl"></a>更新应用程序以支持 SSL

- 使用 Visual Studio Code 切换到 Java 服务器 API。

- 打开“launch.json”文件。

- 将“DB\_CONNECTION\_URL”更新为 `jdbc:mysql://serverDNSname:3306/reg\_app?useUnicode=true\&useJDBCCompliantT imezoneShift=true\&useLegacyDatetimeCode=false\&serverTimezone=UTC\&verifySe rverCertificate=true\&useSSL=true\&requireSSL=true\&noAccessToProcedureBodie s=true.`。注意其他 SSL 参数。

- 将“DB\_USER\_NAME”更新为 conferenceuser@servername。

- 启动“调试”配置，并确保应用程序使用新数据库在本地运行。

## <a name="revert-server-parameters"></a>修改服务器参数

可以更改 Azure Database for MySQL 目标实例上的以下参数。 可以通过 Azure 门户或使用 [Azure PowerShell for MySQL cmdlet](../../howto-configure-server-parameters-using-powershell.md) 来设置这些参数。

```
$rgName = "YourRGName";
$serverName = "servername";
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName 
$rgna me -ServerName $serverName -Value 536870912
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName $rgna 
me -ServerName $serverName -Value 200
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 2000
```
## <a name="change-connection-string-for-the-java-api"></a>更改 Java API 的连接字符串

- 使用以下命令更改应用服务 Java API 的连接字符串

```
$rgName = "YourRGName"; 
$app_name = "servername";
az webapp config appsettings set -g $rgName -n $app_name 
--settings DB_CONNECTION_URL={DB_CONNECTION_URL}
```

> [!NOTE]
> 请记住，可以在门户中设置连接字符串。

- 重启应用服务 API

```
az webapp restart -g $rgName -n $app\_name
```
已成功完成本地 Azure Database for MySQL 迁移\!  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移后管理](./10-post-migration-management.md)