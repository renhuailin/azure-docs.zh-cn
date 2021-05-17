---
title: 创建 Azure Active Directory 应用程序代理应用程序时出现问题
description: 如何排查在 Azure Active Directory 管理门户中创建应用程序代理应用程序时出现的问题
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: a3f60e63d4fb5257d7d822a8e1a9326adf9eb62d
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108186138"
---
# <a name="problem-creating-an-application-proxy-application"></a>创建应用程序代理应用程序时出现问题 

以下是用户在创建新应用程序代理应用程序时遇到的一些常见问题。

## <a name="recommended-documents"></a>建议的文档 

若要了解通过管理门户创建应用程序代理应用程序的详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

如果按照该文档中的步骤操作，但在创建应用程序时出错，请参阅错误详细信息以获取有关如何修复应用程序的信息和建议。 大多数的错误消息都包含建议的修复方法。 

## <a name="specific-things-to-check"></a>检查的具体内容

为避免常见的错误，请验证：

-   是有权创建应用程序代理应用程序的管理员

-   内部 URL 唯一

-   外部 URL 唯一

-   URL 以 http 或 https 开头，以“/”结尾

-   URL 应该是域名，而不是 IP 地址

在创建应用程序时，错误消息应显示在右上角。 还可以选择通知图标以查看错误消息。

   ![通知提示](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>后续步骤
[在 Azure 门户中启用应用程序代理](application-proxy-add-on-premises-application.md)
