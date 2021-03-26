---
title: 快速入门：创建一个 Azure Active Directory 租户
titleSuffix: Microsoft identity platform
description: 本快速入门介绍如何创建 Azure Active Directory 租户，以便在开发使用 Microsoft 标识平台进行身份验证和授权的应用程序时使用。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535155"
---
# <a name="quickstart-set-up-a-tenant"></a>快速入门：设置租户

若要构建使用 Microsoft 标识平台进行标识和访问管理的应用，需要访问 Azure Active Directory (Azure AD) 租户。 你可在 Azure AD 租户中注册和管理应用、配置这些应用对 Microsoft 365 和其他 Web API 中数据的访问权限，还可在这里启用条件访问等功能。

租户代表组织。 它是组织或应用开发人员在与 Microsoft 建立关系之初收到的 Azure AD 的专用实例。 例如，可通过注册 Azure、Microsoft Intune 或 Microsoft 365 来开启这种关系。

每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户。 它使用自己的工作和学校标识、使用者标识（如果是 Azure AD B2C 租户）和应用注册进行表示。 仅可通过你的租户或所有租户中的帐户对你的租户中的应用注册进行身份验证。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="determining-the-environment-type"></a>确定环境类型

可创建两种类型的环境。 环境仅取决于你的应用将进行身份验证的用户类型。 

本快速入门介绍了你要构建的应用类型的两种适用方案：

* 工作和学校 (Azure AD) 帐户，或者 Microsoft 帐户（例如 Outlook.com 和 Live.com）
* 社交和本地 (Azure AD B2C) 帐户

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>工作和学校帐户或个人 Microsoft 帐户

若要为工作和学校帐户或 Microsoft 个人帐户生成环境，可使用现有 Azure AD 租户或新建一个租户。
### <a name="use-an-existing-azure-ad-tenant"></a>使用现有的 Azure AD 租户

许多开发人员已通过绑定到 Azure AD 租户的服务或订阅（例如 Microsoft 365 或 Azure 订阅）获得了租户。

若要检查租户：

1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。 使用将用于管理应用程序的帐户。
1. 查看右上角。 如果有租户，则会自动登录。 你会在帐户名称下直接看到租户名称。
   * 将鼠标悬停在帐户名称上可查看你的姓名、电子邮件地址、目录或租户 ID (GUID) 和域。
   * 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。

> [!TIP]
> 如需查找租户 ID，可以：
> * 将鼠标悬停在帐户名称上来获取目录或租户 ID。
> * 在 Azure 门户中，搜索“Azure Active Directory” > “属性” > “租户 ID”并将其选中  。

如果没有与帐户关联的租户，那么你的帐户名称下会显示一个 GUID。 创建 Azure AD 租户之前，你将无法执行注册应用之类的操作。

### <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

如果还没有 Azure AD 租户，或者想要新建一个来进行开发，请查看[在 Azure AD 中创建新租户](../fundamentals/active-directory-access-create-new-tenant.md)。 或者，使用 Azure 门户中的[目录创建体验](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)。 

你要提供以下信息来创建新租户：

- 组织名称
- **初始域** - 此域是 *.onmicrosoft.com 的一部分。 稍后可对该域进行自定义。
- 国家或地区

> [!NOTE]
> 对租户进行命名时，请使用字母数字字符。 不允许使用特殊字符。 名称不得超过 256 个字符。

## <a name="social-and-local-accounts"></a>社交和本地帐户

若要开始构建用于登录社交帐户和本地帐户的应用，需要创建一个 Azure AD B2C 租户。 若要开始，请查看[创建 Azure AD B2C 租户](../../active-directory-b2c/tutorial-create-tenant.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [注册应用](quickstart-register-app.md)以与 Microsoft 标识平台集成。
