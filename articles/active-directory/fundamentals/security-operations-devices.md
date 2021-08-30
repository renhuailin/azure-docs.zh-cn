---
title: Azure Active Directory 设备安全操作
description: 了解如何建立基线并对设备进行监视和报告，以识别设备的潜在安全风险。
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9298c3c6f7d6c634ed5647a6473e0780cd82bf1d
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798886"
---
# <a name="azure-active-directory-security-operations-for-devices"></a>Azure Active Directory 设备安全操作

设备通常不是基于标识的攻击的目标，但可用于满足和欺骗安全控件或模拟用户。 设备可以与 Azure AD 具有以下四种关系之一： 

* 未注册

* [已注册 Azure Active Directory (Azure AD)](../devices/concept-azure-ad-register.md)

* [已加入 Azure AD](../devices/concept-azure-ad-join.md)

* [已加入混合 Azure AD](../devices/concept-azure-ad-join-hybrid.md)   
‎

系统会向已注册和已加入的设备签发[主刷新令牌 (PRT)](../devices/concept-primary-refresh-token.md)，该令牌可以用作主要身份验证项目，在某些情况下，还可以用作多重身份验证项目。 攻击者可能会尝试注册自己的设备、在合法设备上使用 PRT 访问业务数据、从合法用户设备窃取基于 PRT 的令牌，或在 Azure Active Directory 基于设备的控件中发现配置错误。 使用已加入混合 Azure AD 的设备时，联接过程由管理员发起和控制，从而减少了可用的攻击方法。

有关设备集成方法的详细信息，请参阅[规划 Azure AD 设备部署](../devices/plan-device-deployment.md)一文中的[选择集成方法](../devices/plan-device-deployment.md)。

若要降低不良参与者通过设备攻击基础结构的风险，请监视以下项：

* 设备注册和 Azure AD 加入

* 访问应用程序的不合规设备

* BitLocker 密钥检索

* 设备管理员角色

* 对虚拟机的登录

## <a name="where-to-look"></a>要查阅的内容

用于调查和监视的日志文件包括： 

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [登录日志](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide.md) 

* [Azure Key Vault 日志](../..//key-vault/general/logging.md?tabs=Vault)

在 Azure 门户中，你可以查看 Azure AD 审核日志，并以逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件格式下载。 Azure 门户提供多种将 Azure AD 日志与其他工具集成的方法，能够实现更好的监视和警报自动化：

* **[Azure Sentinel](../../sentinel/overview.md)** – 通过提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* **[Azure Monitor](../..//azure-monitor/overview.md)** – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿来合并不同源的数据。

* 与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md) -  通过 Azure 事件中心集成[可将 Azure AD 日志集成到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) – 支持发现和管理应用、跨应用和资源进行治理以及检查云应用合规性。 

条件访问策略决定了大部分你将监视和发出警报的内容。 可以使用[条件访问见解和报告工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)来检查一个或多个条件访问策略对登录的影响以及包括设备状态在内的策略结果。 通过此工作簿，你可以查看影响摘要并识别特定时间段的影响。 还可以使用此工作簿来调查特定用户的登录。 

 本文接下来将按威胁类型介绍我们建议你监视和发出警报的内容。 如果有特定预生成解决方案，我们会链接到这些解决方案，或在表后提供示例。 否则，可以使用前面的工具来生成警报。 

 ## <a name="device-registrations-and-joins-outside-policy"></a>设备注册和加入外部策略

已注册 Azure AD 和已加入 Azure AD 的设备具有主刷新令牌 (PRT)，这相当于单个身份验证因素。 这些设备有时可能包含强身份验证声明。 有关 PRT 何时包含强身份验证声明的信息，请参阅 [PRT 何时获取 MFA 声明](../devices/concept-primary-refresh-token.md)？ 若要阻止不良参与者注册或加入设备，需要使用多重身份验证 (MFA) 来注册或加入设备。 然后，监视未使用 MFA 注册或加入的任何设备。 还需要监视对 MFA 设置和策略以及设备符合性策略所做的更改。

 | 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 未使用 MFA 完成的设备注册或加入| 中等| 登录日志| 活动：成功向设备注册服务进行身份验证。 <br>且<br>不需要 MFA| 发出警报的情况： <br>未使用 MFA 注册或加入的任何设备 |
| 对 Azure AD 中设备注册 MFA 切换开关的更改| 高| 审核日志| 活动：设置设备注册策略| 查找: <br>设置为关闭的切换开关。 没有审核日志条目。 计划定期检查。 |
| 对要求加入域或合规设备的条件访问策略的更改。| 高| 审核日志| 对 CA 策略的更改<br>| 发出警报的情况： <br><li> 对任何要求加入域或合规的策略的更改。<li>对受信任位置的更改。<li> 添加到 MFA 策略例外的帐户或设备。 |


可以在设备未使用 MFA 注册或加入的情况下，通过 Azure Sentinel 创建通知相应管理员的警报。

```
Sign-in logs

| where ResourceDisplayName == “Device Registration Service”

| where conditionalAccessStatus ==”success”

| where AuthenticationRequirement <> “multiFactorAuthentication”
```

还可以[使用 Microsoft Intune 设置和监视设备符合性策略](/mem/intune/protect/device-compliance-get-started)。

## <a name="non-compliant-device-sign-in"></a>不合规的设备登录

如果条件访问策略要求使用兼容的设备，则可能无法阻止对所有云和软件即服务应用程序的访问。 

[移动设备管理](/windows/client-management/mdm/) (MDM) 可帮助Windows 10 设备保持合规。 在 Windows 版本 1809 中，我们发布了策略的[安全基线](/windows/client-management/mdm/)。 Azure Active Directory 可以[与 MDM 集成](/windows/client-management/mdm/azure-active-directory-integration-with-mdm)以强制设备符合公司策略，并可以报告设备的符合性状态。 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 不合规设备的登录| 高| 登录日志| DeviceDetail.isCompliant ==false| 如果需要从合规设备登录，请在以下情况下发出警报：<br><li> 不合规设备的任何登录。<li> 没有使用 MFA 或受信任位置发起的任何访问。<p>如果致力于对设备提出要求，请监视可疑的登录。 |
| 未知设备的登录| 低| 登录日志| <li>DeviceDetail 为空<li>单因素身份验证<li>来自不受信任的位置| 查找: <br><li>任何来自不合规设备的访问。<li>没有使用 MFA 或受信任位置发起的任何访问 |


### <a name="use-loganalytics-to-query"></a>使用 LogAnalytics 进行查询

**不合规设备的登录**

```
SigninLogs

| where DeviceDetail.isCompliant ==false

| where conditionalAccessStatus == “success”
```
 

**未知设备的登录**

```

SigninLogs
| where isempty(DeviceDetail.deviceId)

| where AuthenticationRequirement == "singleFactorAuthentication"

| where ResultType == "0"

| where NetworkLocationDetails == "[]"
```
 
## <a name="stale-devices"></a>陈旧设备

陈旧设备包括未在指定的时间段登录的设备。 当用户获得新设备或设备丢失，或者擦除或重新预配已加入 Azure AD 的设备时，设备可能会变为陈旧设备。 当用户不再与租户关联时，设备也可能保持注册或加入状态。 应删除陈旧设备，以使其主刷新令牌 (PRT) 不再可供使用。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 上次登录日期| 低| 图形 API| approximateLastSignInDateTime| 使用图形 API 或 PowerShell 来识别和删除陈旧设备。 |

## <a name="bitlocker-key-retrieval"></a>BitLocker 密钥检索

入侵用户设备的攻击者可能会检索 Azure AD 中的 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) 密钥。 用户检索密钥并不常见，应进行监视和调查。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 密钥检索| 中等| 审核日志| OperationName == "Read BitLocker key”| 期望 <br><li>密钥检索`<li> 用户检索密钥的其他异常行为。 |


