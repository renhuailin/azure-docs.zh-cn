---
title: Azure Sentinel SAP 解决方案 - 可用日志参考 | Microsoft Docs
description: 了解 Azure Sentinel SAP 解决方案中提供的 SAP 日志。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 05/12/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 42cd84387d1b5b67a09afcc072c897d6980b3d49
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109815175"
---
# <a name="azure-sentinel-sap-solution-logs-reference-public-preview"></a>Azure Sentinel SAP 解决方案日志参考（公共预览版）

本文介绍 Azure Sentinel SAP 数据连接器中提供的 SAP 日志，包括 Azure Sentinel 中的表名称、日志用途和详细的日志架构。 架构字段说明基于相关 [SAP 文档](https://help.sap.com/) 中的字段说明。

本文适用于高级 SAP 用户。

> [!NOTE]
> 使用 XBP 3.0 接口时，Azure Sentinel SAP 解决方案使用“未发布”服务。 这些服务不影响后端系统或连接器行为。
>
> 若要“发布”这些服务，请实现 [SAP 说明 2910263 - 未发布的 XBP 函数](https://launchpad.support.sap.com/#/notes/2910263)。

> [!IMPORTANT]
> Azure Sentinel SAP 解决方案目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="abap-application-log"></a>ABAP 应用程序日志

- “Azure Sentinel 中的名称”：`ABAPAppLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcc9f36611d3a6510000e835363f.html)

- “日志用途”：记录应用程序执行的进度，以便以后根据需要重新构造。

    可通过将 RFC 与基于 XBP 接口的标准服务的自定义服务一起使用获得。


### <a name="abapapplog_cl-log-schema"></a>ABAPAppLog_CL 日志架构

| 字段                 | 说明                    |
| --------------------- | ------------------------------ |
| AppLogDateTime        | 应用程序日志日期时间      |
| CallbackProgram       | 回调程序               |
| CallbackRoutine       | 回调例程               |
| CallbackType          | 回调类型                  |
| ClientID              | ABAP 客户端 ID (MANDT)         |
| ContextDDIC           | 上下文 DDIC 结构         |
| ExternalID            | 外部日志 ID                |
| 主机                  | 主机                           |
| 实例              | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>`              |
| InternalMessageSerial | 应用程序日志消息串行 |
| LevelofDetail         | 详细级别                |
| LogHandle             | 应用程序日志句柄         |
| LogNumber             | 日志编号                     |
| MessageClass          | Message 类                  |
| MessageNumber         | 消息号                 |
| MessageText           | 消息正文                   |
| MessageType           | 消息类型                   |
| 对象                | 应用程序日志对象         |
| OperationMode         | 操作模式                 |
| ProblemClass          | 问题类                  |
| programName           | 程序名                   |
| SortCriterion         | 排序条件                 |
| StandardText          | 标准文本                  |
| SubObject             | 应用程序日志子对象     |
| SystemID              | 系统 ID                      |
| SystemNumber          | 系统编号                  |
| TransactionCode       | 事务代码               |
| 用户                  | 用户                           |
| UserChange            | 用户更改                    |
| | |



## <a name="abap-change-documents-log"></a>ABAP 更改文档日志

- “Azure Sentinel 中的名称”：`ABAPChangeDocsLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/6f51f5216c4b10149358d088a0b7029c/7.01.22/en-US/b8686150ed102f1ae10000000a44176f.html)

- “日志用途”：记录：

    - 对业务数据对象的 SAP NetWeaver 应用程序服务器 (AS) ABAP 日志更改于更改文档中。

    - SAP 系统中的其他实体，例如用户数据、角色、地址。

    可通过将 RFC 与基于标准服务的自定义服务一起使用获得。

### <a name="abapauditlog_cl-log-schema"></a>ABAPAuditLog_CL 日志架构


| 字段                    | 说明                 |
| ------------------------ | ---------------------------- |
| ActualChangeNum          | 实际更改编号         |
| ChangedTableKey          | 已更改表键            |
| ChangeNumber             | 更改编号                |
| ClientID                 | ABAP 客户端 ID (MANDT)       |
| CreatedfromPlannedChange | 从计划的更改创建，采用以下语法：`(‘X’ , ‘ ‘)`|
| CurrencyKeyNew           | 货币键：新值      |
| CurrencyKeyOld           | 货币键：旧值      |
| FieldName                | 字段名称                   |
| FlagText                 | 标志文本                    |
| 主机                     | 主机                         |
| 实例                 | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| 语言                 | 语言                     |
| ObjectClass              | 对象类，如 `BELEG`、`BPAR`、`PFCG`、`IDENTITY` |
| ObjectID                 | 对象 ID  |
| PlannedChangeNum         | 计划的更改编号  |
| SystemID                 | 系统 ID    |
| SystemNumber             | 系统编号     |
| TableName                | 表名称        |
| TransactionCode          | 事务代码   |
| TypeofChange_Header      | 更改标头类型，包括： <br>`U` = 更改；`I` = 插入；`E` = 删除单个文档；`D` = 删除；`J` = 插入单个文档 |
| TypeofChange_Item        | 更改项目类型，包括： <br>`U` = 更改；`I` = 插入；`E` = 删除单个文档；`D` = 删除；`J` = 插入单个文档 |
| UOMNew                   | 度量单位：新值  |
| UOMOld                   | 度量单位：旧值 |
| 用户                     | 用户  |
| ValueNew                 | 字段内容：新值 |
| ValueOld                 | 字段内容：旧值 |
| 版本                  | 版本          |
| | |

## <a name="abap-cr-log"></a>ABAP CR 日志

- “Azure Sentinel 中的名称”：`ABAPCRLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd5f36611d3a6510000e835363f.html)

- “日志用途”：包括更改和传输系统 (CTS) 日志，包括进行了更改的 Directory 对象和自定义项。

    可通过将 RFC 与基于标准表的自定义服务和标准服务一起使用获得。

> [!NOTE]
> 除了应用程序日志记录、更改文档和表记录外，使用更改和传输系统对生产系统进行的所有更改都记录在 CTS 和 TMS 日志中。
>


### <a name="abapcrlog_cl-log-schema"></a>ABAPCRLog_CL 日志架构

| 字段        | 说明                       |
| ------------ | --------------------------------- |
| 类别     | 类别（工作台，自定义） |
| ClientID     | ABAP 客户端 ID (MANDT)            |
| 说明  | 说明                       |
| 主机         | 主机                              |
| 实例     | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| ObjectName   | 对象名称                       |
| ObjectType   | 对象类型                       |
| “所有者”        | “所有者”                             |
| 请求      | 更改请求                    |
| 状态       | 状态                            |
| SystemID     | 系统 ID                         |
| SystemNumber | 系统编号                     |
| TableKey     | 表键                         |
| TableName    | 表名称                        |
| 视图名     | 视图名称                         |
| | |

## <a name="abap-db-table-data-log"></a>ABAP DB 表数据日志

- “Azure Sentinel 中的名称”：`ABAPTableDataLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd2f36611d3a6510000e835363f.html)

- “日志用途”：为那些对审核至关重要或容易受到审核的表提供日志记录。

    可通过将 RFC 与自定义服务一起使用获得。

### <a name="abaptabledatalog_cl-log-schema"></a>ABAPTableDataLog_CL 日志架构

| 字段            | 说明                           |
| ---------------- | ------------------------------------- |
| DBLogID          | DB 日志 ID                             |
| 主机             | 主机                                  |
| 实例         | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| 语言         | 语言                              |
| LogKey           | 日志键                               |
| NewValue         | 字段新值                       |
| OldValue         | 字段旧值                       |
| OperationTypeSQL | 操作类型，`Insert`、`Update`、`Delete` |
| 节目          | 程序名                          |
| SystemID         | 系统 ID                             |
| SystemNumber     | 系统编号                         |
| TableField       | 表字段                           |
| TableName        | 表名称                            |
| TransactionCode  | 事务代码                      |
| UserName         | 用户                                  |
| VersionNumber    | 版本号                        |
| | |

## <a name="abap-gateway-log"></a>ABAP 网关日志

- “Azure Sentinel 中的名称”：`GW_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.5.7/en-US/48b2a710ca1c3079e10000000a42189b.html)

