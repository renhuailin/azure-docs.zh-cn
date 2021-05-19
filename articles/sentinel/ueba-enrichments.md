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
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901677"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel UEBA 扩充引用

以下表格列出并描述了可用于集中和锐化安全事件调查的实体扩充。

前两个表“用户见解”和“设备见解”包含来自 Active Directory/Azure AD 和 Microsoft 威胁情报源的实体信息。

<a name="baseline-explained"></a>“活动见解表”下的其余表包含基于 Azure Sentinel 的实体行为分析生成的行为配置文件的实体信息。 根据每次使用时动态编译的基线对这些活动进行分析。 每个活动都有其定义的回溯期，此动态基线从该回溯期派生。 此回溯期在这个表中的 [**基线**](#activity-insights-tables)列中指定。

> [!NOTE] 
> 所有三个表中的“扩充名称”字段都显示两行信息。 第一行以“粗体”显示扩充的“易记名称”。 第二行（斜体且加括号）是存储在 [**行为分析表**](identify-threats-with-entity-behavior-analytics.md#data-schema)中的扩充的字段名称。

## <a name="user-insights-table"></a>用户见解表

| 扩充名称 | 说明 | 示例值 |
| --- | --- | --- | --- |
| **帐户显示名称**<br>*(AccountDisplayName)* | 用户的帐户显示名称。 | Admin、Hayden Cook |
| **帐户域**<br>*(AccountDomain)* | 用户的帐户域名。 |  |
| **帐户对象 ID**<br>*(AccountObjectID)* | 用户的帐户对象 ID。 | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **冲击半径**<br>*(BlastRadius)* | 冲击半径根据多个因素来计算：用户在组织树中的位置，以及用户的 Azure Active Directory 角色和权限。 | 低、中、高 |
| **休眠帐户**<br>*(IsDormantAccount)* | 此帐户在过去 180 天内未使用。 | True、False |
| **本地管理员**<br>*(IsLocalAdmin)* | 此帐户具有本地管理员权限。 | True、False |
| **新帐户**<br>*(IsNewAccount)* | 此帐户是在过去 30 天内创建的。 | True、False |
| **本地 SID**<br>*(OnPremisesSID)* | 与该操作相关的用户的本地 SID。 | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>设备见解表

| 扩充名称 | 说明 | 示例值 |
| --- | --- | --- | --- |
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

## <a name="activity-insights-tables"></a>活动见解表

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