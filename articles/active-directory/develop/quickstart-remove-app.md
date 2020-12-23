---
title: 如何：从 Microsoft 标识平台删除已注册的应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本文中，你将了解如何删除注册到 Microsoft 标识平台的应用程序。
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
ms.openlocfilehash: edfed8ca361d456e622096e23079d5887f96ab30
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452987"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>如何删除注册到 Microsoft 标识平台的应用程序

已经向 Microsoft 标识平台注册了应用程序的企业开发人员和软件即服务 (SaaS) 提供商可能需要删除应用程序的注册。

在以下各小节中，你将了解如何实现以下操作：

* 删除你或你的组织编写的应用程序
* 删除其他组织编写的应用程序

## <a name="prerequisites"></a>先决条件

* [已在你的 Azure AD 租户中注册的应用程序](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>删除你或你的组织编写的应用程序

由你或你的组织注册的应用程序是由租户中的应用程序对象与服务主体对象表示的。 有关详细信息，请参阅[应用程序对象和服务主体对象](./app-objects-and-service-principals.md)。

若要删除应用程序，你必须是应用程序的所有者或者具有管理员权限。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  ，然后选择“应用注册”。  找到并选择要配置的应用程序。 选择应用以后，会看到应用程序的“概览”页。 
1. 在“概览”  页中，选择“删除”  。
1. 选择“是”  以确认要删除该应用。

## <a name="remove-an-application-authored-by-another-organization"></a>删除其他组织编写的应用程序

如果在租户上下文中查看“应用注册”，则会发现在“所有应用”选项卡下显示的应用程序子集来自另一个租户，并已在许可过程中注册到你的租户。   更具体地说，它们仅由租户中的服务主体对象表示，没有相应的应用程序对象。 有关应用程序对象与服务主体对象之间的差别的详细信息，请参阅 [Azure AD 中的应用程序对象和服务主体对象](./app-objects-and-service-principals.md)。

若要删除应用程序对目录的访问权限（在授予许可后），公司管理员必须删除该应用程序的服务主体。 管理员必须拥有全局管理员访问权限，并且可以通过 Azure 门户删除应用程序或使用 [Azure AD PowerShell Cmdlet](/previous-versions/azure/jj151815(v=azure.100)) 删除访问权限。

## <a name="next-steps"></a>后续步骤

详细了解 Microsoft 标识平台中的[应用程序和服务主体对象](app-objects-and-service-principals.md)。