- “日志用途”：监视网关活动。 可通过 SAP 控制 Web 服务获得。

### <a name="gw_cl-log-schema"></a>GW_CL 日志架构

| 字段        | 描述      |
| ------------ | ---------------- |
| 主机         | 主机             |
| 实例     | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | 消息正文     |
| 严重性     | 消息严重性：`Debug`、`Info`、`Warning`、`Error`  |
| SystemID     | 系统 ID        |
| SystemNumber | 系统编号    |
| | |

## <a name="abap-icm-log"></a>ABAP ICM 日志

- “Azure Sentinel 中的名称”：`ICM_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/683d6a1797a34730a6e005d1e8de6f22/7.52.4/en-US/a10ec40d01e740b58d0a5231736c434e.html)

- “日志目的”：记录入站和出站请求，并编译 HTTP 请求的统计信息。

    可通过 SAP 控制 Web 服务获得。

### <a name="icm_cl-log-schema"></a>ICM_CL 日志架构

| 字段        | 描述      |
| ------------ | ---------------- |
| 主机         | 主机             |
| 实例     | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | 消息正文     |
| 严重性     | 消息严重性，包括：`Debug`、`Info`、`Warning`、`Error`   |
| SystemID     | 系统 ID        |
| SystemNumber | 系统编号    |
| | |

## <a name="abap-job-log"></a>ABAP 作业日志

- “Azure Sentinel 中的名称”：`ABAPJobLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/b07e7195f03f438b8e7ed273099d74f3/7.31.19/en-US/4b2bc0974c594ba2e10000000a42189c.html)

