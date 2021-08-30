---
title: 为 DICOM 服务注册 Azure Active Directory 应用程序 - Azure Healthcare APIs
description: 本文介绍如何为 DICOM 服务注册 Azure Active Directory 应用程序。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1db5ba11d4a3bd8380561ae493c69b809edf5135
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778469"
---
# <a name="register-azure-active-directory-applications-for-the-dicom-service"></a>为 DICOM 服务注册 Azure Active Directory 应用程序

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

设置 DICOM 服务或适用于 Azure 的 FHIR 服务器 (OSS) 时，有多个选项可供选择。 对于开放源代码，你需要创建自己的资源应用程序注册。 对于Azure API for FHIR，此资源应用程序会自动创建。

## <a name="application-registrations"></a>应用程序注册

为了使应用程序能够与 Azure AD 进行交互，必须对其进行注册。 下面介绍 DICOM 服务上下文中的三种类型的客户端应用程序注册。

## <a name="client-applications"></a>客户端应用程序

客户端应用程序是请求令牌的客户端的注册。 在 OAuth 2.0 中，我们通常会区分至少三种不同类型的应用程序。

### <a name="confidential-clients"></a>机密客户端

机密客户端也称为 Azure AD 中的 Web 应用。 机密客户端是使用[授权代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)代表提供有效凭据的已登录用户获取令牌的应用程序。 之所以将其称作机密客户端，是因为它们可以保存机密，并在使用身份验证码交换令牌时向 Azure AD 提供此机密。 由于机密客户端可以使用客户端机密进行自我身份验证，因此它们比公共客户端更可信，可以包含生存期更长的令牌，并可为其授予刷新令牌。 有关详细信息，请参阅[在 Azure Active Directory 中注册机密客户端应用程序](dicom-register-confidential-client-application.md)。 必须注册回复 URL，客户端将通过此 URL 接收授权代码。

### <a name="public-clients"></a>公共客户端

这些客户端无法保留机密。 通常，此客户端用于移动设备应用程序或单页 JavaScript 应用程序，客户端中的机密可由用户发现。 公共客户端也使用授权代码流，但在获取令牌时它们不能提供机密，另外，它们的令牌的生存期较短，并且没有刷新令牌。 详细了解如何[在 Azure Active Directory 中注册公共客户端应用程序](dicom-register-public-application.md)。

### <a name="service-clients"></a>服务客户端

这些客户端使用[客户端凭据流](.././../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)代表自身（而不是代表用户）获取令牌。 它们通常代表以非交互方式访问 DICOM 服务的应用程序， 例如，一个引入进程。 使用服务客户端时，不需要通过调用 /authorize 终结点启动获取令牌的进程。 服务客户端可以直接转到 /token 终结点，并提供客户端 ID 和客户端机密来获取令牌。 有关详细信息，请参阅[在 Azure Active Directory 中注册服务客户端应用程序](dicom-register-service-client-application.md)。

## <a name="next-steps"></a>后续步骤

本概述文章已引导你完成资源和客户端应用程序的应用程序注册过程，使你能够使用 DICOM 服务。 注册应用程序后，可以部署 DICOM 服务。

>[!div class="nextstepaction"]
>[在 Azure Active Directory 中注册机密客户端应用程序](dicom-register-confidential-client-application.md)

>[!div class="nextstepaction"]
>[在 Azure Active Directory 中注册公共客户端应用程序](dicom-register-public-application.md)

>[!div class="nextstepaction"]
>[在 Azure Active Directory 中注册服务客户端应用程序](dicom-register-service-client-application.md)