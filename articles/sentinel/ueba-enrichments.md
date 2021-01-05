---
title: Azure Sentinel UEBA 根据 reference |Microsoft Docs
description: 本文显示 Azure Sentinel 的实体行为分析生成的实体根据。
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
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901677"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel UEBA 根据 reference

这些表列出并描述了可用于集中和锐化安全事件调查的实体根据。

前两个表： **用户见解** 和 **设备见解** 包含来自 Active Directory/Azure AD 和 Microsoft 威胁智能源的实体信息。

<a name="baseline-explained"></a>**活动见解表** 下的表的其余部分包含基于 Azure Sentinel 的实体行为分析所生成的行为配置文件的实体信息。 针对每次使用时动态编译的基线分析活动。 每个活动都有其定义的 lookback 期间，此动态基线派生自该时间段。 此时间段在此表的 " [**基线**](#activity-insights-tables) " 列中指定。

> [!NOTE] 
> 所有三个表中的 " **扩充名称** " 字段显示两行信息。 以 **粗体显示** 的第一个是扩充的 "友好名称"。 第二个 *(以斜体和括号)* 为存储在 [**行为分析表**](identify-threats-with-entity-behavior-analytics.md#data-schema)中的扩充的字段名称。

## <a name="user-insights-table"></a>用户见解表

| 扩充名称 | 说明 | 示例值 |
| --- | --- | --- | --- |
| **帐户显示名称**<br>*(AccountDisplayName)* | 用户的帐户显示名称。 | 管理员、Hayden 库 |
| **帐户域**<br>*(AccountDomain)* | 用户的帐户域名。 |  |
| **帐户对象 ID**<br>*(AccountObjectID)* | 用户的帐户对象 ID。 | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **群发 radius**<br>*(BlastRadius)* | 群发 radius 根据多个因素来计算：用户在组织树中的位置，以及用户的 Azure Active Directory 角色和权限。 | 低、中、高 |
| **为睡眠帐户**<br>*(IsDormantAccount)* | 过去的180天内未使用该帐户。 | True、False |
| **为本地管理员**<br>*(IsLocalAdmin)* | 此帐户具有本地管理员特权。 | True、False |
| **为新帐户**<br>*(IsNewAccount)* | 此帐户是在过去30天内创建的。 | True、False |
| **本地 SID**<br>*(OnPremisesSID)* | 与该操作相关的用户的本地 SID。 | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>设备见解表

| 扩充名称 | 说明 | 示例值 |
| --- | --- | --- | --- |
| **浏览器**<br>*(Browser)* | 操作中使用的浏览器。 | Edge、Chrome |
| **设备系列**<br>*(DeviceFamily)* | 操作中使用的设备系列。 | Windows |
| **设备类型**<br>*(DeviceType)* | 操作中使用的客户端设备类型 | 桌面型 |
| **ISP**<br>*(ISP)* | 操作中使用的 internet 服务提供商。 |  |
| **操作系统**<br>*(操作系统)* | 操作中使用的操作系统。 | Windows 10 |
| **威胁 intel 指标描述**<br>*(ThreatIntelIndicatorDescription)* | 从操作中使用的 IP 地址解析的观察到的威胁指标的说明。 | Host 是僵尸网络的成员： azorult |
| **威胁 intel 指标类型**<br>*(ThreatIntelIndicatorType)* | 从操作中使用的 IP 地址解析的威胁指标的类型。 | 僵尸网络，C2，CryptoMining，暗网，Ddos，MaliciousUrl，恶意软件，仿冒，Proxy，PUA，播放列表 |
| **用户代理**<br>*(UserAgent)* | 操作中使用的用户代理。 | Microsoft Azure Graph 客户端库1.0，<br>Swagger-Codegen/1.4.0.0/csharp，<br>EvoSTS |
| **用户代理系列**<br>*(UserAgentFamily)* | 操作中使用的用户代理系列。 | Chrome、Edge、Firefox |
|

## <a name="activity-insights-tables"></a>活动见解表

#### <a name="action-performed"></a>执行的操作

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户第一次执行操作**<br>*(FirstTimeUserPerformedAction)* | 180 | 此操作是用户第一次执行的。 | True、False |
| **用户执行的不常见操作**<br>*(ActionUncommonlyPerformedByUser)* | 10 | 用户通常不会执行该操作。 | True、False |
| **在对等方之间执行的不常见操作**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | 通常不会在用户的对等方之间执行操作。 | True、False |
| **租户中的第一次操作执行**<br>*(FirstTimeActionPerformedInTenant)* | 180 | 此操作是由组织中的任何人首次执行。 | True、False |
| **在租户中执行的不常见操作**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | 通常不会在组织中执行此操作。 | True、False |
|

#### <a name="app-used"></a>使用的应用

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次使用应用程序**<br>*(FirstTimeUserUsedApp)* | 180 | 用户首次使用该应用。 | True、False |
| **用户使用的应用不常见**<br>*(AppUncommonlyUsedByUser)* | 10 | 用户通常不会使用该应用。 | True、False |
| **在对等方之间使用的应用不常见**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | 应用通常不在用户的对等方之间使用。 | True、False |
| **第一次在租户中观测应用**<br>*(FirstTimeAppObservedInTenant)* | 180 | 在组织中首次观察到该应用。 | True、False |
| **租户中使用的应用不常见**<br>*(AppUncommonlyUsedInTenant)* | 180 | 此应用通常不在组织中使用。 | True、False |
| 

#### <a name="browser-used"></a>使用的浏览器

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次通过浏览器连接**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | 用户第一次观察到浏览器。 | True、False |
| **用户使用的浏览器不常见**<br>*(BrowserUncommonlyUsedByUser)* | 10 | 用户通常不使用浏览器。 | True、False |
| **在对等方之间使用浏览器不常见**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | 浏览器通常不在用户的对等方之间使用。 | True、False |
| **第一次在租户中观测浏览器**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | 在组织中首次观察到浏览器。 | True、False |
| **租户中使用的浏览器不常见**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | 组织中通常不使用浏览器。 | True、False |
| 

#### <a name="country-connected-from"></a>从其连接的国家/地区

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **第一次从国家/地区连接的用户**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | 从 IP 地址解析的地理位置是第一次由用户连接的。 | True、False |
| **用户不常见连接的国家/地区**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | 从 IP 地址解析的地理位置通常不是由用户进行连接。 | True、False |
| **从双方之间连接的国家/地区不常见**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | 从 IP 地址解析的地理位置通常不会从用户的对等方进行连接。 | True、False |
| **第一次从租户中观测到的国家/地区连接**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 从组织中的任何人首次连接该国家/地区。 | True、False |
| **租户中从不常见连接的国家/地区**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | 从 IP 地址解析的地理位置通常不会从组织中连接。 | True、False |
| 

#### <a name="device-used-to-connect"></a>用于连接的设备

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次从设备连接**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | 用户第一次连接源设备。 | True、False |
| **用户使用的设备不常见**<br>*(DeviceUncommonlyUsedByUser)* | 10 | 用户通常不使用此设备。 | True、False |
| **在对等方之间使用的设备不常见**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | 设备通常不在用户的对等方之间使用。 | True、False |
| **第一次在租户中观测设备**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | 在组织中首次观察到设备。 | True、False |
| **租户中使用的设备不常见**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | 设备通常不在组织中使用。 | True、False |
| 

#### <a name="other-device-related"></a>其他与设备相关的

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次登录到设备**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | 用户第一次将目标设备连接到。 | True、False |
| **租户中使用的设备系列不常见**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | 组织中通常不使用设备系列。 | True、False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>用于连接的 Internet 服务提供商

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户第一次通过 ISP 连接**<br>*(FirstTimeUserConnectedViaISP)* | 30 | 用户第一次观察到了 ISP。 | True、False |
| **用户使用的 ISP 不常见**<br>*(ISPUncommonlyUsedByUser)* | 10 | 用户通常不使用 ISP。 | True、False |
| **在对等方之间使用的 ISP 不常见**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | 用户的对等机中通常不使用 ISP。 | True、False |
| **第一次通过租户中的 ISP 连接**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | 在组织中首次观察到了 ISP。 | True、False |
| **租户中使用的 ISP 不常见**<br>*(ISPUncommonlyUsedInTenant)* | 30 | 组织中通常不使用 ISP。 | True、False |
| 

#### <a name="resource-accessed"></a>访问的资源

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **用户首次访问资源**<br>*(FirstTimeUserAccessedResource)* | 180 | 用户第一次访问该资源。 | True、False |
| **用户访问的资源不常见**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | 用户通常不会访问该资源。 | True、False |
| **在对等方之间访问资源不常见**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | 通常不会在用户的对等方之间访问资源。 | True、False |
| **第一次在租户中访问资源**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 资源是第一次被组织中的任何人访问。 | True、False |
| **已在租户中访问资源不常见**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | 此资源通常不是在组织中访问的。 | True、False |
| 

#### <a name="miscellaneous"></a>杂项

| 扩充名称 | [基线](#baseline-explained) (天)  | 说明 | 示例值 |
| --- | --- | --- | --- |
| **上次用户执行操作的时间**<br>*(LastTimeUserPerformedAction)* | 180 | 用户上次执行相同操作的时间。 | <Timestamp> |
| **未在过去执行类似操作**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 用户执行的不是同一资源提供程序中的任何操作。 | True、False |
| **源 IP 位置**<br>*(SourceIPLocation)* | *不适用* | 从操作的源 IP 解析的国家/地区。 | [Surrey，英国] |
| **很少的操作**<br>*(UncommonHighVolumeOfOperations)* | 7 | 用户在同一个提供程序中执行了类似操作的突发操作 | True、False |
| **Azure AD 条件访问失败的异常数量**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 由于条件性访问，不寻常的用户无法进行身份验证 | True、False |
| **添加的设备数量异常**<br>*(UnusualNumberOfDevicesAdded)* | 5 | 用户添加了不寻常数量的设备。 | True、False |
| **删除的设备的数量异常**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | 用户删除了不寻常数量的设备。 | True、False |
| **添加到组中的用户数量异常**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | 用户向组添加了不寻常数量的用户。 | True、False |
|