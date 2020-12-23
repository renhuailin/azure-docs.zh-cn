---
title: 排查 Azure NetApp 文件 Azure 应用程序一致性快照工具的问题 |Microsoft Docs
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
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632577"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a> (预览版 Azure 应用程序一致性快照工具疑难解答) 

本文提供了使用可与 Azure NetApp 文件一起使用 Azure 应用程序一致快照工具的疑难解答内容。

下面是在运行这些命令时可能会遇到的常见问题。 请按照提到的解决方法说明解决该问题。 如果仍然遇到问题，请从 Azure 门户中打开服务请求，并将请求分配到 SAP HANA 大型实例队列，以便 Microsoft 支持部门做出响应。

## <a name="log-files"></a>日志文件

用于调试 AzAcSnap 的任何错误的一种最佳信息源是日志文件。  

### <a name="log-file-location"></a>日志文件位置

日志文件存储在根据 `logPath` AzAcSnap 配置文件中的参数配置的目录中。  默认配置文件名为 `azacsnap.json` ，默认值为， `logPath` `"./logs"` 这意味着日志文件将写入与 `./logs` 运行命令相关的目录中 `azacsnap` 。  使 `logPath` 绝对位置 (例如 `/home/azacsnap/logs`) 会确保将 `azacsnap` 日志输出到，而不 `/home/azacsnap/logs` 考虑 `azacsnap` 命令的运行位置。

### <a name="log-file-naming"></a>日志文件命名

日志文件名基于应用程序名称 (例如 `azacsnap`) ， () 使用的命令选项 `-c` (例如，、、等 ) `backup` `test` `details` 和配置文件名 (例如，默认值 = `azacsnap.json`) 。  因此，如果使用 `-c backup` 命令，则默认情况下日志文件名为 `azacsnap-backup-azacsnap.log` ，并写入到配置的目录中 `logPath` 。  

此命名约定建立为允许多个配置文件（每个数据库一个），并确保易于查找关联的日志文件。  因此，如果配置文件名为 `SID.json` ，则在使用选项时，结果文件名 `azacsnap -c backup --configfile SID.json` 将为 `azacsnap-backup-SID.log` 。

### <a name="result-file-and-syslog"></a>结果文件和 syslog

对于 `-c backup` 命令选项，AzAcSnap `*.result` 使用命令写入文件和系统日志 (`/var/log/messages`) `logger` 。  `*.result`文件名与[日志文件](#log-file-naming)具有相同的基名称，并与[日志文件](#log-file-location)位于同一位置。  这是一个简单的单行输出文件，每个示例如下所示。

从文件输出的示例 `*.result` 。
```output
Database # 1 (PR1) : completed ok
```

从文件输出的示例 `/var/log/messages` 。
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>与 SAP HANA 的通信失败

通过使用运行测试来验证与 SAP HANA 的通信时 `azacsnap -c test --test hana` ，它提供以下错误：

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

解决方案：

1. 检查配置文件 (例如， `azacsnap.json` 为每个 HANA 实例) ，以确保 SAP HANA 数据库值正确。
1. 尝试运行以下命令来验证该 `hdbsql` 命令是否在路径中，是否可以连接到 SAP HANA 服务器。 下面的示例演示正确运行该命令及其输出。

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

    在此示例中，该 `hdbsql` 命令不在用户中 `$PATH` 。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    在此示例中，该 `hdbsql` 命令暂时添加到用户的 `$PATH` 中，但在运行时，如果命令未正确设置 `hdbuserstore Set` (请参阅入门指南以了解详细信息) ：

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > 若要永久添加到用户的 `$PATH` ，请更新用户的 `$HOME/.profile` 文件

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`位置

设置与 SAP HANA 的通信时， `hdbuserstore` 程序用于创建安全通信设置。  此 `hdbuserstore` 程序通常位于或下 `/usr/sap/<SID>/SYS/exe/hdb/` `/usr/sap/hdbclient` 。  通常，安装程序将正确的位置添加到 `azacsnap` 用户的 `$PATH` 。

## <a name="failed-test-with-storage"></a>测试存储失败

命令 `azacsnap -c test --test storage` 未成功完成。

### <a name="azure-large-instance"></a>Azure 大型实例

下面的示例是在 `azacsnap` Azure 大型实例上的 SAP HANA 上运行：

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

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>无法建立主机 "172.18.18.11 (172.18.18.11) " 的真实性

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

**解决方案：** 不要选择 "是"。 确保你的存储 IP 地址正确。 如果仍有问题，请使用 Microsoft 操作团队确认存储 IP 地址。

### <a name="azure-netapp-files"></a>Azure NetApp 文件

以下示例是在 `azacsnap` VM 上使用 Azure NetApp 文件运行：

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

1. 检查在 `azureauth.json` 配置文件中设置的服务主体文件是否存在 `azacsnap.json` 。
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
