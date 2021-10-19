---
title: Azure 市场应用程序的一键式单一登录 (SSO) 配置
description: Azure 市场中的应用程序的一键式 SSO 配置步骤。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: ergreenl
ms.openlocfilehash: 97cdedac91fa80c45ec8b36bad4a1368e48e3e81
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615783"
---
# <a name="one-click-app-configuration-of-single-sign-on-in-azure-active-directory"></a>Azure Active Directory 中的单一登录的一键式应用配置

 在本教程中，你将了解如何从 Azure 市场为支持 SAML 的 Azure Active Directory (Azure AD) 应用程序执行一键式单点登录 (SSO) 配置。

## <a name="introduction-to-one-click-sso"></a>一键式 SSO 介绍

一键式 SSO 功能旨在为支持 SAML 协议的 Azure 市场应用配置单一登录。 在 Azure AD SSO 配置页面上，选择此选项可在应用程序端自动配置 Azure AD 元数据。 通过这种方式，可以用最少的手动工作快速设置 SSO。

## <a name="advantages-of-one-click-sso"></a>一键式 SSO 的优点

- 快速配置 Azure 市场应用程序的 SSO（客户必须先在应用程序端进行手动设置）
- 更有效、更准确的 SSO 配置。
- 设置时无需合作伙伴通信或支持。 应用程序会提供用于配置 SAML 的 UI。

## <a name="prerequisites"></a>先决条件

- 用于配置 SSO 的应用程序的有效订阅。 还需要管理员凭据。
- 在浏览器中安装 Microsoft 所提供的“我的应用安全登录扩展”。 有关详细信息，请参阅[在“我的应用”门户中访问和使用应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="one-click-sso-configuration-steps"></a>一键式 SSO 配置步骤

1. 从 Azure 市场添加应用程序。

2. 选择“单一登录”。

3. 选择“启用单一登录”。

4. 在“基本 SAML 配置”部分填充必需的配置值。

    > [!NOTE]
    > 如果应用程序需要配置自定义声明，请在执行一键式 SSO 前处理进行配置。

5. 如果 Azure 市场应用程序可以使用一键式 SSO 功能，则会看到以下屏幕。 可能必须通过选择“安装扩展程序”来安装“我的应用安全登录浏览器扩展” 。

   ![安装“我的应用”安全登录浏览器扩展](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. 将扩展添加到浏览器后，选择“设置 \<Application Name\>”。 重定向到应用程序管理门户后，以管理员身份登录。

   ![设置应用程序名称](./media/one-click-sso-tutorial/setup-sso.png)

7. 浏览器扩展会自动在应用程序上配置 SSO。 选择“是”确认。

   ![保存自动填充的数据](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > 如果应用程序的 SSO 配置需要额外的步骤，请按照提示执行这些步骤。

8. 完成配置后，选择“确定”保存更改。

   ![保存自动填充的数据](./media/one-click-sso-tutorial/save-data.png)

9. 系统将显示一个确认窗口，通知你已成功配置 SSO 设置。

   ![已配置 SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 配置成功之后，你将从应用程序中注销并返回到 Azure 门户。

11. 可以选择“测试”来测试单一登录。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](../saas-apps/tutorial-list.md)
- [什么是我的应用安全登录浏览器扩展？](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)