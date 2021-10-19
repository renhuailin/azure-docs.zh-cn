---
title: 对应用程序执行许可时发生的意外错误
titleSuffix: Azure AD
description: 介绍对应用程序进行许可期间可能发生的错误，以及解决这些错误的方法
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bb824f9527619033467594a98310663c1113fdc
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620728"
---
# <a name="unexpected-error-when-performing-consent-to-an-application-in-azure-active-directory"></a>对 Azure Active Directory 中的应用程序授予同意时发生意外错误

本文介绍对应用程序进行许可期间可能发生的错误。 如果要对不包含任何错误消息的意外许可提示进行故障排除，请参阅 [Azure AD 的身份验证方案](../develop/authentication-vs-authorization.md)。

与 Azure Active Directory 集成的许多应用程序需要获取访问其他资源的权限才能正常工作。 当这些资源也与 Azure Active Directory 集成时，通常使用通用许可框架来请求访问它们的权限。 这会显示许可提示，此情形通常发生在首次使用应用程序时，但也可能发生在后续使用应用程序时。

对于用户而言，必须满足一些条件才能对应用程序所需的权限进行许可。 如果不满足这些条件，可能会发生以下错误。

## <a name="requesting-not-authorized-permissions-error"></a>请求未授予的权限错误

* **AADSTS90093：** &lt;clientAppDisplayName&gt; 正在请求一个或多个你无权授予的权限。 请与管理员联系，他/她可代表你对此应用程序进行许可。
* AADSTS90094：  &lt;clientAppDisplayName&gt; 需要访问组织中资源的权限（只有管理员可以授予）。 使用应用前，请先向管理员请求授予此应用的权限。

当非全局管理员用户尝试使用的应用程序请求只有管理员才能授予的权限时，会发生此错误。 此错误可通过管理员代表其组织授予访问此应用程序的权限进行解决。

如果 Microsoft 检测到权限请求存在风险而阻止用户许可应用程序，则也可能会发生此错误。 在这种情况下，还将记录一个审核事件，其“类别”为“ApplicationManagement”、“活动类型”为“许可应用程序”、“状态原因”为“检测到风险应用程序”。

可能发生此错误的另一种情况是，应用程序需要用户分配，但未获得管理员同意。 在这种情况下，必须先获得管理员同意。

## <a name="policy-prevents-granting-permissions-error"></a>策略阻止权限授予错误

* **AADSTS90093：** &lt;tenantDisplayName&gt; 管理员设置的策略阻止授予 &lt;name of app&gt; 请求的权限。 请与 &lt;tenantDisplayName&gt; 管理员联系，他/她可代表你授予对此应用的权限。

当全局管理员关闭用户对应用程序进行许可的能力，非管理员用户尝试使用的应用程序需要许可时，会发生此错误。 此错误可通过管理员代表其组织授予访问此应用程序的权限进行解决。

## <a name="intermittent-problem-error"></a>不稳定问题错误

* **AADSTS90090：** 登录过程似乎遇到了间歇性问题，它记录了尝试授予 &lt;clientAppDisplayName&gt; 的权限。 请稍后重试。

此错误表明服务端已发生不稳定问题。 可通过重新尝试对应用程序进行许可来解决此问题。

## <a name="resource-not-available-error"></a>资源不可用错误

* **AADSTS65005：** &lt;clientAppDisplayName&gt; 应用已请求访问不可用的 &lt;resourceAppDisplayName&gt; 资源的权限。

请与应用程序开发人员联系。

## <a name="resource-not-available-in-tenant-error"></a>租户中资源不可用错误

* **AADSTS65005：** &lt;clientAppDisplayName&gt; 正在请求访问你的组织 &lt;tenantDisplayName&gt; 中没有的 &lt;resourceAppDisplayName&gt; 资源。

确保此资源可用，或与 &lt;tenantDisplayName&gt; 管理员联系。

## <a name="permissions-mismatch-error"></a>权限不匹配错误

* **AADSTS65005：** 应用已请求对访问 &lt;resourceAppDisplayName&gt; 资源进行许可。 由于此请求与应用注册期间预配置应用的方式不匹配，该请求失败。 与应用供应商联系。**

在以下情况下会发生所有这些错误：用户尝试许可的应用程序请求对资源应用程序的访问权限，该资源应用程序在组织目录（租户）中找不到。 出现这种情况的原因可能有很多个：

* 客户端应用程序开发人员错误地配置了应用程序，从而导致应用程序请求访问无效的资源。 在这种情况下，为了解决此问题，应用程序开发人员必须更新客户端应用程序的配置。

* 代表目标资源应用程序的服务主体在组织中不存在，或过去存在但已删除。 若要解决此问题，必须在组织中预配资源应用程序的服务主体，以便客户端应用程序可以请求其权限。 可通过多种方式配置服务主体，具体取决于应用程序的类型，包括：

* 获取资源应用程序（Microsoft 已发布的应用程序）的订阅

* 对资源应用程序进行许可

* 通过 Azure 门户授予应用程序权限

* 从 Azure AD 应用程序库添加应用程序

## <a name="risky-app-error-and-warning"></a>关于有风险的应用的错误和警告

* **AADSTS900941:** 管理员同意是必需的。 应用被视为有风险。 (AdminConsentRequiredDueToRiskyApp)
* 这个应用可能有风险。 如果你信任此应用，请让管理员授予你访问权限。
* **AADSTS900981:** 有风险的应用收到一个管理员同意请求。 (AdminConsentRequestRiskyAppWarning)
* 这个应用可能有风险。 仅当你信任此应用时继续。

当 Microsoft 确定同意请求可能存在风险时，将显示这两条消息。 在许多其他因素中，如果未将[经过验证的发布者](../develop/publisher-verification-overview.md)添加到应用注册中，则可能会发生这种情况。 如果禁用[管理员同意工作流](configure-admin-consent-workflow.md)，则将向最终用户显示第一个错误代码和消息。 如果启用管理员同意工作流，则将向最终用户和管理员显示第二个代码和消息。

最终用户将无法对检测为有风险的应用授予许可。 管理员可以评估应用，但必须谨慎行事。 如果该应用在进一步审查后看起来可疑，可以从同意屏幕向 Microsoft 报告。

## <a name="next-steps"></a>后续步骤

[Azure Active Directory（v1 终结点）中的应用、权限和许可](../develop/quickstart-register-app.md)<br>

[Azure Active Directory（v2.0 终结点）中的范围、权限和许可](../develop/v2-permissions-and-consent.md)