- “日志用途”：合并所有后台处理作业日志 (SM37)。

    可通过将 RFC 与基于 XBP 接口的标准服务的自定义服务一起使用获得。

### <a name="abapjoblog_cl-log-schema"></a>ABAPJobLog_CL 日志架构


| 字段               | 说明                      |
| ------------------- | -------------------------------- |
| ABAPProgram         | ABAP 程序                     |
| BgdEventParameters  | 后台事件参数      |
| BgdProcessingEvent  | 后台处理事件      |
| ClientID            | ABAP 客户端 ID (MANDT)           |
| DynproNumber        | Dynpro 编号                    |
| GUIStatus           | GUI 状态                       |
| 主机                | 主机                             |
| 实例            | ABAP 实例 (HOST_SYSID_SYSNR)，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| JobClassification   | 作业分类               |
| JobCount            | 作业计数                        |
| JobGroup            | 作业组                        |
| JobName             | 作业名称                         |
| JobPriority         | 作业优先级                     |
| MessageClass        | Message 类                    |
| MessageNumber       | 消息号                   |
| MessageText         | 消息正文                     |
| MessageType         | 消息类型                     |
| ReleaseUser         | 作业发布用户                 |
| SchedulingDateTime  | 计划日期时间             |
| StartDateTime       | 开始日期时间                  |
| SystemID            | 系统 ID                        |
| SystemNumber        | 系统编号                    |
| TargetServer        | 目标服务器                    |
| 用户                | 用户                             |
| UserReleaseInstance | ABAP 实例 - 用户发布     |
| WorkProcessID       | 工作进程 ID                  |
| WorkProcessNumber   | 工作进程编号              |
| | |

## <a name="abap-security-audit-log"></a>ABAP 安全审核日志

