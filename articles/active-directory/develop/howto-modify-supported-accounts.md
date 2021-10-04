---
title: 如何：更改应用程序支持的帐户类型 | Azure
titleSuffix: Microsoft identity platform
description: 在本操作指南中，你将配置注册到 Microsoft 标识平台的应用程序，以更改可以访问该应用程序的人员或帐户。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 35415a42f6970a7a0aabc0fb210775a15fc1d481
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585439"
---
# <a name="modify-the-accounts-supported-by-an-application"></a>修改应用程序支持的帐户

在 Microsoft 标识平台上注册应用程序时，你指定了可以访问该应用程序的人员或帐户类型。 例如，你可能只在你的组织中指定了帐户，该组织是一个单租户应用。 或者，你可能在任何组织（包括你的组织）中指定了帐户，该组织是一个多租户应用。

在以下各小节中，你将了解如何在 Azure 门户中修改应用的注册，以更改可访问应用程序的用户或帐户类型。

## <a name="prerequisites"></a>先决条件

* [已在你的 Azure AD 租户中注册的应用程序](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>更改应用程序注册以支持不同的帐户

若要为现有应用注册支持的帐户类型指定不同的设置，请执行以下操作：

1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，切换到在其中注册应用的租户。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”下，选择“应用注册”，然后选择你的应用程序 。
1. 现在，指定可使用该应用程序的人员，这有时称为“登录受众”。

    | 支持的帐户类型 | 说明 |
    |-------------------------|-------------|
    | **仅此组织目录中的帐户** | 如果要生成仅供租户中的用户（或来宾）使用的应用程序，请选择此选项。<br><br>通常称为业务线 (LOB) 应用程序，这是 Microsoft 标识平台中的单租户应用程序。 |
    | **任何组织目录中的帐户** | 如果希望任何 Azure AD 租户中的用户都能够使用你的应用程序，请选择此选项。 例如，如果要构建打算向多个组织提供的软件即服务 (SaaS) 应用程序，则适合使用此选项。<br><br>这在 Microsoft 标识平台中被称为多租户应用程序。 |
1. 选择“保存”。

### <a name="why-changing-to-multi-tenant-can-fail"></a>更改为多租户可能会失败的原因

由于应用程序 ID URI （应用 ID URI）名称冲突，将应用注册从单租户切换到多租户有时可能会失败。 应用 ID URI 的示例为 `https://contoso.onmicrosoft.com/myapp`。

应用 ID URI 是在协议消息中标识应用程序的方式之一。 对于单租户应用程序而言，应用 ID URI 仅需在该租户中保持唯一。 对于多租户应用程序而言，该 URI 必须全局唯一，以便 Azure AD 能够在所有租户中找到该应用。 通过要求应用 ID URI 的主机名与 Azure AD 租户的其中一个[已验证发布服务器域](howto-configure-publisher-domain.md)相匹配，来强制实施全局唯一性。

例如，如果租户的名称是 contoso.onmicrosoft.com，则 `https://contoso.onmicrosoft.com/myapp` 是有效的应用 ID URI。 如果租户具有已验证的域 contoso.com，则有效的应用 ID URI 也是 `https://contoso.com/myapp`。 如果应用 ID URI 不遵循第二种模式 `https://contoso.com/myapp`，则将应用注册转换为多租户会失败。

有关配置已验证发布服务器域的详细信息，请参阅[配置已验证的域](howto-configure-publisher-domain.md)。

## <a name="next-steps"></a>后续步骤

详细了解[将应用从单租户转换为多租户](howto-convert-app-to-be-multi-tenant.md)的要求。
