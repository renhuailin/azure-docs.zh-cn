---
title: Azure AD 多重身份验证数据驻留
description: 了解 Azure AD 多重身份验证存储了关于你和你的用户的哪些个人和组织数据，以及哪些数据保留在来源国/地区内。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa8098192340505ecb7555f407375690c36ecdfb
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525797"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证的数据驻留和客户数据

Azure Active Directory (Azure AD) 根据组织在订阅 Microsoft 在线服务（例如 Microsoft 365 或 Azure）时提供的地址，将客户数据存储在相关地理位置。 要了解客户数据的存储位置，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)。

基于云的 Azure 多重身份验证和 MFA 服务器会处理和存储个人数据和组织数据。 本文简要介绍了存储哪些数据及存储在何处。

Azure AD 多重身份验证服务在美国、欧洲和亚太地区都有数据中心。 以下活动源自区域数据中心，但以下情况除外：

* 使用电话进行的多重身份验证源自客户所在区域的数据中心，由全球提供商进行路由。 使用自定义问候语的电话呼叫始终源自美国的数据中心。
* 来自其他区域的常规用途用户身份验证请求当前根据用户位置进行处理。
* 使用 Microsoft Authenticator 应用的推送通知当前在区域数据中心根据用户位置进行处理。 特定于供应商的设备服务（如 Apple Push Notification 服务）可能位于用户位置之外。

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证存储的个人数据

个人数据是与特定人员关联的用户级信息。 以下数据存储包含个人信息：

* 被阻止的用户
* 免验证的用户
* Microsoft Authenticator 设备令牌更改请求
* 多重身份验证活动报告 - 存储本地多重身份验证组件（NPS 扩展、AD FS 适配器和 MFA 服务器）中的多重身份验证活动。
* Microsoft Authenticator 激活

此信息将保留 90 天。

Azure AD 多重身份验证不会记录个人数据，如用户名、电话号码或 IP 地址。 但是，UserObjectId 会识别对用户的身份验证尝试。 日志数据将存储 30 天。

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证存储的数据

对于 Azure 公有云（不包括 Azure AD B2C 身份验证、NPS 扩展、Windows Server 2016 或 2019 Active Directory 联合身份验证服务 (AD FS) 适配器），将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 多重身份验证日志     |
| 单向短信                          | 多重身份验证日志     |
| 语音呼叫                           | 多重身份验证日志<br/>多重身份验证活动报告数据存储<br/>被阻止的用户（如果报告了欺诈） |
| Microsoft Authenticator 通知 | 多重身份验证日志<br/>多重身份验证活动报告数据存储<br/>被阻止的用户（如果报告了欺诈）<br/>Microsoft Authenticator 设备令牌更改时的更改请求 |

对于 Microsoft Azure 政府、Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure、Azure AD B2C 身份验证、NPS 扩展，以及 Windows Server 2016 或 2019 AD FS 适配器，将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 多重身份验证日志<br/>多重身份验证活动报告数据存储 |
| 单向短信                          | 多重身份验证日志<br/>多重身份验证活动报告数据存储 |
| 语音呼叫                           | 多重身份验证日志<br/>多重身份验证活动报告数据存储<br/>被阻止的用户（如果报告了欺诈） |
| Microsoft Authenticator 通知 | 多重身份验证日志<br/>多重身份验证活动报告数据存储<br/>被阻止的用户（如果报告了欺诈）<br/>Microsoft Authenticator 设备令牌更改时的更改请求 |

### <a name="data-stored-by-mfa-server"></a>MFA 服务器存储的数据

使用 MFA 服务器将会存储以下个人数据。

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 不再为新部署提供 MFA 服务器。 希望用户执行多重身份验证的新客户应使用基于云的 Azure AD 多重身份验证。 在 2019 年 7 月 1 日之前激活了多重身份验证服务器的现有客户可像平时一样下载最新版本和更新，也可生成激活凭据。

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 多重身份验证日志<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 多重身份验证日志<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 多重身份验证日志<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告了欺诈） |
| Microsoft Authenticator 通知 | 多重身份验证日志<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告了欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证存储的组织数据

组织数据是可公开配置或环境设置的租户级信息。 来自 Azure 门户中下列多重身份验证页面的租户设置可存储组织数据，例如锁定阈值或传入电话身份验证请求的呼叫方 ID 信息：

* 帐户锁定
* 欺诈警报
* 通知
* 电话呼叫设置

对于 MFA 服务器，Azure 门户的以下页面可能包含组织数据：

* 服务器设置
* 免验证一次
* 缓存规则
* 多重身份验证服务器状态

## <a name="multifactor-authentication-activity-reports-for-public-cloud"></a>公有云的多重身份验证活动报告

多重身份验证活动报告会存储来自本地组件（NPS 扩展、AD FS 适配器和 MFA 服务器）的活动。 多重身份验证服务日志用于运行服务。
以下部分显示活动报告和服务日志（用于不同客户区域的每个组件的特定身份验证方法）的存储位置。 标准语音呼叫可能会故障转移到不同的区域。

>[!NOTE]
>多重身份验证活动报告包含个人数据，如用户主体名称 (UPN) 和完整的电话号码。

### <a name="nps-extension-and-ad-fs-adapter"></a>NPS 扩展和 AD FS 适配器

| 身份验证方法                                                             | 客户区域                      | 活动报告位置 | 服务日志位置 |
|-----------------------------------------------------------------------------------|--------------------------------------|--------------------------|----------------------|
| OATH 软件和硬件令牌                                                 | 澳大利亚和新西兰            | 澳大利亚/新西兰    | 云区域内      |
| OATH 软件和硬件令牌                                                 | 澳大利亚和新西兰境外 | 美国            | 云区域内      |
| 不含自定义问候语和所有其他身份验证方法（OATH 软件和硬件令牌除外）的语音呼叫  | 任意                               | 美国            | 云区域内      |
| 含自定义问候语的语音呼叫                                         | 任意                                  | 美国            | 美国的 MFA 后端 |

### <a name="mfa-server-and-cloud-based-mfa"></a>MFA 服务器和基于云的 MFA

| 组件  | 身份验证方法                          | 客户区域                      | 活动报告位置        | 服务日志位置         |
|------------|------------------------------------------------|--------------------------------------|---------------------------------|------------------------------|
| MFA 服务器 | 所有方法                                    | 任意                                  | 美国                   | 美国的 MFA 后端 |
| 云 MFA  | 标准语音呼叫和所有其他方法     | 任意                                  | 区域中的 Azure AD 登录日志 | 云区域内              |
| 云 MFA  | 含自定义问候语的语音呼叫              | 任意                                  | 区域中的 Azure AD 登录日志 | 美国的 MFA 后端 |

## <a name="multifactor-authentication-activity-reports-for-sovereign-clouds"></a>主权云的多重身份验证活动报告

下表显示了主权云的服务日志的位置。

| 主权云                      | 登录日志                         | 多重身份验证活动报告 | 多重身份验证服务日志 |
|--------------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------|
| Microsoft Azure 德国              | 德国                              | 美国                              | 美国                           |
| Azure 中国世纪互联                 | 中国                                | 美国                              | 美国                           |
| Microsoft 政府云           | 美国                        | 美国                              | 美国                           |

## <a name="next-steps"></a>后续步骤

要详细了解基于云的 Azure AD 多重身份验证和 MFA 服务器收集的用户信息，请参阅 [Azure AD 多重身份验证用户数据收集](howto-mfa-reporting-datacollection.md)。