- “Azure Sentinel 中的名称”：`ABAPAuditLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/280f016edb8049e998237fcbd80558e7/7.5.7/en-US/4d41bec4aa601c86e10000000a42189b.html)

- “日志用途”：记录以下数据：

    - SAP 系统环境的安全相关更改，例如主用户记录的更改
    - 提供更高级别数据的信息，例如成功和失败的登录尝试
    - 启用一系列事件重建的信息，例如成功或失败的事务启动

    可通过使用 RFC XAL/SAL 接口获得。 版本 Basis 7.50 及更高版本支持此功能。

### <a name="abapauditlog_cl-log-schema"></a>ABAPAuditLog_CL 日志架构

| 字段                      | 说明                     |
| -------------------------- | ------------------------------- |
| ABAPProgramName            | 程序名，仅 SAL                    |
| AlertSeverity              | 警报严重性                  |
| AlertSeverityText          | 警报严重性文本，仅 SAL             |
| AlertValue                 | 警报值                     |
| AuditClassID               | 审核类 ID，仅 SAL                 |
| ClientID                   | ABAP 客户端 ID (MANDT)          |
| Computer                   | 用户计算机，仅 SAL                   |
| 电子邮件                      | 用户电子邮件                      |
| 主机                       | 主机                                                   |
| 实例                   | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>`                  |
| MessageClass               | Message 类                   |
| MessageContainerID         | 消息容器 ID，仅 XAL            |
| 消息 ID                  | 消息 ID，如 `‘AU1’,’AU2’…`                     |
| MessageText                | 消息正文                    |
| MonitoringObjectName       | MTE 监视器对象名称，仅 XAL        |
| MonitorShortName           | MTE 监视器短名称，仅 XAL          |
| SAPProcesType              | 系统日志： SAP 进程类型，仅 SAL    |
| B* - 后台处理 |                                 |
| D* - 对话处理     |                                 |
| U* - 更新任务         |                                 |
| SAPWPName                  | 系统日志：工作进程编号，仅 SAL |
| SystemID                   | 系统 ID                       |
| SystemNumber               | 系统编号                   |
| TerminalIPv6               | 用户计算机 IP，仅 SAL |
| TransactionCode            | 事务代码，仅 SAL |
| 用户                       | 用户                            |
| Variable1                  | 消息变量 1              |
| Variable2                  | 消息变量 2              |
| Variable3                  | 消息变量 3              |
| Variable4                  | 消息变量 4              |
| | |

## <a name="abap-spool-log"></a>ABAP Spool 日志

