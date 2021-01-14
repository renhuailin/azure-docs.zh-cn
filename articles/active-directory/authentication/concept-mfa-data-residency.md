---
title: Azure AD 多重身份验证数据派驻服务
description: 了解个人和组织数据 Azure AD 多因素身份验证存储有关你和你的用户的信息，以及哪些数据仍在源的国家/地区。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208346"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>数据驻留和客户数据用于 Azure AD 多重身份验证

客户数据根据你的组织在订阅 Microsoft Online 服务（例如 Microsoft 365 和 Azure）时所提供的地址 Azure AD 存储在地理位置。 要了解客户数据的存储位置，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

基于云的 Azure AD 多重身份验证和 Azure AD 多重身份验证服务器进程，并存储一定量的个人数据和组织数据。 本文简要介绍了存储哪些数据及存储在何处。

Azure AD 多重身份验证服务在美国、欧洲和亚太包含数据中心。 以下活动源自地区数据中心，但以下情况除外：

* 使用电话呼叫的多重身份验证源自美国数据中心，由全局提供商路由。
* 根据用户的位置，当前正在处理来自其他区域（如欧洲或澳大利亚）的常规用途用户身份验证请求。
* 使用 Microsoft Authenticator 应用的推送通知当前在区域数据中心基于用户位置进行处理。
    * 特定于设备供应商的服务（如 Apple 推送通知）可能位于用户位置之外。

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证存储的个人数据

个人数据是与特定人员关联的用户级信息。 以下数据存储包含个人信息：

* 被阻止的用户
* 免验证的用户
* Microsoft Authenticator 设备令牌更改请求
* 多重身份验证活动报表
* Microsoft Authenticator 激活

此信息将保留 90 天。

Azure AD 多重身份验证不会记录个人数据（例如用户名、电话号码或 IP 地址），但有一个 *UserObjectId* ，用于标识用户的多重身份验证尝试。 日志数据将存储 30 天。

### <a name="azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证

对于 Azure 公有云（不包括 Azure B2C 身份验证、NPS 扩展和 Windows Server 2016 或 2019 AD FS 适配器），将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中     |
| 单向短信                          | 在多重身份验证日志中     |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

对于 Microsoft Azure 政府、Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure、Azure B2C 身份验证、NPS 扩展，以及 Windows Server 2016 或 2019 AD FS 适配器，将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

### <a name="multifactor-authentication-server"></a>多重身份验证服务器

如果部署并运行 Azure AD 多重身份验证服务器，则会存储以下个人数据：

> [!IMPORTANT]
> 从2019年7月1日起，Microsoft 将不再为新部署提供多重身份验证服务器。 想要从用户要求多重身份验证的新客户应使用基于云的 Azure AD 多重身份验证。 在7月1日前激活多重身份验证服务器的现有客户将能够下载最新版本、将来更新和生成激活凭据。

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重身份验证存储的组织数据

组织数据是可公开配置或环境设置的租户级别的信息。 以下 Azure 门户多因素身份验证页面中的租户设置可能存储组织数据，如锁定阈值或传入电话身份验证请求的调用方 ID 信息：

* 帐户锁定
* 欺诈警报
* 通知
* 电话呼叫设置

对于 Azure AD 多重身份验证服务器，以下 Azure 门户页面可能包含组织数据：

* 服务器设置
* 免验证一次
* 缓存规则
* 多重身份验证服务器状态

## <a name="multifactor-authentication-logs-location"></a>多重身份验证日志位置

下表显示了公有云的服务日志的位置。

| 公有云| 登录日志 | 多重身份验证活动报表        | 多重身份验证服务日志       |
|-------------|--------------|----------------------------------------|------------------------|
| US          | US           | US                                     | US                     |
| 欧洲      | 欧洲       | 美国                                     | 欧洲 <sup>2</sup>    |
| 澳大利亚   | 澳大利亚    | US<sup>1</sup>                         | 澳大利亚 <sup>2</sup> |

<sup>1</sup>OATH 代码日志存储在澳大利亚

<sup>2</sup>语音呼叫多因素身份验证服务日志存储在美国

下表显示了主权云的服务日志的位置。

| 主权云                      | 登录日志                         | 多重身份验证活动报表 (包含个人数据) | 多重身份验证服务日志 |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure 德国              | 德国                              | US                            | US               |
| 由世纪互联运营的 Microsoft Azure | 中国                                | US                            | US               |
| Microsoft 政府云           | US                                   | US                            | US               |

多重身份验证活动报表数据包含个人数据，如用户主体名称 (UPN) 和完整的电话号码。

多重身份验证服务日志用于运行服务。

## <a name="next-steps"></a>后续步骤

有关基于云的 Azure AD 多重身份验证和 Azure AD 多重身份验证服务器收集的用户信息的详细信息，请参阅 [Azure AD 多重身份验证用户数据收集](howto-mfa-reporting-datacollection.md)。