在 LogAnalytics 中，创建如下所示的查询

```
AuditLogs

| where OperationName == "Read BitLocker key” 
```

## <a name="device-administrator-roles"></a>设备管理员角色

全局管理员和云设备管理员会自动获取所有已加入 Azure AD 设备的本地管理员权限。 监视谁拥有这些权利对于保护环境的安全非常重要。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 添加到全局或设备管理员角色的用户| 高| 审核日志| 活动类型 = 将成员添加到角色。| 查找:<li> 添加到这些 Azure AD 角色的新用户。<li> 计算机或用户的后续异常行为。 |


## <a name="non-azure-ad-sign-ins-to-virtual-machines"></a>对虚拟机的非 Azure AD 登录

应监视通过 Azure AD 帐户以外的帐户登录 Windows 或 LINUX 虚拟机 (VM) 的情况。

### <a name="azure-ad-sign-in-for-linux"></a>对 LINUX 的 Azure AD 登录

对 LINUX 的 Azure AD 登录使组织能够通过安全外壳协议 (SSH) 使用 Azure AD 帐户登录 Azure LINUX (VM)。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 非 Azure AD 帐户登录，尤其是通过 SSH 进行的登录| 高| 本地身份验证日志| Ubuntu：  <br>‎监视 /var/log/auth.log 获取 SSH 使用情况<br>RedHat： <br>‎监视 /var/log/sssd/ 获取 SSH 使用情况| 查找:<li> [非 Azure AD 帐户成功连接到 VM](../devices/howto-vm-sign-in-azure-ad-linux.md) 的条目。 <li>请参阅以下示例。 |


Ubuntu 示例：

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]：版本：1.0.015570001；用户：localusertest01

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]：用户“localusertest01”不是 AAD 用户；返回空结果。

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]：版本：1.0.015570001；用户：localusertest01

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]：用户“localusertest01”不是 AAD 用户；返回空结果。

   May 9 23:49:43 ubuntu1804 sshd[3909]：公开接受来自 192.168.0.15 端口 53582 的 localusertest01 ssh2：RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ

   May 9 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session)：(uid=0) 为用户 localusertest01 打开了会话。

可以为 LINUX VM 登录设置策略，并检测和标记添加了未经批准的本地帐户的 Linux VM。 若要了解详细信息，请参阅[使用 Azure Policy 确保标准并评估符合性](../devices/howto-vm-sign-in-azure-ad-linux.md)。 

### <a name="azure-ad-sign-ins-for-windows-server"></a>对 Windows Server 的 Azure AD 登录

对 Windows Server 的 Azure AD 登录使组织能够通过远程桌面协议 (RDP) 使用 Azure AD 帐户登录 Azure Windows 2019+ VM。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 非 Azure AD 帐户登录，尤其是通过 RDP 进行的登录| 高| Windows Server 事件日志| 对 Windows VM 的交互式登录| 事件 528，登录类型 10 (RemoteInteractive)。<br>在用户通过终端服务或远程桌面登录时显示。 |


## <a name="next-steps"></a>后续步骤

请参阅以下其他安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)

 
[基础结构安全操作](security-operations-infrastructure.md)