- “Azure Sentinel 中的名称”：`ABAPSpoolLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae791c40f72045e10000000a421937.html)

- “日志用途”：用作 SAP 打印的主日志，包含 spool 请求的历史记录。 (SP01)。

    可通过将 RFC 与基于标准表的自定义服务一起使用获得。

### <a name="abapspoollog_cl-log-schema"></a>ABAPSpoolLog_CL 日志架构

| 字段                               | 说明                                |
| ----------------------------------- | ------------------------------------------ |
| ArchiveStatus                       | 存档状态                             |
| ArchiveType                         | 存档类型                               |
| ArchivingDevice                     | 存档设备                           |
| AutoRereoute                        | 自动重新路由                              |
| ClientID                            | ABAP 客户端 ID (MANDT)                     |
| CountryKey                          | 国家/地区键                                |
| DeleteSpoolRequestAuto              | 自动删除 spool 请求                  |
| DelFlag                             | 删除标志                              |
| 部门                          | 部门                                 |
| DocumentType                        | 文档类型                              |
| ExternalMode                        | 外部模式                              |
| FormatType                          | 格式类型                                |
| 主机                                | 主机                                       |
| 实例                            | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| NumofCopies                         | 份数                           |
| OutputDevice                        | 输出设备                              |
| PrinterLongName                     | 打印机长名称                          |
| PrintImmediately                    | 立即打印                          |
| PrintOSCoverPage                    | 打印 OSCover 页                         |
| PrintSAPCoverPage                   | 打印 SAPCover 页                        |
| 优先级                            | 优先级                                   |
| RecipientofSpoolRequest             | spool 请求的收件人                 |
| SpoolErrorStatus                    | Spool 错误状态                         |
| SpoolRequestCompleted               | spool 请求已完成                    |
| SpoolRequestisALogForAnotherRequest | Spool 请求是另一个请求的日志 |
| SpoolRequestName                    | spool 请求名称                         |
| SpoolRequestNumber                  | spool 请求编号                       |
| SpoolRequestSuffix1                 | Spool 请求后缀 1                      |
| SpoolRequestSuffix2                 | Spool 请求后缀 2                      |
| SpoolRequestTitle                   | spool 请求标题                        |
| SystemID                            | 系统 ID                                  |
| SystemNumber                        | 系统编号                              |
| TelecommunicationsPartner           | 电信合作伙伴                 |
| TelecommunicationsPartnerE          | 电信合作伙伴 E               |
| TemSeGeneralcounter                 | Temse 计数器                              |
| TemseNumAddProtectionRule           | Temse 编号添加保护规则              |
| TemseNumChangeProtectionRule        | Temse 编号更改保护规则           |
| TemseNumDeleteProtectionRule        | Temse 编号删除保护规则           |
| TemSeObjectName                     | Temse 对象名称                          |
| TemSeObjectPart                     | TemSe 对象部分                          |
| TemseReadProtectionRule             | Temse 读取保护规则                 |
| 用户                                | 用户                                       |
| ValueAuthCheck                      | 值身份验证检查                           |
| | |

## <a name="apab-spool-output-log"></a>APAB Spool 输出日志

- “Azure Sentinel 中的名称”：`ABAPSpoolOutputLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae779e40f72045e10000000a421937.html)

- “日志用途”：用作 SAP 打印的主日志，包含 spool 输出请求的历史记录。 (SP02)。

    可通过将 RFC 与基于标准表的自定义服务一起使用获得。

### <a name="abapspooloutputlog_cl-log-schema"></a>ABAPSpoolOutputLog_CL 日志架构

| 字段                              | 说明                               |
| ---------------------------------- | ----------------------------------------- |
| AppServer                          | 应用程序服务器                        |
| ClientID                           | ABAP 客户端 ID (MANDT)                    |
| 注释                            | 注释                                   |
| CopyCount                          | 复制计数                                |
| CopyCounter                        | 复制计数器                              |
| 部门                         | 部门                                |
| ErrorSpoolRequestNumber            | 错误请求编号                      |
| FormatType                         | 格式类型                               |
| 主机                               | 主机                                      |
| HostName                           | 主机名                                 |
| HostSpoolerID                      | 主机 spooler ID                          |
| 实例                           | ABAP 实例                             |
| LastPage                           | 最后一页                                 |
| NumofCopies                        | 份数                              |
| OutputDevice                       | 输出设备                             |
| OutputRequestNumber                | 输出请求编号                     |
| OutputRequestStatus                | 获取请求状态                     |
| PhysicalFormatType                 | 物理格式类型                      |
| PrinterLongName                    | 打印机长名称                         |
| PrintRequestSize                   | 打印请求大小                        |
| 优先级                           | 优先级                                  |
| ReasonforOutputRequest             | 输出请求的原因                 |
| RecipientofSpoolRequest            | spool 请求的收件人                 |
| SpoolNumberofOutputReqProcessed    | 输出请求数 - 已处理     |
| SpoolNumberofOutputReqWithErrors   | 输出请求数 - 有错误   |
| SpoolNumberofOutputReqWithProblems | 输出请求数 - 有问题 |
| SpoolRequestNumber                 | spool 请求编号                      |
| StartPage                          | 起始页                                |
| SystemID                           | 系统 ID                                 |
| SystemNumber                       | 系统编号                             |
| TelecommunicationsPartner          | 电信合作伙伴                |
| TemSeGeneralcounter                | Temse 计数器                             |
| Title                              | Title                                     |
| 用户                               | 用户                                      |
| | |


