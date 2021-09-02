---
title: 查看 Microsoft Azure Maps 的身份验证详细信息
description: 使用 Azure 门户查看 Azure Maps 的身份验证详细信息。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: ccc0e8c43001f161648032be7fbb83aec43cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802263"
---
若要在 Azure 门户中查看 Azure Maps 帐户身份验证详细信息：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 导航到 Azure 门户菜单。 选择“所有资源”，然后选择你的 Azure Maps 帐户。

3. 在左侧窗格中的“设置”下，选择“身份验证” 。

    :::image type="content" border="true" source="../media/how-to-manage-authentication/view-authentication-keys.png" alt-text="身份验证详细信息。":::

创建 Azure 地图帐户时，将创建三个值。 这些值将用于在 Azure Maps 中支持两种类型的身份验证：
- Azure Active Directory 身份验证：`Client ID` 表示要用于 REST API 请求的帐户。 `Client ID` 值应存储在应用程序配置中，然后应在向使用 Azure AD 身份验证的 Azure Maps 发出 HTTP 请求之前检索该值。
- 共享密钥身份验证：`Primary Key` 和 `Secondary Key` 用作共享密钥身份验证的订阅密钥。 共享密钥身份验证依赖于将 Azure Maps 帐户生成的密钥连同每个请求一起传递到 Azure Maps。 建议定期重新生成密钥。 为在重新生成期间保持当前连接，有两个密钥可供使用。 重新生成另一个密钥期间，可使用其中一个密钥。 重新生成密钥时，必须将访问此帐户的所有应用程序更新为使用新密钥。 有关详细信息，请参阅[向 Azure Maps 进行身份验证](../azure-maps-authentication.md)
