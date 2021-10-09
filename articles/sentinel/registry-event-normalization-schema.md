---
title: Azure Sentinel 注册表事件规范化架构参考 | Microsoft Docs
description: 本文介绍 Azure Sentinel 注册表事件规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/01/2021
ms.author: bagol
ms.openlocfilehash: d10d1e9408db7ab29a7fe01e5bf906e9023124c7
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407221"
---
# <a name="azure-sentinel-registry-event-normalization-schema-reference-public-preview"></a>Azure Sentinel 注册表事件规范化架构参考（公共预览版）

注册表事件架构用于描述创建、修改或删除 Windows 注册表实体的 Windows 活动。

注册表事件特定于 Windows 系统，但由监视 Windows 的不同系统报告，例如 EDR（终结点检测和响应）系统、Sysmon 或 Windows 本身。

有关 Azure Sentinel 中的规范化的详细信息，请参阅[规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)。

> [!IMPORTANT]
> 注册表事件规范化架构目前为预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负荷。
>
> [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="parsers"></a>分析器

Azure Sentinel 提供以下内置的特定于产品的注册表事件分析器：

- 使用 Log Analytics 代理或 Azure Monitor 代理收集的安全事件注册表更新（事件 4657）
- 使用 Log Analytics 代理或 Azure Monitor 代理收集的 Sysmon 注册表监视事件（事件 12、13 和 14）
- Microsoft 365 Defender for Endpoints 注册表事件

若要使用统一了所有内置分析器的与源无关的分析器，并确保针对所有配置的源中运行分析，请在查询中使用“imRegistry”作为表名称。

从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelRegistry)部署[与源无关的分析器和特定于源的分析器](normalization-about-parsers.md)。

### <a name="add-your-own-normalized-parsers"></a>添加自己的规范化分析器

为注册表事件信息模型实现自定义分析器时，请使用以下语法来命名 KQL 函数：`imRegistry<vendor><Product>`。

将 KQL 函数添加到与源无关的分析器 `imRegistry`，以确保使用注册表事件模型的任何内容也使用你的新分析器。

## <a name="normalized-content"></a>规范化内容

Azure Sentinel 提供了 [通过 IFEO 注册表项持久化](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml) 的搜寻查询。 此查询适用于使用 Azure Sentinel 信息模型规范化的任何注册表活动数据。

有关详细信息，请参阅[通过 Azure Sentinel 搜寻威胁](hunting.md)。

## <a name="schema-details"></a>架构详细信息

注册表事件信息模型与 [OSSEM 注册表实体架构](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/registry.md)保持一致。

### <a name="log-analytics-fields"></a>Log Analytics 字段


Log Analytics 针对每条记录生成以下字段，在创建自定义连接器时可以重写这些字段。

