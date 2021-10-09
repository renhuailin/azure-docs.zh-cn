---
title: 向应用程序添加基于密码的单一登录
description: 在 Azure Active Directory 中添加基于密码的应用程序单一登录。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 6ed7241e62b037a2d0ebd5303bd18e3c859d93f0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639653"
---
# <a name="add-password-based-single-sign-on-to-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中添加基于密码的应用程序单一登录

本文介绍如何在 Azure Active Directory (Azure AD) 中设置基于密码的单一登录 (SSO)。 使用基于密码的 SSO 时，用户在首次访问应用程序时使用用户名和密码登录。 首次登录后，Azure AD 会为应用程序发送用户名和密码。 

基于密码的 SSO 使用应用程序提供的现有身份验证过程。 为应用程序启用基于密码的 SSO 时，Azure AD 会收集应用程序的用户名和密码并将其安全地进行存储。 用户凭据以加密状态存储在目录中。 任何基于云且具有基于 HTML 的登录页面的应用程序都支持基于密码的 SSO。

遇到以下情况时，请选择基于密码的 SSO：
- 应用程序不支持 SAML SSO 协议。
- 应用程序使用用户名和密码而非访问令牌和标头进行身份验证。

基于密码的 SSO 的配置页非常简单。 配置页仅包含应用程序使用的登录页的 URL。 此字符串必须是包含“用户名”输入字段的页面。

## <a name="prerequisites"></a>先决条件

若要在 Azure AD 租户中配置基于密码的 SSO，你需要：
-   具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
-   一个支持基于密码的 SSO 的应用程序。

## <a name="configure-password-based-single-sign-on"></a>配置基于密码的单一登录

1.  使用适当的角色登录到 [Azure 门户](https://portal.azure.com)。
1.  在 Azure 服务中选择“Azure Active Directory”，然后选择“企业应用程序” 。
1.  搜索并选择要添加基于密码的 SSO 的应用程序。
1.  选择“单一登录”，然后选择“基于密码” 。
1.  输入应用程序的登录页的 URL。
1.  选择“保存”。 

Azure AD 分析登录页的 HTML 以获得用户名和密码输入字段。 如果尝试成功，则已完成。 下一步是为应用程序[分配用户或组](add-application-portal-assign-users.md)。 

分配用户和组后，你可以提供凭据，以便在用户登录到应用程序时使用。 

1. 选择“用户和组”，选中用户或组所在行的复选框，然后选择“更新凭据”。 
1. 输入要用于该用户或组的用户名和密码。 否则，系统会在启动时提示用户自行输入凭据。

## <a name="manual-configuration"></a>手动配置

如果 Azure AD 的分析尝试失败，你可以手动配置登录。

1. 选择“配置 {应用程序名称} 密码单一登录设置”以显示“配置登录”页。 
1. 选择“手动检测登录字段”。 此时会显示描述如何手动检测登录字段的更多说明。
1. 选择“捕获登录字段”。 此时将在新选项卡中打开“捕获状态”页，显示消息“元数据捕获当前正在进行”。
1. 如果新选项卡中显示了“所需的我的应用扩展”框，请选择“立即安装”以安装“我的应用安全登录扩展”浏览器扩展。 （浏览器扩展要求使用的浏览器是 Microsoft Edge、Chrome 或 Firefox。）接下来，安装、启动和启用该扩展，并刷新捕获状态页。 然后，浏览器扩展将打开一个显示所输入 URL 的选项卡。
1. 在包含所输入 URL 的选项卡中，完成登录过程。 填写“用户名”和“密码”字段，然后尝试登录。 （不必提供正确的密码。）会出现一个提示，要求你保存捕获的登录字段。
1. 选择“确定”。 浏览器扩展更新“捕获状态”页，其中包含“应用程序已更新元数据”的消息。 浏览器选项卡关闭。
1. 在 Azure AD 的“配置登录”页上，选择“确定，我可以成功登录到应用”。
1. 选择“确定”  。

## <a name="next-steps"></a>后续步骤

- [管理对应用的访问权限](what-is-access-management.md)
