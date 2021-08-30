---
title: Azure Sentinel 实体类型引用 |Microsoft Docs
description: 本文显示 Azure Sentinel 实体类型及其必需的标识符。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 5b1fc91868f420b2f156fbb4d219d08cf5ad2ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726059"
---
# <a name="azure-sentinel-entity-types-reference"></a>Azure Sentinel 实体类型引用

## <a name="entity-types-and-identifiers"></a>实体类型和标识符

下表显示了当前可用于在 Azure Sentinel 中映射的“实体类型”，并显示了可用作每个实体类型的“标识符”的属性，这些属性显示在[分析规则向导](detect-threats-custom.md)的[实体映射](map-data-fields-to-entities.md)部分的“标识符”下拉列表中   。

“必需的标识符”列中的每个标识符都必须至少用于标识其实体。 但是，必需的标识符本身可能并不足以提供唯一标识。 使用的标识符越多，唯一标识的可能性就越大。 单个实体映射最多可以使用三个标识符。

为了获得最佳结果 - 确保唯一标识 - 应尽可能使用“最强标识符”列中的标识符。 使用多个强标识符可实现不同数据源和架构中强标识符之间的相关性。 这反过来允许 Azure Sentinel 为给定实体提供更全面的见解。

| 实体类型 | 标识符 | 必需的标识符 | 最强标识符 |
| - | - | - | - |
| [**用户帐户**](#user-account)<br>*（账户）* | 名称<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>名称<br>AadUserId<br>PUID<br>ObjectGuid | 名称 + NTDomain<br>名称 + UPNSuffix<br>AADUserId<br>Sid |
| [**主机**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | HostName + NTDomain<br>HostName + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP 地址**](#ip-address)<br>*(IP)* | 地址 | 地址 | |
| [**恶意软件**](#malware) | 名称<br>类别 | 名称 | |
| [**文件**](#file) | 目录<br>名称 | 名称 | |
| [**进程**](#process) | ProcessId<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ProcessId | |
| [**云应用程序**](#cloud-application)<br>*(CloudApplication)* | AppId<br>名称<br>InstanceName | AppId<br>名称 | |
| [**域名**](#domain-name)<br>*(DNS)* | DomainName | DomainName | |
| [**Azure 资源**](#azure-resource) | ResourceId | ResourceId | |
| [**文件哈希**](#file-hash)<br>*(FileHash)* | 算法<br>“值” | Algorithm + 值 | |
| [**注册表项**](#registry-key) | Hive<br>密钥 | Hive<br>密钥 | Hive + 项 |
| [**注册表值**](#registry-value) | 名称<br>“值”<br>ValueType | 名称 | |
| [**安全组**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | URL | URL | |
| [**IoT 设备**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>源<br>SourceRef<br>制造商<br>建模<br>OperatingSystem<br>IpAddress<br>MacAddress<br>协议<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**邮箱**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**邮件群集**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>威胁<br>查询<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>源<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | 查询<br>源 | 查询 + 源 |
| [**邮件消息**](#mail-message) | Recipient<br>Url<br>威胁<br>发送方<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>使用者<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>语言<br>ThreatDetectionMethods | NetworkMessageId<br>Recipient | NetworkMessageId + 收件人 |
| [**提交邮件**](#submission-mail) | SubmissionId<br>SubmissionDate<br>“提交者”<br>NetworkMessageId<br>时间戳<br>Recipient<br>发送方<br>SenderIp<br>使用者<br>ReportType | SubmissionId<br>NetworkMessageId<br>Recipient<br>“提交者” |  |
|

## <a name="entity-type-schemas"></a>实体类型架构

下面更深入地介绍了每种实体类型的完整架构。 你会注意到，其中许多架构都包含指向其他实体类型的链接 - 例如，用户帐户架构包含指向主机实体类型的链接，因为用户帐户的一个属性是在其上定义的主机。 这些外部链接的实体不能用作实体映射的标识符，但在实体页和调查关系图上提供实体的完整图片时，它们非常有用。

> [!NOTE]
> “类型”列中的值后面的问号指示该字段可为空。

## <a name="user-account"></a>用户帐户

*实体名称：帐户*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “帐户” |
| 名称 | String | 帐户的名称。 此字段应仅包含名称，且未添加任何域。 |
| *FullName* | *空值* | *并非架构的一部分，是为了与向后兼容旧版本的实体映射而包括在内。*
| NTDomain | 字符串 | 以警报格式显示的 NETBIOS 域名 - 域\用户名。 示例：财务、NT 机构 |
| DnsDomain | 字符串 | 完全限定域 DNS 名。 示例：finance.contoso.com |
| UPNSuffix | 字符串 | 帐户的用户主体名称后缀。 在某些情况下，这也是域名。 示例：contoso.com |
| 主机 | 实体 | 包含帐户的主机（如果该帐户是本地帐户）。 |
| Sid | 字符串 | 帐户安全标识符，如 S-1-5-18。 |
| AadTenantId | Guid? | Azure AD 租户 ID（如果已知）。 |
| AadUserId | Guid? | Azure AD 帐户对象 ID（如果已知）。 |
| PUID | Guid? | Azure AD Passport 用户 ID（如果已知）。 |
| IsDomainJoined | Bool? | 确定这是否为域帐户。 |
| DisplayName | 字符串 | 帐户的显示名称。 |
| ObjectGuid | Guid? | ObjectGUID 特性是一个单值属性，它是对象的唯一标识符，由 Active Directory 分配。 |
|

帐户实体的强标识符：

- 名称 + UPNSuffix
- AadUserId
- Sid + 主机（必需内置帐户的 SID）
- Sid（除了内置帐户的 SID）
- 名称 + NTDomain（除非 NTDomain 为内置域，例如“Workgroup”）
- 名称 + 主机（如果 NTDomain 为内置域，例如“Workgroup”）
- 名称 + DnsDomain
- PUID
- ObjectGuid

帐户实体的弱标识符：

- 名称

## <a name="host"></a>主机

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “主机” |
| DnsDomain | 字符串 | 此主机所属的 DNS 域。 应该包含域的完整 DNS 后缀（如果已知）。 |
| NTDomain | 字符串 | 此主机所属的 NT 域。 |
| HostName | 字符串 | 不带域后缀的主机名。 |
| *FullName* | *空值* | *并非架构的一部分，是为了与向后兼容旧版本的实体映射而包括在内。*
| NetBiosName | 字符串 | 主机名（Windows 2000 以前版本）。 |
| IoTDevice | 实体 | IoT 设备实体（如果此主机表示 IoT 设备）。 |
| AzureID | 字符串 | VM 的 Azure 资源 ID （如果已知）。 |
| OMSAgentID | 字符串 | OMS 代理 ID（如果主机安装了 OMS 代理）。 |
| OSFamily | Enum? | 以下值之一： <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | 字符串 | 操作系统的自由文本表示形式。<br>此字段旨在保存特定版本，该版本比 OSFamily 更细化，或者是 OSFamily 枚举不支持的未来值。 |
| IsDomainJoined | Bool | 确定此主机是否属于域。 |
|

主机实体的强标识符：
- HostName + NTDomain
- HostName + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice（实体映射不支持）

主机实体的弱标识符：
- HostName
- NetBiosName

## <a name="ip-address"></a>IP 地址

*实体名称：IP*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “IP” |
| 地址 | String | 字符串形式的 IP 地址，如 127.0.0.1（在 IPv4 或 IPv6）中。 |
| 位置 | GeoLocation | 附加到 IP 实体的地理位置上下文。 <br><br>有关详细信息，另请参阅[通过 REST API 用地理位置数据丰富 Azure Sentinel 中的实体](geolocation-data-api.md)。 |
|

IP 实体的强标识符：
- 地址

## <a name="malware"></a>恶意软件

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “恶意软件” |
| 名称 | String | 由供应商提供的恶意软件名称，如 `Win32/Toga!rfn`。 |
| Category | String | 供应商的恶意软件类别，例如木马病毒。 |
| 文件 | List\<Entity> | 在其中找到了恶意软件的链接文件实体的列表。 可以内联或作为引用包含文件实体。<br>有关结构的更多详细信息，请参阅文件实体。 |
| 进程 | List\<Entity> | 发现恶意软件的链接进程实体的列表。 当警报在无文件活动中触发时，通常会使用它。<br>有关结构的更多详细信息，请参阅[进程](#process)实体。 |
|

恶意软件实体的强标识符：

- 名称 + 类别

## <a name="file"></a>文件

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “文件” |
| 目录 | 字符串 | 文件的完整路径。 |
| 名称 | String | 不带路径的文件名（某些警报可能不包括路径）。 |
| 主机 | 实体 | 存储文件的主机。 |
| FileHashes | 列出&lt;实体&gt; | 与此文件关联的文件哈希。 |
|

文件实体的强标识符：
- 名称 + 目录
- 名称 + FileHash
- 名称 + 目录 + 获取-filehash

## <a name="process"></a>进程

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “进程” |
| ProcessId | 字符串 | 进程 ID。 |
| CommandLine | 字符串 | 用于创建进程的命令行。 |
| ElevationToken | Enum? | 与进程关联的提升标记。<br>可能的值：<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | 进程开始运行的时间。 |
| ImageFile | 实体（文件） | 可以内联或作为引用包含文件实体。<br>有关结构的更多详细信息，请参阅文件实体。 |
| 帐户 | 实体 | 运行进程的帐户。<br>可以内联或作为参考包含[账户](#user-account)实体。<br>有关结构的更多详细信息，请参阅[账户](#user-account)实体。 |
| ParentProcess | 实体（进程） | 父进程实体。 <br>可以包含部分数据，即仅限 PID。 |
| 主机 | 实体 | 运行进程的主机。 |
| LogonSession | 实体 (HostLogonSession) | 运行进程的会话。 |
|

进程实体的强标识符：

- 主机 + ProcessId + CreationTimeUtc
- 主机 + ParentProcessId + CreationTimeUtc + CommandLine
- 主机 + ProcessId + CreationTimeUtc + ImageFile
- 主机 + ProcessId + CreationTimeUtc + ImageFile.FileHash

进程实体的弱标识符：

- ProcessId + CreationTimeUtc + CommandLine（无主机）
- ProcessId + CreationTimeUtc + ImageFile（无主机）

## <a name="cloud-application"></a>云应用程序

*实体名称：CloudApplication*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “云应用程序” |
| AppId | int | 应用程序的技术标识符。 这应该是[云应用程序标识符](#cloud-application-identifiers)列表中定义的值之一。 AppId 字段的值是可选的。 |
| 名称 | String | 相关云应用程序的名称。 应用程序名称的值是可选的。 |
| InstanceName | 字符串 | 云应用程序的用户定义实例名称。 它通常用于区分客户拥有的多个相同类型的应用程序。 |
|

云应用程序实体的强标识符：
 - AppId（无 InstanceName）
 - 名称（无 InstanceName）
 - AppId + InstanceName
 - 名称 + InstanceName

## <a name="domain-name"></a>域名

*实体名称：DNS*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “dns” |
| DomainName | 字符串 | 与警报关联的 DNS 记录的名称。 |
| IpAddress | 列出&lt;实体 (IP)&gt; | 与解析的 IP 地址相对应的实体。 |
| DnsServerIp | 实体 (IP) | 表示 DNS 服务器解析请求的实体。 |
| HostIpAddress | 实体 (IP) | 表示 DNS 请求客户端的实体。 |
|

DNS 实体的强标识符：
- DomainName + DnsServerIp + HostIpAddress

DNS 实体的弱标识符：
- DomainName + HostIpAddress

## <a name="azure-resource"></a>Azure 资源

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “azure-resource” |
| ResourceId | String | 资源的 Azure 资源 ID。 |
| SubscriptionId | 字符串 | 资源的订阅 ID。 |
| TryGetResourceGroup | Bool | 资源组值（如果存在）。 |
| TryGetProvider | Bool | 提供程序值（如果存在）。 |
| TryGetName | Bool | 名称值（如果存在）。 |
|

Azure 资源实体的强标识符：
- ResourceId

## <a name="file-hash"></a>文件哈希

*实体名称：FileHash*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “filehash” |
| 算法 | 枚举 | 哈希算法类型。 可能的值：<li>未知<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| “值” | 字符串 | 哈希值。 |
|

文件哈希实体的强标识符：
- Algorithm + 值

## <a name="registry-key"></a>注册表项

*实体名称：RegistryKey*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “registry-key” |
| Hive | Enum? | 以下值之一：<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| 密钥 | 字符串 | 注册表项路径。 |
|

注册表项实体的强标识符：
- Hive + 项

## <a name="registry-value"></a>注册表值

*实体名称：RegistryValue*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “registry-value” |
| 密钥 | 实体 (RegistryKey) | 注册表项实体。 |
| 名称 | String | 注册表值名称。 |
| “值” | 字符串 | 值数据的字符串格式表示形式。 |
| ValueType | Enum? | 以下值之一：<li>String<li>二进制<li>DWord<li>四字<li>MultiString<li>ExpandString<li>无<li>未知<br>值应符合 Microsoft.Win32.RegistryValueKind 枚举。 |
|

注册表值实体的强标识符：
- 项 + 名称

注册表值实体的弱标识符：
- 名称（无项）

## <a name="security-group"></a>安全组

*实体名称：SecurityGroup*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “security-group” |
| DistinguishedName | 字符串 | 组可分辨名称。 |
| SID | 字符串 | SID 特性是一个单值属性，它指定组 (SID) 的安全标识符。 |
| ObjectGuid | Guid? | ObjectGUID 特性是一个单值属性，它是对象的唯一标识符，由 Active Directory 分配。 |
|

安全组实体的强标识符：
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “url” |
| URL | Uri | 实体指向的完整 URL。 |
|

URL 实体的强标识符：
- URL（当使用绝对 URL 时）

URL 实体的弱标识符：
- URL（当使用相对 URL 时）

## <a name="iot-device"></a>IoT 设备

*实体名称：IoTDevice*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “iotdevice” |
| IoTHub | 实体 (AzureResource) | 表示设备所属的 IoT 中心的 AzureResource 实体。 |
| DeviceId | 字符串 | IoT 中心中设备的 ID。 |
| DeviceName | 字符串 | 设备的友好名称。 |
| IoTSecurityAgentId | Guid? | 设备上运行的 Defender for IoT 代理程序的 ID。 |
| DeviceType | 字符串 | 设备的类型（“温度传感器”、“冷冻机”、“风力涡轮机”等）。 |
| 源 | 字符串 | 设备实体的源（Microsoft/供应商）。 |
| SourceRef | 实体 (Url) | 对设备管理的源项的 URL 引用。 |
| 制造商 | 字符串 | 设备制造商。 |
| 建模 | 字符串 | 设备型号。 |
| OperatingSystem | 字符串 | 设备正在运行的操作系统。 |
| IpAddress | 实体 (IP) | 设备的当前 IP 地址。 |
| MacAddress | 字符串 | 设备的 MAC 地址。 |
| 协议 | 列出&lt;字符串&gt; | 设备支持的协议的列表。 |
| SerialNumber | 字符串 | 设备的序列号。 |
|

IoT 设备实体的强标识符：
- IoTHub + DeviceId

IoT 设备实体的弱标识符：
- DeviceId（无 IoTHub）

## <a name="mailbox"></a>邮箱

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “mailbox” |
| MailboxPrimaryAddress | 字符串 | 邮箱的主要地址。 |
| DisplayName | 字符串 | 邮箱的显示名称。 |
| Upn | 字符串 | 邮箱的 UPN。 |
| RiskLevel | Enum? | 此邮箱的风险级别。 可能的值：<li>无<li>低<li>中<li>高 |
| ExternalDirectoryObjectId | Guid? | 邮箱的 AzureAD 标识符。 与 Account 实体中的 AadUserId 类似，但此属性特定于 Office 端的邮箱对象。 |
|

邮箱实体的强标识符：
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>邮件群集

*实体名称：MailCluster*

> [!NOTE]
> Microsoft Defender for Office 365 之前被称为 Office 365 高级威胁防护 (O365 ATP)。

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “mail-cluster” |
| NetworkMessageIds | 列出&lt;字符串&gt; | 邮件 ID 是邮件群集的一部分。 |
| CountByDeliveryStatus | IDictionary&lt;String,Int&gt; | 通过 DeliveryStatus 字符串表示形式的邮件计数。 |
| CountByThreatType | IDictionary&lt;String,Int&gt; | 通过 ThreatType 字符串表示形式的邮件计数。 |
| CountByProtectionStatus | IDictionary&lt;String,long&gt; | 按威胁防护状态的邮件计数。 |
| 威胁 | 列出&lt;字符串&gt; | 作为邮件群集一部分的邮件的威胁。 |
| 查询 | String | 用于标识邮件群集的消息的查询。 |
| QueryTime | DateTime? | 查询时间。 |
| MailCount | Int? | 作为邮件群集一部分的邮件数量。 |
| IsVolumeAnomaly | Bool? | 确定这是否为卷异常邮件群集。 |
| 源 | 字符串 | 邮件群集的源（默认为“O365 ATP”）。 |
| ClusterSourceIdentifier | 字符串 | 作为此邮件群集的源的邮件的网络消息 ID。 |
| ClusterSourceType | 字符串 | 邮件群集的源类型。 这会映射到来自 Microsoft Defender for Office 365 的 MailClusterSourceType 设置（见上文注释）。 |
| ClusterQueryStartTime | DateTime? | 群集开始时间 - 用作群集计数查询的开始时间。 通常可追溯到 Microsoft Defender for Office 365 的“结束时间”减去“DaysToLookBack”设置（见上文注释）。 |
| ClusterQueryEndTime | DateTime? | 群集结束时间 - 用作群集计数查询的结束时间。 通常是邮件数据的接收时间。 |
| ClusterGroup | 字符串 | 对应于 Microsoft Defender for Office 365 上使用的 Kusto 查询密钥（见上文注释）。 |
|

邮件群集实体的强标识符：
- 查询 + 源

## <a name="mail-message"></a>邮件消息

*实体名称：MailMessage*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “mail-message” |
| 文件 | 列出&lt;文件&gt; | 此邮件消息附件的文件实体。 |
| Recipient | 字符串 | 此邮件消息的收件人。 对于多个收件人，会复制邮件，每个副本都有一个收件人。 |
| Url | 列出&lt;字符串&gt; | 此邮件消息中包含的 URL。 |
| 威胁 | 列出&lt;字符串&gt; | 此邮件消息中包含的威胁。 |
| 发送方 | 字符串 | 发件人的电子邮件地址。 |
| P1Sender | 字符串 | （委托）用户的电子邮件 ID，该用户已“代表 P2（主）用户”发送此邮件。 如果委托未发送电子邮件，则此值等于 P2Sender。 |
| P1SenderDisplayName | 字符串 | （委托）用户的显示名称，该用户已“代表 P2（主）用户”发送此邮件。 在电子邮件标头中以“OnbehalfofSenderDisplayName”属性表示。 |
| P1SenderDomain | 字符串 | （委托）用户的电子邮件域，该用户已“代表 P2（主）用户”发送此邮件。 如果委托未发送邮件，则此值等于 P2SenderDomain。 |
| P2Sender | 字符串 | 发送此邮件的（主）用户的邮件。 |
| P2SenderDisplayName | 字符串 | 发送此电子邮件的（主）用户的显示名称。 如果委托未发送邮件，则表示发送者的显示名称。 |
| P2SenderDomain | 字符串 | 发送此电子邮件的（主）用户的电子邮件域。 如果委托未发送邮件，则表示发件人的发送方的域。 |
| SenderIP | 字符串 | 发件人的 IP 地址。 |
| ReceivedDate | DateTime | 此消息的接收日期。 |
| NetworkMessageId | Guid? | 此邮件消息的网络消息 ID。 |
| InternetMessageId | 字符串 | 此邮件消息的 internet 消息 ID。 |
| 使用者 | 字符串 | 此邮件消息的主题。 |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Microsoft Defender for Office 365 用于查找匹配或类似的邮件消息。 |
| AntispamDirection | Enum? | 此邮件消息的方向性。 可能的值：<li>未知<li>入站<li>出站<li>Intraorg（内部） |
| DeliveryAction | Enum? | 此邮件消息的传递操作。 可能的值：<li>未知<li>DeliveredAsSpam<li>已交货<li>已阻止<li>已替换 |
| DeliveryLocation | Enum? | 此邮件消息的传递位置。 可能的值：<li>未知<li>Inbox<li>JunkFolder<li>DeletedFolder<li>隔离<li>外部<li>失败<li>Dropped<li>Forwarded |
| 语言 | 字符串 | 写入邮件内容所用的语言。 |
| ThreatDetectionMethods | 列出&lt;字符串&gt; | 应用于此邮件的威胁检测方法的列表。 |
|

邮件消息实体的强标识符：
- NetworkMessageId + 收件人

## <a name="submission-mail"></a>提交邮件

*实体名称：SubmissionMail*

| 字段 | 类型 | 说明 |
| ----- | ---- | ----------- |
| 类型 | 字符串 | “SubmissionMail” |
| SubmissionId | Guid? | 提交 ID。 |
| SubmissionDate | DateTime? | 此提交的报告日期时间。 |
| “提交者” | 字符串 | 提交者的电子邮件地址。 |
| NetworkMessageId | Guid? | 提交所属的邮件的网络消息 ID。 |
| 时间戳 | DateTime? | 接收到（邮件）消息时的时间戳。 |
| Recipient | 字符串 | 邮件的收件人。 |
| 发送方 | 字符串 | 邮件的发送人。 |
| SenderIp | 字符串 | 发件人的 IP。 |
| 使用者 | 字符串 | 提交邮件的主题。 |
| ReportType | 字符串 | 给定实例的提交类型。 这会映射到垃圾邮件、网络钓鱼诈骗、恶意软件或 NotJunk。 |
|

SubmissionMail 实体的强标识符：
- SubmissionId、提交者、NetworkMessageId、收件人

## <a name="cloud-application-identifiers"></a>云应用程序标识符

以下列表定义了已知的云应用程序的标识符。 应用 ID 值用作[云应用程序](#cloud-application)实体标识符。

|应用 ID|名称|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive 软件|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive for Business|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft Office SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk 合成生命周期|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype for Business|
|25988|Google Docs|
|26055|Microsoft Office 365 管理中心|
|26060|OPSWAT Gears|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS Proxy Emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 中的实体结构、标识符和架构。

详细了解[实体](entities-in-azure-sentinel.md)和[实体映射](map-data-fields-to-entities.md)。 
