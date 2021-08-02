---
title: 适用于 Azure NetApp 文件的 Troubleshoot Azure 应用程序一致快照工具 | Microsoft Docs
description: 提供有关使用可与 Azure NetApp 文件一起使用 Azure 应用程序一致快照工具的疑难解答内容。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/17/2021
ms.author: phjensen
ms.openlocfilehash: 85f6c7d8ef0eced1e7cbb2259d4117bc1ae5ff89
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083721"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool"></a>排查 Azure 应用程序一致性快照工具问题

本文提供与使用 Azure 应用程序一致性快照工具相关的故障排除内容，该工具可以与 Azure NetApp 文件和 Azure 大型实例配合使用。

下面是在运行这些命令时可能会遇到的常见问题。 请按照本文提到的解决方案说明解决这些问题。 如果仍然遇到问题，请从 Azure 门户中打开服务请求，并将请求分配到 SAP HANA 大型实例队列，以便 Microsoft 支持部门做出响应。

## <a name="log-files"></a>日志文件

使用 AzAcSnap 调试任何错误的最佳信息源之一是日志文件。  

### <a name="log-file-location"></a>日志文件位置

日志文件存储在根据 `logPath`AzAcSnap 配置文件中的参数配置的目录中。  默认配置文件名为 `azacsnap.json` ，`logPath` 的默认值为 `"./logs"`，这意味着日志文件将写入与运行 `azacsnap` 命令相关的`./logs`目录中。  赋予 `logPath` 绝对位置 (例如 `/home/azacsnap/logs`) 将确保将 `azacsnap` 日志输出到 `/home/azacsnap/logs`，而不考虑运行 `azacsnap` 命令的位置。

### <a name="log-file-naming"></a>日志文件命名

日志文件名基于应用程序名称 (例如 `azacsnap`) 、所使用的命令选项（`-c`） (例如 `backup`、`test`、`details`等 ) 和配置文件名 (例如默认值 = `azacsnap.json`) 。  因此，如果使用 `-c backup` 命令，则默认情况下日志文件名为 `azacsnap-backup-azacsnap.log` ，并写入到 `logPath` 配置的目录中。  

这种命名约定允许多个配置文件（每个数据库一个），并确保易于查找关联的日志文件。  因此，如果配置文件名为 `SID.json` ，则在使用 `azacsnap -c backup --configfile SID.json` 选项时，结果文件名为 `azacsnap-backup-SID.log` 。

### <a name="result-file-and-syslog"></a>结果文件和 syslog