| 字段         | 类型     | 讨论 (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>“TimeGenerated” | datetime | 报告设备生成事件的时间。|
| “_ResourceId”   | guid     | 报告设备或服务的 Azure 资源 ID，或使用 Syslog、CEF 或 WEF 转发的事件的日志转发器资源 ID。 |
| **类型** | String | 从中提取记录的原始表。 当同一事件可通过多个通道传入不同的表，并且具有相同的 EventVendor 和 EventProduct 值时，此字段很有用。<br><br>例如，Sysmon 事件可以传入 Event 表或 WindowsEvent 表。 |


> [!NOTE]
> Log Analytics 还会添加与安全用例不太相关的其他字段。 有关详细信息，请参阅 [Azure Monitor 日志中的标准列](../azure-monitor/logs/log-standard-columns.md)。
>

### <a name="event-fields"></a>事件字段


事件字段通用于所有架构，描述活动本身和报告设备。

| 字段               | 类       | 类型       |  说明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 可选    | 字符串     |     一般消息或说明，包含在记录中或者根据记录生成。   |
| “EventCount”          | 必需   | Integer    |     记录描述的事件数。 <br><br>当源支持聚合且单个记录可以表示多个事件时，将使用此值。 <br><br>对于其他源，设置为 `1`。   |
| **EventStartTime**      | 必需   | 日期/时间  |      如果源支持聚合且记录表示多个事件，则此字段将指定生成第一个事件的时间。 <br><br>否则，此字段将别名化 [TimeGenerated](#timegenerated) 字段。 |
| **EventEndTime**        | 必需   | Alias      |      [TimeGenerated](#timegenerated) 字段的别名。    |
| **EventType**           | 必需   | Enumerated |    描述记录报告的操作。 <br><br>对于注册表记录，支持的值包括： <br>- `RegistryKeyCreated` <br>- `RegistryKeyDeleted`<br>- `RegistryKeyRenamed` <br>- `RegistryValueDeleted` <br>- `RegistryValueSet`|
| **EventOriginalUid**    | 可选    | 字符串     |   原始记录的唯一 ID（如果已由源提供）。<br><br>示例： `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| “EventOriginalType”   | 可选    | 字符串     |   原始事件类型或 ID（如果已由源提供）。<br><br>示例： `4657`|
| <a name ="eventproduct"></a>“EventProduct”        | 必需   | 字符串     |             生成事件的产品。 <br><br>示例： `Sysmon`<br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。           |
| **EventProductVersion** | 可选    | 字符串     | 生成事件的产品的版本。 <br><br>示例： `12.1`      |
| **EventVendor**         | 必需   | 字符串     |           生成事件的产品的供应商。 <br><br>示例： `Microsoft`  <br><br>注意：此字段在源记录中可能不可用。 在这种情况下，此字段必须由分析器设置。  |
| **EventSchemaVersion**  | 必需   | 字符串     |    架构的版本。 此处记录的架构版本为 `0.1`         |
| **EventReportUrl**      | 可选    | 字符串     | 在资源的事件中提供的 URL，提供有关该事件的其他信息。|
| “Dvc” | 必需       | 字符串     |               发生该事件的设备的唯一标识符。 <br><br>此字段可以别名化 [DvcId](#dvcid)、[DvcHostname](#dvchostname) 或 [DvcIpAddr](#dvcipaddr) 字段。 对于没有明确的设备的云源，请使用与 [EventProduct](#eventproduct) 字段相同的值。         |
| <a name ="dvcipaddr"></a>DvcIpAddr           | 建议 | IP 地址 |         发生注册表事件的设备的 IP 地址。  <br><br>示例： `45.21.42.12`    |
| <a name ="dvchostname"></a>“DvcHostname”         | 建议 | 主机名   |               发生注册表事件的设备的主机名。 <br><br>示例： `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>“DvcId”               | 可选    | 字符串     |  发生注册表事件的设备的唯一 ID。 <br><br>示例： `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 可选    | MAC        |   发生注册表事件的设备的 MAC。  <br><br>示例： `00:1B:44:11:3A:B7`       |
| “DvcOs”               | 可选    | 字符串     |         发生注册表事件的设备上运行的操作系统。    <br><br>示例： `Windows`    |
| “DvcOsVersion”        | 可选    | 字符串     |   发生注册表事件的设备上的操作系统版本。 <br><br>示例： `10` |
| **AdditionalFields**    | 可选    | 动态    | 如果源提供了值得保留的其他信息，请使用原始字段名称保留这些信息，或者创建动态“AdditionalFields”字段，并在其中以键/值对的形式添加这些额外信息。    |


### <a name="registry-event-specific-fields"></a>特定于注册表事件的字段

下表中列出的字段特定于注册表事件，但类似于其他架构中的字段，并遵循类似的命名约定。

有关详细信息，请参阅 Windows 文档中的[注册表结构](/windows/win32/sysinfo/structure-of-the-registry)。

| 字段          | 类        | 类型       | 说明   |
|---------------|--------------|------------|-----------------|
|<a name="registrykey"></a>**RegistryKey**     |     必需    |   字符串      |与操作关联的注册表项，规范化为标准根密钥命名约定。 有关详细信息，请参阅[根密钥](#root-keys)。<br><br>注册表项类似于文件系统中的文件夹。 <br><br>例如：`HKEY_LOCAL_MACHINE\SOFTWARE\MTG`        |
|**RegistryValue**     |    建议     |  字符串       |与操作关联的注册表值。 注册表值类似于文件系统中的文件。 <br><br>例如：`Path`        |
|<a name="registryvaluetype"></a>**RegistryValueType**     |    建议     |    字符串     | 注册表值的类型，规范化为标准格式。 有关更多信息，请参阅[值类型](#value-types)。<br><br>例如：`Reg_Expand_Sz`        |
|**RegistryValueData**     | 建议       |      字符串   |  存储在注册表值中的数据。 <br><br>示例： `C:\Windows\system32;C:\Windows;`       |
|<a name="registrypreviouskey"></a>**RegistryPreviousKey**     |  建议       |   字符串      |  对于修改注册表的操作，原始注册表项规范化为标准根项命名。 有关详细信息，请参阅[根密钥](#root-keys)。 <br><br>**注意**：如果操作更改了其他字段（例如值），但项保持不变，则 [RegistryPreviousKey](#registrypreviouskey) 将具有与 [RegistryKey](#registrykey) 相同的值。<br><br>示例： `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`       |
|<a name="registrypreviousvalue"></a>**RegistryPreviousValue**     | 建议        | 字符串        | 对于修改注册表的操作，原始值类型规范化为标准格式。 有关更多信息，请参阅[值类型](#value-types)。 <br><br>如果类型未更改，则此字段具有与 [RegistryValueType](#registryvaluetype) 字段相同的值。  <br><br>示例： `Path`       |
|**RegistryPreviousValueType**     | 建议        |   字符串      |用于修改注册表的操作，为原始值类型。 <br><br>如果类型未更改，则此字段将具有与 [RegistryValueType](#registryvaluetype) 字段相同的值，规范化为标准格式。 有关更多信息，请参阅[值类型](#value-types)。<br><br>示例： `Reg_Expand_Sz`         |
|**RegistryPreviousValueData**     | 建议        |   字符串      |原始注册表数据，用于修改注册表的操作。 <br><br>示例：`C:\Windows\system32;C:\Windows;`         |
|**用户** | Alias | |[ActorUsername](#actorusername) 字段的别名。 <br><br>示例： `CONTOSO\ dadmin` |
|**处理**     |  Alias       |         |  [ActingProcessName](#actingprocessname) 字段的别名。<br><br>示例： `C:\Windows\System32\rundll32.exe`       |
| <a name="actorusername"></a>ActorUsername  | 必需    | 字符串     | 发起事件的用户的用户名。 <br><br>示例： `CONTOSO\WIN-GG82ULGC9GO$`     |
| “ActorUsernameType”              | 必需    | Enumerated |   指定 [ActorUsername](#actorusername) 字段中存储的用户名的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。 <br><br>示例： `Windows`       |
| <a name="actoruserid"></a>“ActorUserId”    | 建议  | 字符串     |   Actor 的唯一 ID。 特定的 ID 取决于生成事件的系统。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。  <br><br>示例： `S-1-5-18`    |
| “ActorUserIdType”| 建议  | 字符串     |  [ActorUserId](#actoruserid) 字段中存储的 ID 的类型。 有关详细信息，请参阅[用户实体](normalization-about-schemas.md#the-user-entity)。 <br><br>示例： `SID`         |
| “ActorSessionId” | 可选     | 字符串     |   Actor 登录会话的唯一 ID。  <br><br>示例： `999`<br><br>注意：类型定义为字符串以支持不同的系统，但在 Windows 上，此值必须是数字。 如果你使用的是 Windows 计算机并且源发送其他类型，请务必转换值。 例如，如果源发送的是十六进制值，请将其转换为十进制值。   |
| <a name="actingprocessname"></a>ActingProcessName              | 可选     | 字符串     |   操作进程映像文件的文件名。 此名称通常被视为进程名称。  <br><br>示例： `C:\Windows\explorer.exe`  |
| **ActingProcessId**| 必需    | 字符串        | 操作进程的进程 ID (PID)。<br><br>示例：`48610176`           <br><br>注意：类型定义为“字符串”以支持不同的系统，但在 Windows 和 Linux 上，此值必须是数字。 <br><br>如果使用的是 Windows 或 Linux 计算机并使用了其他类型，请务必转换值。 例如，如果使用了十六进制值，请将其转换为十进制值。    |
| “ActingProcessGuid”              | 可选     | 字符串     |  为操作进程生成的唯一标识符 (GUID)。   <br><br> 示例： `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ParentProcessName**              | 可选     | 字符串     |  父进程映像文件的文件名。 此值通常被视为进程名称。    <br><br>示例： `C:\Windows\explorer.exe` |
| **ParentProcessId**| 必需    | 字符串    | 父进程的进程 ID (PID)。   <br><br>     示例：`48610176`    |
| “ParentProcessGuid”              | 可选     | 字符串     |  父进程的生成的唯一标识符 (GUID)。     <br><br> 示例： `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |



### <a name="root-keys"></a>根项

不同的源使用不同的表示形式表示注册表项前缀。 对于 [RegistryKey](#registrykey) 和 [RegistryPreviousKey](#registrypreviouskey) 字段，请使用以下规范化前缀：

|规范化密钥前缀  |其他常见表示形式  |
|---------|---------|
|HKEY_LOCAL_MACHINE      | `HKLM`, `\REGISTRY\MACHINE`        |
|**HKEY_USERS**     | `HKU`, `\REGISTRY\USER`        |


### <a name="value-types"></a>值类型

不同的源使用不同的表示形式表示注册表值类型。 对于 [RegistryValueType](#registryvaluetype) 和 [RegistryPreviousValueType](#registrypreviousvalue) 字段，请使用以下规范化类型：


|规范化密钥前缀  |其他常见表示形式  |
|---------|---------|
|  **Reg_None**   |    `None`, `%%1872`     |
|  **Reg_Sz**   |     `String`, `%%1873`    |
| **Reg_Expand_Sz**    | `ExpandString`, `%%1874`        |
|  **Reg_Binary**   |   `Binary`, `%%1875`      |
| **Reg_DWord**    |    `Dword`, `%%1876`     |
|  **Reg_Multi_Sz**   |  `MultiString`, `%%1879`       |
|  **Reg_QWord**   |    `Qword`, `%%1883`     |



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 中的规范化](normalization.md)
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考](dns-normalization-schema.md)
- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [Azure Sentinel 网络规范化架构参考](normalization-schema.md)
