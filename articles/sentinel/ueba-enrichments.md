---
title: Azure Sentinel UEBA 扩充引用 | Microsoft Docs
description: 本文显示 Azure Sentinel 的实体行为分析生成的实体扩充。
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
ms.date: 05/10/2021
ms.author: yelevin
ms.openlocfilehash: 3253a399015e533fc299bc7a338ce091769f4c65
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721692"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel UEBA 扩充引用

本文介绍了在“日志”中找到并在[实体详细信息页](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages)上提到的 Azure Sentinel“BehaviorAnalytics”表，并提供了该表中实体扩充字段的详细信息，其中的内容可用于专注处理和加强安全事件调查 。

[下面的表](#entity-enrichments-dynamic-fields)介绍了 BehaviorAnalytics 表中的以下三个动态字段。

[UsersInsights](#usersinsights-field) 和 [DevicesInsights](#devicesinsights-field) 字段包含来自 Active Directory/Azure AD 和 Microsoft 威胁情报源的实体信息。

[ActivityInsights](#activityinsights-field) 字段包含的实体信息基于 Azure Sentinel 的实体行为分析生成的行为配置文件。 

<a name="baseline-explained"></a>根据每次使用时都会动态编译的基线来分析用户活动。 每个活动都有其定义的回溯期，动态基线从该回溯期中派生。 此表的[基线](#activityinsights-field)列中指定了该回溯期。

> [!NOTE]
> 所有[实体扩充字段](#entity-enrichments-dynamic-fields)表中的“扩充名称”列都显示两行信息。 
>
> - 第一行以“粗体”显示扩充的“易记名称”。
> - 第二行（斜体且加括号）是存储在 [**行为分析表**](#behavioranalytics-table)中的扩充的字段名称。

> [!IMPORTANT]
> 请注意，这些功能目前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>
## <a name="behavioranalytics-table"></a>BehaviorAnalytics 表

下表说明了 Azure Sentinel 中每个[实体详细信息页](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages)上显示的行为分析数据。

| 字段                     | 类型 | 说明                                                  |
|---------------------------|------|--------------------------------------------------------------|
| **TenantId**              | string | 租户的唯一 ID 编号。                             |
| **SourceRecordId**        | 字符串 | EBA 事件的唯一 ID 编号。                          |
| **TimeGenerated**         | datetime | 活动发生时的时间戳。                   |
| **TimeProcessed**         | datetime | EBA 引擎处理活动时的时间戳。 |
| **ActivityType**          | 字符串 | 活动的高级类别。                        |
| **ActionType**            | 字符串 | 活动的规范化名称。                            |
| **UserName**              | 字符串 | 发起活动的用户的用户名。           |
| **UserPrincipalName**     | 字符串 | 发起活动的用户的完整用户名。      |
| **EventSource**           | 字符串 | 提供原始事件的数据源。               |
| **SourceIPAddress**       | 字符串 | 发起活动的 IP 地址。               |
| **SourceIPLocation** | 字符串 | 发起活动的国家/地区，从 IP 地址进行扩充。 |
| **SourceDevice**          | 字符串 | 发起活动的设备的主机名。         |
| **DestinationIPAddress**  | 字符串 | 活动目标的 IP 地址。                   |
| **DestinationIPLocation** | 字符串 | 活动目标所在国家/地区，从 IP 地址进行扩充。 |
| **DestinationDevice**     | 字符串 | 目标设备的名称。                                  |
| **UsersInsights**         | 动态 | 相关用户的上下文扩充（[详细信息如下](#usersinsights-field)）。 |
| **DevicesInsights**       | 动态 | 相关设备的上下文扩充（[详细信息如下](#devicesinsights-field)）。 |
| **ActivityInsights**      | 动态 | 基于我们的分析的活动的上下文分析（[详细信息如下](#activityinsights-field)）。 |
| **InvestigationPriority** | int | 异常分数，介于 0-10（0=良性，10=高度异常）。   |



## <a name="entity-enrichments-dynamic-fields"></a>实体扩充动态字段

### <a name="usersinsights-field"></a>UsersInsights 字段

下表说明了 BehaviorAnalytics 表中 UsersInsights 动态字段中提供的扩充：

| 扩充名称 | 说明 | 示例值 |
| --- | --- | --- |
| **帐户显示名称**<br>*(AccountDisplayName)* | 用户的帐户显示名称。 | Admin、Hayden Cook |
| **帐户域**<br>*(AccountDomain)* | 用户的帐户域名。 |  |
| **帐户对象 ID**<br>*(AccountObjectID)* | 用户的帐户对象 ID。 | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **冲击半径**<br>*(BlastRadius)* | 冲击半径根据多个因素来计算：用户在组织树中的位置，以及用户的 Azure Active Directory 角色和权限。 | 低、中、高 |
| **休眠帐户**<br>*(IsDormantAccount)* | 此帐户在过去 180 天内未使用。 | True、False |
| **本地管理员**<br>*(IsLocalAdmin)* | 此帐户具有本地管理员权限。 | True、False |
| **新帐户**<br>*(IsNewAccount)* | 此帐户是在过去 30 天内创建的。 | True、False |
| **本地 SID**<br>*(OnPremisesSID)* | 与该操作相关的用户的本地 SID。 | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

### <a name="devicesinsights-field"></a>DevicesInsights 字段

下表说明了 BehaviorAnalytics 表中 DevicesInsights 动态字段中提供的扩充：

| 扩充名称 | 说明 | 示例值 |
| --- | --- | --- |
| **浏览器**<br>*(Browser)* | 操作中使用的浏览器。 | Edge、Chrome |
| **设备系列**<br>*(DeviceFamily)* | 操作中使用的设备系列。 | Windows |
| **设备类型**<br>*(DeviceType)* | 操作中使用的客户端设备类型 | 桌面 |
| **ISP**<br>*(ISP)* | 操作中使用的 Internet 服务提供商。 |  |
| **操作系统**<br>*(OperatingSystem)* | 操作中使用的操作系统。 | Windows 10 |
| **威胁 intel 指标说明**<br>*(ThreatIntelIndicatorDescription)* | 从操作中使用的 IP 地址解析的观察到的威胁指标的说明。 | 主机是僵尸网络的成员：azorult |
| **威胁 intel 指标类型**<br>*(ThreatIntelIndicatorType)* | 从操作中使用的 IP 地址解析的威胁指标的类型。 | 僵尸网络、C2、CryptoMining、Darknet、Ddos、MaliciousUrl、恶意软件、仿冒、代理、PUA、播放列表 |
| **用户代理**<br>*(UserAgent)* | 操作中使用的用户代理。 | Microsoft Azure Graph Client Library 1.0、<br>Swagger-Codegen/1.4.0.0/csharp、<br>EvoSTS |
| **用户代理系列**<br>*(UserAgentFamily)* | 操作中使用的用户代理系列。 | Chrome、Edge、Firefox |
|

### <a name="activityinsights-field"></a>ActivityInsights 字段

下表说明了 BehaviorAnalytics 表中 ActivityInsights 动态字段中提供的扩充：

#### <a name="action-performed"></a>已执行的操作

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次执行的操作**<br>*(FirstTimeUserPerformedAction)* | 180 | 用户首次执行此操作。 | True、False |
| **用户不常执行的操作**<br>*(ActionUncommonlyPerformedByUser)* | 10 | 用户不常执行此操作。 | True、False |
| **对等机之间不常执行的操作**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | 用户的对等机之间不常执行此操作。 | True、False |
| **首次在租户中执行的操作**<br>*(FirstTimeActionPerformedInTenant)* | 180 | 此操作由组织中的任何人首次执行。 | True、False |
| **租户中不常执行的操作**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | 组织中不常执行此操作。 | True、False |
|

#### <a name="app-used"></a>使用的应用

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次使用的应用**<br>*(FirstTimeUserUsedApp)* | 180 | 用户首次使用此应用。 | True、False |
| **用户不常使用的应用**<br>*(AppUncommonlyUsedByUser)* | 10 | 用户不常使用此应用。 | True、False |
| **在对等机之间不常使用的应用**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | 在用户的对等机之间不常使用此应用。 | True、False |
| **首次在租户中观察到的应用**<br>*(FirstTimeAppObservedInTenant)* | 180 | 在组织中首次观察到此应用。 | True、False |
| **租户中不常使用的应用**<br>*(AppUncommonlyUsedInTenant)* | 180 | 组织中不常使用此应用。 | True、False |
| 

#### <a name="browser-used"></a>使用的浏览器

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次通过浏览器连接**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | 用户首次观察到此浏览器。 | True、False |
| **用户不常使用的浏览器**<br>*(BrowserUncommonlyUsedByUser)* | 10 | 用户不常使用此浏览器。 | True、False |
| **在对等机之间不常使用的浏览器**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | 在用户的对等机之间不常使用此浏览器。 | True、False |
| **首次在租户中观察到的浏览器**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | 在组织中首次观察到此浏览器。 | True、False |
| **租户中不常使用的浏览器**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | 组织中不常使用此浏览器。 | True、False |
| 

#### <a name="country-connected-from"></a>从其连接的国家/地区

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次从国家/地区连接**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | 用户首次从 IP 地址解析的这个地理位置连接。 | True、False |
| **用户不常从其连接的国家/地区**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | 用户不常从 IP 地址解析的这个地理位置连接。 | True、False |
| **在对等机之间不常从其连接的国家/地区**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | 用户的对等机之间不常从 IP 地址解析的这个地理位置连接。 | True、False |
| **首次从租户中观察到的国家/地区连接**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 组织中的任何人首次从该国家/地区连接。 | True、False |
| **租户中不常从其连接的国家/地区**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | 组织中不常从 IP 地址解析的这个地理位置连接。 | True、False |
| 

#### <a name="device-used-to-connect"></a>用于连接的设备

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次从设备连接**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | 用户首次从该源设备连接。 | True、False |
| **用户不常使用的设备**<br>*(DeviceUncommonlyUsedByUser)* | 10 | 用户不常使用此设备。 | True、False |
| **在对等机之间不常使用的设备**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | 在用户的对等机之间不常使用此设备。 | True、False |
| **首次在租户中观察到的设备**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | 此设备首次在组织中观察到。 | True、False |
| **租户中不常使用的设备**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | 组织中不常使用此设备。 | True、False |
| 

#### <a name="other-device-related"></a>其他相关设备

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次登录到设备**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | 用户首次连接到此目标设备。 | True、False |
| **租户中不常使用的设备系列**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | 组织中不常使用此设备系列。 | True、False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>用于连接的 Internet 服务提供商

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次通过 ISP 连接**<br>*(FirstTimeUserConnectedViaISP)* | 30 | 用户首次观察到此 ISP。 | True、False |
| **用户不常使用的 ISP**<br>*(ISPUncommonlyUsedByUser)* | 10 | 用户不常使用此 ISP。 | True、False |
| **在对等机之间不常使用的 ISP**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | 在用户的对等机之间不常使用此 ISP。 | True、False |
| **首次在租户中通过 ISP 连接**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | 在组织中首次观察到此 ISP。 | True、False |
| **租户中不常使用的 ISP**<br>*(ISPUncommonlyUsedInTenant)* | 30 | 组织中不常使用此 ISP。 | True、False |
| 

#### <a name="resource-accessed"></a>访问的资源

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次访问的资源**<br>*(FirstTimeUserAccessedResource)* | 180 | 此资源由用户首次访问。 | True、False |
| **用户不常访问的资源**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | 用户不常访问该资源。 | True、False |
| **在对等机之间不常访问的资源**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | 在用户的对等机之间不常访问该资源。 | True、False |
| **首次在租户中访问的资源**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 此资源由组织中的任何人首次访问。 | True、False |
| **租户中不常访问的资源**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | 组织中不常访问此资源。 | True、False |
| 

#### <a name="miscellaneous"></a>杂项

| 扩充名称 | [基线](#baseline-explained)（天） | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户上次执行的操作**<br>*(LastTimeUserPerformedAction)* | 180 | 上次用户执行了相同的操作。 | <Timestamp> |
| **过去未执行类似操作**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 用户未在相同的资源提供程序中执行任何操作。 | True、False |
| **源 IP 位置**<br>*(SourceIPLocation)* | *空值* | 此国家/地区从操作的源 IP 解析。 | [英国萨里] |
| **不常见的大量操作**<br>*(UncommonHighVolumeOfOperations)* | 7 | 用户在同一个提供程序中执行了一连串类似操作 | True、False |
| **Azure AD 条件访问失败的异常数量**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 由于条件访问导致无法进行身份验证的用户的异常数量 | True、False |
| **添加的异常数量的设备**<br>*(UnusualNumberOfDevicesAdded)* | 5 | 用户添加了异常数量的设备。 | True、False |
| **删除的异常数量的设备**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | 用户删除了异常数量的设备。 | True、False |
| **添加到组中的异常数量的用户**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | 用户向组中添加了异常数量的用户。 | True、False |
|


## <a name="identityinfo-table-public-preview"></a>IdentityInfo 表（公开预览版）

为 Azure Sentinel 工作区[启用 UEBA](enable-entity-behavior-analytics.md) 后，来自 Azure Active Directory 的数据将同步到 Log Analytics 中的 IdentityInfo 表以在 Azure Sentinel 中使用。 可以在分析规则中嵌入从 Azure AD 同步的用户数据，以增强分析来适应你的用例并减少误报。

虽然初始同步可能需要几天时间，但在数据完全同步后，将获得以下优势：

- 对 Azure AD 中的用户配置文件所做的更改会在 15 分钟内更新到 IdentityInfo 表中。

- 组和角色信息每天在 IdentityInfo 表和 Azure AD 之间同步。

- 每 21 天，Azure Sentinel 会与整个 Azure AD 重新同步，以确保过时的记录将完全更新。

- IdentityInfo 表中的默认保留时间为 30 天。


> [!NOTE]
> 目前，仅支持内置角色。
>
> 目前不支持有关已删除组（从组中删除了用户）的数据。
>

下表描述了 Log Analytics 的 IdentityInfo 表中包含的用户身份数据。

| 字段                      | 类型     | 说明                                                                                                             |
| --------------------------- | -------- | -------------------------------------------------------- |
| **AccountCloudSID**             | string   | 帐户的 Azure AD 安全标识符。       |
| **AccountCreationTime**         | datetime | 创建用户帐户的日期 (UTC)。    |
| **AccountDisplayName**          | string   | 用户帐户的显示名称。           |
| **AccountDomain**               | string   | 用户帐户的域名。  |
| **AccountName**                 | string   | 用户帐户的用户名。      |
| **AccountObjectId**             | string   | 用户帐户的 Azure Active Directory 对象 ID。           |
| **AccountSID**                  | string   | 用户帐户的本地安全标识符。      |
| **AccountTenantId**             | string   | 用户帐户的 Azure Active Directory 租户 ID。    |
| **AccountUPN**                  | string   | 用户帐户的用户主体名称。     |
| **AdditionalMailAddresses**     | 动态  | 用户的其他电子邮件地址。   |
| **AssignedRoles**               | 动态  | 为用户帐户分配的 Azure AD 角色。    |
| **城市**                        | string   | 用户帐户的城市。   |
| **国家/地区**                     | string   | 用户帐户的国家/地区。   |
| **DeletedDateTime**             | datetime | 删除用户的日期和时间。       |
| **部门**                  | string   | 用户帐户的部门。    |
| **GivenName**                   | string   | 用户帐户的给定名称。     |
| **GroupMembership**             | 动态  | 用户帐户所归属的 Azure AD 组。      |
| **IsAccountEnabled**            | bool     | 指示是否在 Azure AD 中启用了用户帐户。    |
| **JobTitle**                    | string   | 用户帐户的职务。    |
| **MailAddress**                 | string   | 用户帐户的主要电子邮件地址。    |
| 管理员                     | string   | 用户帐户的管理员别名。     |
| **OnPremisesDistinguishedName** | string   | Azure AD 可分辨名称 (DN)。 专有名称是一系列相对专有名称 (RDN)，由逗号连接。 |
| **电话**                       | string   | 用户帐户的电话号码。       |
| **SourceSystem**                | string   | 用户数据的来源系统。   |
| **State**                       | string   | 用户帐户的地理状态。  |
| **StreetAddress**               | string   | 用户帐户的办公街道地址。    |
| **Surname**                     | string   | 用户的姓氏。 帐户。      |
| **TenantId**                    | string   |          用户的租户 ID。   |
| **TimeGenerated**               | datetime | 生成事件的时间 (UTC)。       |
| **类型**                        | string   | 表的名称。          |
| **UserState**                   | string   | Azure AD 中用户帐户的当前状态（活动/禁用/休眠/锁定）。  |
| **UserStateChangedOn**          | datetime | 上次更改帐户状态的日期 (UTC)。     |
| **UserType**                    | string   | 用户类型。         |


## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 实体行为分析表架构。

- 详细了解[实体行为分析](identify-threats-with-entity-behavior-analytics.md)。
- [放置要在调查中使用的 UEBA](investigate-with-ueba.md)。