## <a name="abap-syslog"></a>ABAP SysLog

- “Azure Sentinel 中的名称”：`SysLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcbaf36611d3a6510000e835363f.html)

- “日志用途”：记录所有 SAP NetWeaver 应用程序服务器 (SAP NetWeaver AS) ABAP 系统错误、警告、由于已知用户的登录尝试失败而锁定的用户锁、进程消息。

    可通过 SAP 控制 Web 服务获得。

### <a name="syslog_cl-log-schema"></a>SysLog_CL 日志架构


| 字段            | 说明            |
| ---------------- | ---------------------- |
| ClientID         | ABAP 客户端 ID (MANDT) |
| 主机             | 主机                   |
| 实例         | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR> ` |
| MessageNumber    | 消息号         |
| MessageText      | 消息正文           |
| 严重性         | 消息严重性，以下值之一：`Debug`、`Info`、`Warning`、`Error`        |
| SystemID         | 系统 ID              |
| SystemNumber     | 系统编号          |
| TransacationCode | 事务代码       |
| 类型             | SAP 进程类型       |
| 用户             | 用户                   |
| | |


## <a name="abap-workflow-log"></a>ABAP 工作流日志

- “Azure Sentinel 中的名称”：`ABAPWorkflowLog_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcccf36611d3a6510000e835363f.html)

- “日志用途”：使用 SAP Business Workflow (WebFlow Engine)，可以定义尚未映射到 SAP 系统中的业务进程。

    例如，未映射的业务进程可能是简单的发布或审批过程，或者是更复杂的业务进程，如创建基本材料，然后协调关联的部门。

    可通过将 RFC 与基于标准表的自定义服务和标准服务一起使用获得。

### <a name="abapworkflowlog_cl-log-schema"></a>ABAPWorkflowLog_CL 日志架构


| 字段               | 说明                      |
| ------------------- | -------------------------------- |
| ActualAgent         | 实际代理                     |
| 地址             | 地址                          |
| ApplicationArea     | 应用程序区域                 |
| CallbackFunction    | 回调函数                |
| ClientID            | ABAP 客户端 ID (MANDT)           |
| CreationDateTime    | 创建日期时间               |
| 创建者             | 创建者                          |
| CreatorAddress      | 创建者地址                  |
| ErrorType           | 错误类型                       |
| ExceptionforMethod  | 方法的异常             |
| 主机                | 主机                             |
| 实例            | ABAP 实例 (HOST_SYSID_SYSNR)，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| 语言            | 语言                         |
| LogCounter          | 日志计数器                      |
| MessageNumber       | 消息号                   |
| MessageType         | 消息类型                     |
| MethodUser          | 方法用户                      |
| 优先级            | 优先级                         |
| SimpleContainer     | 简单容器，打包为工作项键值实体列表 |
| 状态              | 状态                           |
| SuperWI             | 超级 WI                         |
| SystemID            | 系统 ID                        |
| SystemNumber        | 系统编号                    |
| TaskID              | 任务 ID                          |
| TasksClassification | 任务分类            |
| TaskText            | 任务文本                        |
| TopTaskID           | 最高任务 ID                      |
| UserCreated         | 创建者用户                     |
| WIText              | 工作项文本                   |
| WIType              | 工作项类型                   |
| WorkflowAction      | Workflow 操作                  |
| WorkItemID          | 工作项 ID                     |
| | |





## <a name="abap-workprocess-log"></a>ABAP WorkProcess 日志

- “Azure Sentinel 中的名称”：`WP_CL`

- “相关 SAP 文档”：[SAP 帮助门户](https://help.sap.com/viewer/d0739d980ecf42ae9f3b4c19e21a4b6e/7.3.15/en-US/46fb763b6d4c5515e10000000a1553f6.html)

- “日志用途”：合并所有工作进程日志。 （默认：`dev_*`）。

    可通过 SAP 控制 Web 服务获得。

### <a name="wp_cl-log-schema"></a>WP_CL 日志架构


| 字段        | 描述         |
| ------------ | ------------------- |
| 主机         | 主机                |
| 实例     | ABAP 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | 消息正文     |
| 严重性     | 消息严重性：`Debug`、`Info`、`Warning`、`Error`  |
| SystemID     | 系统 ID           |
| SystemNumber | 系统编号       |
| WPNumber     | 工作进程编号 |
| | |


## <a name="hana-db-audit-trail"></a>HANA DB 审核线索

- “Azure Sentinel 中的名称”：`Syslog`

- “相关 SAP 文档”：[常规](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/48fd6586304c4f859bf92d64d0cd8b08.html)  |   [审核线索](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US/0a57444d217649bf94a19c0b68b470cc.html)

- “日志用途”：记录 SAP HANA 数据库中的用户操作或尝试的操作。 例如，能够实现记录和监视对敏感数据的读取访问。

    可通过 Syslog 的 Sentinel Linux 代理获得。

### <a name="syslog-log-schema"></a>Syslog 日志架构

| 字段         | 说明  |
| ------------- | ------------ |
| Computer      | 主机名    |
| HostIP        | 主机 IP      |
| HostName      | 主机名    |
| ProcessID     | 进程 ID   |
| ProcessName   | 进程名称：`HDB*` |
| SeverityLevel | 警报        |
| SourceSystem  |   源系统 OS，`Linux`           |
| SyslogMessage | 消息，未分析的审核线索消息      |
| | |

## <a name="java-files"></a>JAVA 文件

- “Azure Sentinel 中的名称”：`JavaFilesLogsCL`

- “相关 SAP 文档”：[常规](https://help.sap.com/viewer/2f8b1599655d4544a3d9c6d1a9b6546b/7.5.9/en-US/485059dfe31672d4e10000000a42189c.html)  |  [Java 安全审核日志](https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/7.5.9/en-US/4b6013583840584ae10000000a42189c.html)

- “日志用途”：合并所有基于 Java 文件的日志，包括安全审核日志和系统（群集和服务器进程）、性能和网关日志。 还包括开发人员跟踪和默认跟踪日志。

    可通过 SAP 控制 Web 服务获得。

### <a name="javafileslogscl-log-schema"></a>JavaFilesLogsCL 日志架构


| 字段            | 说明          |
| ---------------- | -------------------- |
| 应用程序      | Java 应用程序     |
| ClientID         | 客户端 ID           |
| CSNComponent     | CSN 组件，例如 `BC-XI-IBD` |
| DCComponent      | DC 组件，例如 `com.sap.xi.util.misc` |
| DSRCounter       | DSR 计数器          |
| DSRRootContentID | DSR 上下文 GUID     |
| DSRTransaction   | DSR 事务 GUID |
| 主机             | 主机                 |
| 实例         | Java 实例，采用以下语法：`<HOST>_<SYSID>_<SYSNR>` |
| 位置         | Java 类           |
| LogName          | Java logName，例如：`Available`、`defaulttrace`、`dev*`、`security` 等
| MessageText      | 消息正文         |
| MNo              | 消息号       |
| Pid              | 进程 ID           |
| 节目          | 程序名         |
| 会话          | 会话              |
| 严重性         | 消息严重性，包括：`Debug`、`Info`、`Warning`、`Error`     |
| 解决方案         | 解决方案             |
| SystemID         | 系统 ID            |
| SystemNumber     | 系统编号        |
| ThreadName       | 线程名          |
| 引发           | 引发异常     |
| TimeZone         | 时区             |
| 用户             | 用户                 |
| | |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [教程：为 SAP 部署 Azure Sentinel 解决方案](sap-deploy-solution.md)
- [Azure Sentinel SAP 解决方案详细的 SAP 要求](sap-solution-detailed-requirements.md)
- [在本地部署 Azure Sentinel SAP 数据连接器](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 解决方案：内置安全内容](sap-solution-security-content.md)