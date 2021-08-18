---
title: 登录到应用程序时出现的意外许可提示 | Microsoft Docs
description: 如何对用户遇到的与 Azure AD 集成的应用程序出现意外许可提示这一情形进行故障排除
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
ms.openlocfilehash: 81640356eb60167e8cdefc67b962bf7f38783556
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738875"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>登录到应用程序时出现的意外许可提示

许多与 Azure Active Directory 集成的应用程序都需要各种资源的权限才能运行。 当这些资源也与 Azure Active Directory 集成时，使用 Azure AD 许可框架请求其访问权限。

这会导致首次使用应用程序时显示许可提示，这通常是一次性操作。

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>用户看到许可提示的情况

可能在各种情况下看到其他提示：

* 应用程序已配置为需要分配。 需要分配的应用目前不支持用户同意。 如果将应用程序配置为需要分配，还务必要授予租户范围的管理员同意，这样，分配的用户才能够登录。

* 应用程序所需的权限集已更改。

* 最初向应用程序授予同意的用户并不是管理员，现在，其他（非管理员）用户在首次使用该应用程序。

* 最初对应用程序进行许可的用户是管理员，但他们未代表整个组织进行许可。

* 最初授予许可后，应用程序使用[增量许可和动态许可](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)请求其他权限。 这通常会在以下情况下使用：应用程序的可选功能需要超出基线功能所需权限以外的附加权限。

* 已在最初授予许可后将其吊销。

* 开发人员已对应用程序作了如下配置：每次使用时，都需要许可提示（注意：这并非最佳做法）。

   > [!NOTE]
   > 许多组织已经按照 Microsoft 的建议和最佳做法禁用或限制了用户向应用授予同意的权限。 如果应用程序强制用户在每次登录时都授予同意，将会阻止大多数用户使用这些应用程序，即使管理员授予租户范围的管理员同意。 如果遇到在已经授予管理员同意后仍要求用户同意的应用程序，请咨询应用发布者，以了解他们是否有设置或选项可用于停止强制用户在每次登录时都同意。

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory（v1.0 终结点）中的应用、权限和许可](../develop/quickstart-register-app.md)

* [Azure Active Directory（v2.0 终结点）中的范围、权限和许可](../develop/v2-permissions-and-consent.md)
