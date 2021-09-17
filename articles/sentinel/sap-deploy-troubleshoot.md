---
title: Azure Sentinel SAP 解决方案部署故障排除 | Microsoft Docs
description: 了解如何对 Azure Sentinel SAP 解决方案部署中可能发生的特定问题进行故障排除。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: troubleshooting
ms.custom: mvc
ms.date: 08/09/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 525048346edc184744a69c70c8fd4dc0db1bd554
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681203"
---
# <a name="troubleshooting-your-azure-sentinel-sap-solution-deployment"></a>Azure Sentinel SAP 解决方案部署故障排除

## <a name="useful-docker-commands"></a>有用的 Docker 命令

在对 SAP 数据连接器进行故障排除时，可能会发现以下命令很有用：

|函数  |命令  |
|---------|---------|
|停止 Docker 容器     |  `docker stop sapcon-[SID]`       |
|启动 Docker 容器     |`docker start sapcon-[SID]`         |
|查看 Docker 系统日志     |  `docker logs -f sapcon-[SID]`       |
|输入 Docker 容器     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

有关详细信息，请参阅 [Docker CLI 文档](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="review-system-logs"></a>查看系统日志

强烈建议在安装或者[重置数据连接器](#reset-the-sap-data-connector)后查看系统日志。

运行：

```bash
docker logs -f sapcon-[SID]
```

## <a name="enable-debug-mode-printing"></a>启用调试模式打印

若要启用调试模式打印，请执行以下操作：

1. 将以下文件复制到 sapcon/[SID] 目录，然后将其重命名为 `loggingconfig.yaml`： https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [重置 SAP 数据连接器](#reset-the-sap-data-connector)。

例如，对于 SID `A4H`：

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.y
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

若要再次禁用调试模式打印，请运行：

```bash
mv loggingconfig.yaml loggingconfig.old
ls
docker restart sapcon-[SID]
```

## <a name="view-all-docker-execution-logs"></a>查看所有 Docker 执行日志

若要查看 Azure Sentinel SAP 数据连接器部署的所有 Docker 执行日志，请运行以下命令之一：

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

或

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

应会显示类似于以下内容的输出：

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Azure Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

若要将日志复制到主机操作系统，请运行：

```bash
docker cp sapcon-[SID]:/sapcon-app/sapcon/logs /directory
```

例如：

```bash
docker cp sapcon-A4H:/sapcon-app/sapcon/logs /tmp/sapcon-logs-extract
```

## <a name="review-and-update-the-sap-data-connector-configuration"></a>查看和更新 SAP 数据连接器配置

如果要检查 SAP 数据连接器配置文件并手动进行更新，请执行以下步骤：

1. 在 VM 上的用户主目录中，打开 ~/sapcon/[SID]/systemconfig.ini 文件。
1. 根据需要更新配置，然后重启容器：

    ```bash
    docker restart sapcon-[SID]
    ```

## <a name="reset-the-sap-data-connector"></a>重置 SAP data 连接器

以下步骤将重置连接器并重新引入最近 24 小时内的 SAP 日志。

1.  停止连接器。 运行：

    ```bash
    docker stop sapcon-[SID]
    ```

1.  从 sapcon/[SID] 目录中删除 metadata.db 文件。  运行：

    ```bash
    cd ~/sapcon/<SID>
    ls
    mv metadata.db metadata.old
    ```

    > [!NOTE]
    > metadata.db 文件包含每个日志的最后一个时间戳，可防止重复。

1. 再次启动连接器。 运行：

    ```bash
    docker start sapcon-[SID]
    ```

完成后确保[查看系统日志](#review-system-logs)。



## <a name="common-issues"></a>常见问题

部署 SAP 数据连接器和安全内容后，你可能会遇到以下错误或问题：

### <a name="corrupt-or-missing-sap-sdk-file"></a>SAP SDK 文件损坏或缺失

如果连接器无法用 PyRfc 启动，或者显示了与 zip 相关的错误消息，则可能发生此错误。

1. 重新安装 SAP SDK。
1. 验证你的 Linux 64 位版本是否正确。 从当前日期开始，发布文件名为：nwrfc750P_8-70002752.zip。

如果你已手动安装数据连接器，请确保已将 SDK 文件复制到 Docker 容器中。

运行：

```bash
Docker cp SDK by running docker cp nwrfc750P_8-70002752.zip /sapcon-app/inst/
```

### <a name="abap-runtime-errors-appear-on-a-large-system"></a>大型系统上出现 ABAP 运行时错误

如果在大型系统上出现 ABAP 运行时错误，请尝试缩小区域大小：

1. 编辑 sapcon/SID/systemconfig.ini 文件并定义 `timechunk = 5`。
2. [重置 SAP 数据连接器](#reset-the-sap-data-connector)。

> [!NOTE]
> timechunk 大小以分钟为单位定义。

### <a name="empty-or-no-audit-log-retrieved-with-no-special-error-messages"></a>空或未检索审核日志，无特殊错误消息

1. 检查是否在 SAP 中启用了审核日志记录。
1. 验证 SM19 或 RSAU_CONFIG 事务 。
1. 根据需要启用任何事件。
1. 验证消息是否到达并存在于 SAP SM20 或 RSAU_READ_LOG 中，连接器日志中不显示任何特殊错误。 


### <a name="incorrect-azure-sentinel-workspace-id-or-key"></a>Azure Sentinel 工作区 ID 或密钥不正确

如果你发现了在[部署脚本](sap-deploy-solution.md#create-key-vault-for-your-sap-credentials)中输入了不正确的工作区 ID 或密钥，请更新 Azure KeyVault 中存储的凭据。

验证 Azure 密钥保管库中的凭据后，重启容器：

```bash
docker restart sapcon-[SID]
```

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>固定配置中的 SAP ABAP 用户凭据不正确

固定配置是指密码直接存储在 systemconfig.ini 配置文件中。

如果凭据不正确，请验证凭据。

使用 base64 加密对用户和密码进行加密。 可以使用联机加密工具（例如 https://www.base64encode.org/ ）加密凭据。

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>密钥保管库中 SAP ABAP 用户凭据不正确

检查凭据，并根据需要通过将正确的值应用于 Azure 密钥保管库中的 ABAPUSER 和 ABAPPASS 值来修复凭据 。

然后重启容器：

```bash
docker restart sapcon-[SID]
```


### <a name="missing-abap-sap-user-permissions"></a>缺失 ABAP（SAP 用户）权限

如果收到了类似于“..缺失后台 RFC 授权..”的错误消息，说明没有恰当应用 SAP 授权和角色。

1. 请确保在[更改请求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)传输过程中，已导入 MSFTSEN/SENTINEL_CONNECTOR 角色并将其应用到连接器用户。

1. 使用 SAP 事务 PFCG 运行角色生成和用户比较。

### <a name="missing-data-in-your-workbooks-or-alerts"></a>工作簿或警告中缺失数据

如果发现在 Azure Sentinel 工作簿或警告中缺失数据，请确保在 SAP 端恰当启用了审核日志策略，并且日志文件中没有错误。 

在此步骤中使用 RSAU_CONFIG_LOG 事务。


### <a name="missing-sap-change-request"></a>SAP 更改请求缺失

如果发现缺少所需的 [SAP 更改请求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)错误，请确保已为系统导入正确的 SAP 更改请求。

有关详细信息，请参阅[配置 IDE](sap-deploy-solution.md#configure-your-sap-system)。

### <a name="network-connectivity-issues"></a>网络连接问题

如果在 SAP 环境或 Azure Sentinel 上遇到网络连接问题，请检查你的网络连接，以确保数据按预期方式流动。

### <a name="other-unexpected-issues"></a>其他意外问题

如果出现本文未列出的意外问题，请尝试执行以下步骤：

- [重置连接器并重新加载日志](#reset-the-sap-data-connector)
- [将连接器升级到](sap-deploy-solution.md#update-your-sap-data-connector)最新版本。

> [!TIP]
> 另外还建议在完成任何大的配置更改后，重置连接器并确保已获得最新升级。

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>检索审核日志失败并出现警告

如果尝试检索审核日志，但未部署[所需的更改请求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)，或使用了较旧/未修补的版本，并且该进程失败并出现警告，请验证是否可以使用下列方法之一来检索 SAP 审核日志：

- 在较旧版本中使用称为 XAL 的兼容性模式
- 使用最近未修补的版本
- 未安装所需的更改请求

尽管系统应在需要时自动切换到兼容模式，但你可能需要手动切换。 手动切换到兼容模式：

1. 在 sapcon/SID 目录中，编辑 systemconfig.ini 文件 

1. 定义：`auditlogforcexal = True`

1. 重启 Docker 容器：

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="sapcontrol-or-java-subsystems-unable-to-connect"></a>SAPCONTROL 或 JAVA 子系统无法连接

请检查 OS 用户是否有效，并且是否可以在目标 SAP 系统上运行以下命令：

```bash
sapcontrol -nr <SID> -function GetSystemInstanceList
```

### <a name="sapcontrol-or-java-subsystem-fails-with-timezone-related-error-message"></a>SAPCONTROL 或 JAVA 子系统失败，出现时区相关错误消息

如果 SAPCONTROL 或 JAVA 子系统失败，并出现时区相关的错误消息，例如：请检查 SAP 服务器 'Etc/NZST' 的配置和网络访问，请确保使用的是标准时区代码。

例如，使用 `javatz = GMT+12` 或 `abaptz = GMT-3**`。


### <a name="unable-to-import-the-change-request-transports-to-sap"></a>无法将更改请求传输导入到 SAP

如果无法导入[所需的 SAP 日志更改请求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)并且收到有关无效组件版本的错误，请在导入更改请求时添加 `ignore invalid component version`。

### <a name="audit-log-data-not-ingested-past-initial-load"></a>引入初始负载之后未引入审核日志数据

如果在引入初始负载之后，RSAU_READ_LOAD 或 SM200 事务中显示的 SAP 审核日志数据未引入 Azure Sentinel 中，则原因可能是错误配置了 SAP 系统和 SAP 主机操作系统 。

- 初始负载是在全新安装 SAP 数据连接器后或者在删除 metadata.db 文件后引入的。
- 示例错误配置：STZAC 事务中的 SAP 系统时区设置为 CET，但 SAP 主机操作系统时区却设置为 UTC  。

若要检查是否存在错误配置，请在事务 SE38 中运行 RSDBTIME 报告 。 如果发现 SAP 系统与 SAP 主机操作系统之间存在不匹配情况，请执行以下操作：

1. 停止 Docker 容器。 运行

    ```bash
    docker stop sapcon-[SID]
    ```

1.  从 sapcon/[SID] 目录中删除 metadata.db 文件。  运行：

    ```bash
    rm ~/sapcon/[SID]/metadata.db
    ```

1. 更新 SAP 系统和 SAP 主机操作系统，使设置匹配，例如使用相同的时区。 有关详细信息，请参阅 [SAP 社区 Wiki](https://wiki.scn.sap.com/wiki/display/Basis/Time+zone+settings%2C+SAP+vs.+OS+level)。

1. 再次启动容器。 运行：

    ```bash
    docker start sapcon-[SID]
    ```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [部署 SAP 连续威胁监视（公共预览版）](sap-deploy-solution.md)
- [Azure Sentinel SAP 解决方案日志参考（公共预览版）](sap-solution-log-reference.md)
- [通过 SNC 部署 Azure Sentinel SAP 数据连接器](sap-solution-deploy-snc.md)
- [专家配置选项、本地部署和 SAPControl 日志源](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 解决方案：安全内容参考（公共预览版）](sap-solution-security-content.md)
- [Azure Sentinel SAP 解决方案的详细 SAP 要求（公共预览版）](sap-solution-detailed-requirements.md)