对于 `-c backup` 命令选项，AzAcSnap 将使用 `logger` 命令写入 `*.result` 文件和系统日志 (`/var/log/messages`) 。  `*.result` 文件名与 [ 日志文件 ](#log-file-naming) 具有相同的基名称，并与 [ 日志文件 ](#log-file-location) 位于同一位置。  这是一个简单的单行输出文件，其示例如下所示。

`*.result` 文件输出示例。
```output
Database # 1 (PR1) : completed ok
```

`/var/log/messages` 文件输出示例。
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>与 Azure NetApp 文件的通信失败

当验证与 Azure NetApp 文件的通信时，通信可能会失败或超时。请检查以确保防火墙规则不会阻止从运行 AzAcSnap 的系统到以下地址和 TCP/IP 端口的出站流量：

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

## <a name="problems-with-sap-hana"></a>SAP HANA 的问题

### <a name="running-the-test-command-fails"></a>运行测试命令失败

通过运行测试 `azacsnap -c test --test hana` 来验证与 SAP HANA 的通信时，它会提供以下错误：

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

解决方案：

1. 检查每个 HANA 实例的配置文件 (例如 `azacsnap.json`），以确保 SAP HANA 数据库值正确。
1. 尝试运行以下命令来验证 `hdbsql` 命令是否在路径中，是否可以连接到 SAP HANA 服务器。 下面的示例演示了正确运行该命令及其输出。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    在此示例中， `hdbsql` 命令不在用户中 `$PATH` 。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    在此示例中，`hdbsql` 命令暂时被添加到用户的 `$PATH` 中，但在运行时，如果 `hdbuserstore Set` 命令未正确设置 (请参阅入门指南以了解详细信息) ：

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (AZACSNAP)
    ```

    > [!NOTE]
    > 若要永久添加到用户的 `$PATH` ，请更新用户的 `$HOME/.profile` 文件

### <a name="insufficient-privilege"></a>权限不足

如果运行 `azacsnap` 时显示错误（例如“`* 258: insufficient privilege`”），请进行检查，确保已为“AZACSNAP”数据库用户（假定这是根据[安装指南](azacsnap-installation.md#enable-communication-with-sap-hana)创建的用户）分配适当的权限。  使用以下命令验证用户的当前权限：

```bash
hdbsql -U AZACSNAP "select GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE from sys.granted_privileges "' | grep -i -e GRANTEE -e azacsnap
```

```output
GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE
"AZACSNAP","USER","BACKUP ADMIN","TRUE","FALSE"
"AZACSNAP","USER","CATALOG READ","TRUE","FALSE"
"AZACSNAP","USER","CREATE ANY","TRUE","TRUE"
```

该错误还可能提供进一步的有助于确定所需 SAP HANA 权限的信息，例如输出“`Detailed info for this error can be found with guid '99X9999X99X9999X99X99XX999XXX999' SQLSTATE: HY000`”。  在这种情况下，请按 [SAP 帮助门户 - GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/9a73c4c017744288b8d6f3b9bc0db043.html) 中的 SAP 说明操作，该门户建议使用以下 SQL 查询来确定所需特权的详细信息。

```sql
CALL SYS.GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS ('99X9999X99X9999X99X99XX999XXX999', ?)
```

```output
GUID,CREATE_TIME,CONNECTION_ID,SESSION_USER_NAME,CHECKED_USER_NAME,PRIVILEGE,IS_MISSING_ANALYTIC_PRIVILEGE,IS_MISSING_GRANT_OPTION,DATABASE_NAME,SCHEMA_NAME,OBJECT_NAME,OBJECT_TYPE
"99X9999X99X9999X99X99XX999XXX999","2021-01-01 01:00:00.180000000",120212,"AZACSNAP","AZACSNAP","DATABASE ADMIN or DATABASE BACKUP ADMIN","FALSE","FALSE","","","",""
```

在以上示例中，将“DATABASE BACKUP ADMIN”权限添加到 SYSTEMDB 的 AZACSNAP 用户应该就会解决权限不足的错误。

### <a name="the-hdbuserstore-location"></a>`hdbuserstore` 位置

当设置与 SAP HANA 的通信时， `hdbuserstore` 程序用于创建安全通信设置。  该 `hdbuserstore` 程序通常位于 `/usr/sap/<SID>/SYS/exe/hdb/` 或 `/usr/sap/hdbclient`或下面 。  通常，安装程序将正确的位置添加到 `azacsnap` 用户的 `$PATH`。

## <a name="failed-test-with-storage"></a>测试存储失败

命令 `azacsnap -c test --test storage`未顺利完成。

### <a name="azure-large-instance"></a>Azure 大型实例

下面的示例是在 `azacsnap` Azure 大型实例上的 SAP HANA 上运行的：

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**解决方案：** 当 Azure 大型实例存储用户无权访问基础存储时，通常会出现上述错误。 若要使用所提供的存储用户验证对存储的访问权限，请运行 `ssh` 命令来验证与存储平台的通信。

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

具有预期输出的示例：

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>无法建立主机 '172.18.18.11 (172.18.18.11)' 的验证。

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**解决方案：** 请勿选择 "是"。 确保你的存储 IP 地址正确。 如果仍有问题，请使用 Microsoft 操作团队确认存储 IP 地址。

### <a name="azure-netapp-files"></a>Azure NetApp 文件

以下示例是在 `azacsnap` VM 上使用 Azure NetApp 文件运行的：

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

解决方案：

1. 检查在 `azacsnap.json` 配置文件中设置的服务主体文件 `azureauth.json` 是否存在。
1. 检查日志文件 (例如， `logs/azacsnap-test-azacsnap.log`) 以查看服务主体 (`azureauth.json`) 是否包含正确的内容。  日志中的示例，如下所示：

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. 检查日志文件 (例如， `logs/azacsnap-test-azacsnap.log`) 以查看服务主体 (`azureauth.json`) 是否已过期。 日志中的示例，如下所示：

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>后续步骤

- [提示](azacsnap-tips.md)
