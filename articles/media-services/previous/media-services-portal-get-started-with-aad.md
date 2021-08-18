---
title: 通过 Azure 门户开始使用 Azure AD 身份验证 | Microsoft Docs
description: 了解如何使用 Azure 门户访问 Azure Active Directory (Azure AD) 身份验证设置，以便使用 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6dee5dd57e233656f8ec160656b7c67b744d4c86
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712525"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>通过 Azure 门户开始使用 Azure AD 身份验证

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

了解如何使用 Azure 门户访问 Azure Active Directory (Azure AD) 身份验证设置，以便使用 Azure 媒体服务 API。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，请从 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)入手。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。

将 Azure AD 身份验证与 Azure 媒体服务结合使用时，可以选择下列两个身份验证选项：

- **服务主体身份验证**。 对服务进行身份验证。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用程序：Web 应用程序、函数应用程序、逻辑应用程序、API 或微服务。
- **用户身份验证**。 验证使用应用程序与媒体服务资源进行交互的用户。 交互式应用程序应先提示用户输入凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 

## <a name="access-the-media-services-api"></a>访问媒体服务 API

可以通过此页选择要用来连接 API 的身份验证方法。 该页面还提供了连接到 API 所需的值。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择媒体服务帐户。
2. 选择与媒体服务 API 的连接方式。
3. 在“连接到媒体服务 API”下，选择要连接到的媒体服务 API 版本。

## <a name="service-principal-authentication--recommended"></a>服务主体身份验证（推荐）

使用 Azure Active Directory (Azure AD) 应用和密钥对服务进行身份验证。 建议将它用于对媒体服务 API 的任何中间层服务调用， 例如 Web 应用、Functions、逻辑应用、API 和微服务。 这是推荐的身份验证方法。

### <a name="manage-your-azure-ad-app-and-secret"></a>管理 Azure AD 应用和密钥

通过“理 AAD 应用和密钥”部分，可选择或创建新的 Azure AD 应用并生成密钥。 出于安全原因，边栏选项卡关闭后密钥无法显示。 应用程序使用应用程序 ID 和密钥进行验证，以便为媒体服务获取有效的令牌。

请确保你拥有足够的权限，可向 Azure AD 租户注册应用程序，并将应用程序分配给 Azure 订阅中的角色。 有关详细信息，请参阅[所需的权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

在“连接到媒体服务 API”页面，可查看用于连接服务主体应用程序的值。 可获取文本值，也可复制 JSON 或 XML 块。

## <a name="user-authentication"></a>用户身份验证

此选项可用于对使用应用与媒体服务资源进行交互的 Azure Active Directory 的员工或成员进行身份验证。 交互式应用程序应先提示用户输入用户凭据。 这种身份验证方法应仅用于管理应用程序。

### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

从“连接到媒体服务 API”部分复制凭据来连接用户应用程序。 可获取文本值，也可复制 JSON 或 XML 块。

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-portal-upload-files.md)。
