---
title: 将 Azure AD 应用限制为仅供一组用户使用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将在 Azure AD 中注册的应用限制为仅供所选的一组用户访问。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d36d3dca584499e5bc9508c99485507b198159c6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123036422"
---
# <a name="restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>将 Azure AD 应用限制为仅供 Azure AD 租户中的一组用户使用

默认情况下，在 Azure Active Directory (Azure AD) 租户中注册的应用程序可供租户的所有已成功进行身份验证的用户使用。

类似地，在使用[多租户](howto-convert-app-to-be-multi-tenant.md)应用时，如果此应用在 Azure AD 租户中预配，则该租户中的所有用户在相应的租户中成功进行身份验证以后，都能够访问此应用程序。

租户管理员和开发人员通常会要求一个应用只能供特定的一组用户使用。 开发人员可以使用 Azure 基于角色的访问控制 (Azure RBAC) 之类的常用授权模式来完成此操作，但这种方法要求开发人员完成大量的工作。

租户管理员和开发人员也可以使用 Azure AD 的此内置功能，将应用限制为仅供租户中特定的一组用户或安全组使用。

## <a name="supported-app-configurations"></a>支持的应用配置

将应用限制为仅供租户中特定的一组用户或安全组使用这一选项适用于以下类型的应用程序：

- 经配置后可以使用基于 SAML 的身份验证进行联合单一登录的应用程序。
- 使用 Azure AD 预身份验证的应用程序代理应用程序。
- 直接在 Azure AD 应用程序平台上生成且使用 OAuth 2.0/OpenID Connect 身份验证的应用程序（前提是用户或管理员已认可该应用程序）。

     > [!NOTE]
     > 此功能仅适用于 Web 应用/Web API 和企业应用程序。 注册为[本机](./quickstart-register-app.md)的应用不能只限租户中的一组用户或安全组使用。

## <a name="update-the-app-to-require-user-assignment"></a>更新应用以要求用户分配

若要更新应用程序以要求用户分配，你必须是 Enterprise 应用下的应用程序所有者，或者分配有全局管理员、应用程序管理员或云应用程序管理员目录角色  。

1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，选择要在其中注册应用程序的租户。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”下，选择“企业应用程序” > “所有应用程序”  。
1. 选择要配置为要求分配的应用程序。 使用窗口顶部的筛选器搜索特定应用程序。
1. 在应用程序的“概述”页上的“管理”下，选择“属性”  。
1. 找到设置“需要进行用户分配?”，将其设置为“是”。  当此选项设置为“是”时，必须先为尝试访问应用程序或服务的用户和服务分配此应用程序，否则这些用户和服务将无法登录或获取访问令牌。
1. 选择“保存”。

> [!NOTE]
> 当应用程序要求分配时，不允许用户同意该应用程序。 即使允许用户同意该应用，也是如此。 请务必对要求分配的应用[授予租户范围的管理员同意](../manage-apps/grant-admin-consent.md)。 

## <a name="assign-the-app-to-users-and-groups"></a>将应用分配给用户和组

将应用配置为启用用户分配后，即可继续操作，将应用分配给用户和组。

1. 在“管理”下，选择“用户和组” > “添加用户/组”  。
1. 选择“用户”选择器。 

     将会显示用户和安全组的列表和一个文本框，后者用于搜索和查找特定用户或组。 此屏幕允许一次选择多个用户和组。

1. 选择用户和组后，选择“选择”。
1. （可选）如果已在应用程序中定义了应用角色，则可以使用“选择角色”选项将应用角色分配给选定的用户和组。 
1. 选择“分配”以完成将应用分配给用户和组的操作。 
1. 确认已添加的用户和组显示在更新的“用户和组”列表中。

## <a name="more-information"></a>详细信息

- [如何：在应用程序中添加应用角色](./howto-add-app-roles-in-azure-ad-apps.md)
- [使用应用角色和角色声明向 ASP.NET Core Web 应用添加授权](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=LRoc-na27l0)（在应用中使用安全组和应用程序角色（视频））
- [Azure Active Directory 现在可以与组声明和应用程序角色配合使用](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 应用清单](./reference-app-manifest.md)
