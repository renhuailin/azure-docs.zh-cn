---
title: 配置适用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具 | Microsoft Docs
description: 提供有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的配置命令的指南。
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
ms.topic: reference
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: f12e9ab2ab0faf77520805e3be645a14dbd38885
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929986"
---
# <a name="configure-azure-application-consistent-snapshot-tool"></a>配置 Azure 应用程序一致性快照工具

本文提供有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的配置命令的指南。

## <a name="introduction"></a>简介

可以使用 `azacsnap -c configure` 命令创建或编辑配置文件。

## <a name="command-options"></a>命令选项

`-c configure` 命令具有以下选项

- `--configuration new` 创建新配置文件。

- `--configuration edit` 编辑现有配置文件。

- `[--configfile <config filename>]` 是一个可选参数，可用于自定义配置文件名称。

## <a name="configuration-file-for-snapshot-tools"></a>用于快照工具的配置文件

可以通过运行 `azacsnap -c configure --configuration new` 来创建配置文件。  默认情况下，配置文件名为 `azacsnap.json`。  自定义文件名可以与 `--configfile=` 参数一起使用（例如，`--configfile=<customname>.json`）以下示例适用于 Azure 大型实例配置：

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>所需值的详细信息

以下部分详细介绍了配置文件所需的各种值。

### <a name="sap-hana-values"></a>SAP HANA 值

将“数据库”添加到配置时，需要以下值：

- **HANA 服务器的地址** = SAP HANA 服务器主机名或 IP 地址。
- **HANA SID** = SAP HANA 系统 ID。
- **HANA 实例编号** = SAP HANA 实例编号。
- **HANA HDB 用户存储密钥** = 配置有运行数据库备份权限的 SAP HANA 用户。

- 单个节点：节点的 IP 和主机名
- 带有 STONITH 的 HSR：节点的 IP 和主机名
- 横向扩展（N+N、N+M）：当前主节点 IP 和主机名
- 不带 STONITH 的 HSR：节点的 IP 和主机名
- 单个节点上的多 SID：承载这些 SID 的节点的主机名和 IP

### <a name="azure-large-instance-hli-storage-values"></a>Azure 大型实例 (HLI) 存储值

向数据库部分添加“HLI 存储”时，需要以下值：

- **存储用户名** = 此值是用于建立到存储的 SSH 连接的用户名。
- **存储 IP 地址** = 存储系统的地址。
- **存储卷名称** = 快照的卷名称。  可以通过多种方式确定此值，最简单的方式可能是尝试运行以下 shell 命令：

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Azure NetApp 文件 (ANF) 存储值

向数据库部分添加“ANF 存储”时，需要以下值：

- **服务主体身份验证文件名** = 这是使用 Azure NetApp 文件存储配置通信时，在 Cloud Shell 中生成的 `authfile.json` 文件。
- **完整 ANF 存储卷资源 ID** = 要创建快照的卷的完整资源 ID。  可从“Azure 门户”–> ANF –>“卷”–>“设置/属性”–>“资源 ID”中检索此 ID

## <a name="configuration-file-overview-azacsnapjson"></a>配置文件概述 (`azacsnap.json`)

在下面的示例中，`azacsnap.json` 配置了一个 SID。

参数值必须设置为客户特定的 SAP HANA 环境。
对于“Azure 大型实例”系统，此信息由 Microsoft 服务管理在加入/移交调用期间提供，并在移交期间提供的 Excel 文件中可用。 如果你需要再次获取此信息，请打开服务请求。

下面只是一个示例，相应地更新所有值。

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
              }
            ]
          }
        ],
        "anfStorage": []
      }
    }
  ]
}
```

> [!NOTE]
> 对于要在 DR 站点上运行备份的 DR 方案，在 DR 站点上在 DR 配置文件中配置的 HANA 服务器名称（例如，`DR.json`）应与生产服务器名称相同。

> [!NOTE]
> 对于 Azure 大型实例，存储 IP 地址必须与服务器池位于同一子网中。 例如，在本例中，我们的服务器池子网为 172. 18. 18 .0/24，我们分配的存储 IP 为 172.18.18.11。

## <a name="next-steps"></a>后续步骤

- [测试 Azure 应用程序一致性快照工具](azacsnap-cmd-ref-test.md)
