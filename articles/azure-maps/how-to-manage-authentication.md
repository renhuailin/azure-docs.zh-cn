---
title: 管理身份验证
titleSuffix: Azure Maps
description: 熟悉 Azure Maps 身份验证。 了解哪种方法最适合哪种方案。 了解如何使用门户查看身份验证设置。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864068"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户后，将创建客户端 ID 和密钥，用于支持 Azure Active Directory (Azure AD) 身份验证和共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 当[使用共享密钥身份验证以调用 Azure Maps](./azure-maps-authentication.md#shared-key-authentication) 时，建议使用主密钥作为订阅密钥。 在滚动密钥更改等方案下，可以使用辅助密钥。 有关详细信息，请参阅 [Azure Maps 中的身份验证](./azure-maps-authentication.md)。

可以在 Azure 门户中查看身份验证详细信息。 在帐户对应的“设置”菜单上，选择“身份验证” 。

> [!div class="mx-imgBorder"]
> ![身份验证详细信息](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>发现类别和方案

根据应用程序的需要，有特定的路径来保护应用程序的安全。 Azure AD 定义了多种类别，以支持各种身份验证流。 请参阅[应用程序类别](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories)，了解应用程序适合的类别。

> [!NOTE]
> 即使是使用共享密钥身份验证，了解类别和方案也有助于保护应用程序的安全。

## <a name="determine-authentication-and-authorization"></a>确定身份验证和授权

下表概述了 Azure Maps 中常见的身份验证和授权方案。 该表提供了每个方案所提供的保护类型的对比。

> [!IMPORTANT]
> Microsoft 建议对生产应用程序实施具有 Azure 基于角色的访问控制 (Azure RBAC) 的 Azure Active Directory (Azure AD)。

| 场景                                                                                    | 身份验证 | 授权 | 开发工作 | 运营工作 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [可信守护程序/非交互式客户端应用程序](./how-to-secure-daemon-app.md)        | 共享密钥     | 不可用           | 中型             | 高               |
| [可信守护程序/非交互式客户端应用程序](./how-to-secure-daemon-app.md)        | Azure AD       | 高          | 低                | 中             |
| [具有交互式单一登录的 Web 单页面应用程序](./how-to-secure-spa-users.md) | Azure AD       | 高          | 中等             | 中             |
| [具有非交互式登录的 Web 单页面应用程序](./how-to-secure-spa-app.md)      | Azure AD       | 高          | 中等             | 中             |
| [具有交互式单一登录的 Web 应用程序](./how-to-secure-webapp-users.md)          | Azure AD       | 高          | 高               | 中等             |
| [IoT 设备/输入受限设备](./how-to-secure-device-code.md)                     | Azure AD       | 高          | 中等             | 中             |

单击表中的链接，将转到每个方案对应的详细配置信息。

## <a name="view-role-definitions"></a>查看角色定义

若要查看可用于 Azure Maps 的 Azure 角色，请转到 **访问控制 (IAM)** 。 选择“角色”，然后搜索以“Azure Maps”开头的角色。 这些 Azure Maps 角色是可以向其授予访问权限的角色。

> [!div class="mx-imgBorder"]
> ![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>查看角色分配

若要查看已为 Azure Maps 授予访问权限的用户和应用，请转到 **访问控制 (IAM)** 。 在此处选择“角色分配”，然后按“Azure Maps”进行筛选。

> [!div class="mx-imgBorder"]
> ![查看已被授予访问权限的用户和应用](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

从 Azure AD 令牌终结点请求令牌。 在 Azure AD 请求中，使用以下详细信息：

| Azure 环境      | Azure AD 令牌终结点             | Azure 资源 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 公有云     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 政府版云 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

有关从 Azure AD 为用户和服务主体请求访问令牌的详细信息，请参阅 [Azure AD 的身份验证方案](../active-directory/develop/authentication-vs-authorization.md)并查看[方案](./how-to-manage-authentication.md#determine-authentication-and-authorization)表中的特定方案。

## <a name="manage-and-rotate-shared-keys"></a>管理和轮换共享密钥

Azure Maps 订阅密钥类似于 Azure Maps 帐户的根密码。 始终要小心保护订阅密钥。 使用 Azure 密钥保管库安全地管理和轮换密钥。 避免将访问密钥分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他人可以访问的任何位置。 如果你认为访问密钥可能已泄露，请轮换密钥。

> [!NOTE]
> Microsoft 建议在可能的情况下，应使用 Azure Active Directory (Azure AD) 代替共享密钥来为请求授权。 Azure AD 通过共享密钥提供更高的安全性和易用性。

### <a name="manually-rotate-subscription-keys"></a>手动轮换订阅密钥

Microsoft 建议定期轮换订阅密钥，这有助于确保 Azure Maps 帐户安全。 如果可能，请使用 Azure 密钥保管库管理访问密钥。 如果不使用密钥保管库，将需要手动轮换密钥。

分配了两个订阅密钥，以便轮换使用。 拥有两个密钥可确保应用程序在整个过程中能够持续访问 Azure Maps。

若要在 Azure 门户中轮换 Azure Maps 订阅密钥，请执行以下操作：

1. 更新应用程序代码以引用 Azure Maps 帐户的辅助密钥并进行部署。
2. 导航到 [Azure 门户](https://portal.azure.com/)中的 Azure Maps 帐户。
3. 在“设置”下选择“身份验证” 。
4. 若要为 Azure Maps 帐户重新生成主密钥，请选择主密钥旁边的“重新生成”按钮。
5. 更新应用程序代码以引用新的主密钥并进行部署。
6. 以相同方式重新生成辅助密钥。

> [!WARNING]
> Microsoft 建议同一时间在所有应用程序中只使用一个密钥。 如果在某些地方使用密钥 1，又在其他地方使用密钥 2，则在没有部分应用程序失去访问的情况下，将无法轮换密钥。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure AD 和 Azure Maps Web SDK](./how-to-use-map-control.md)。

查找 Azure Maps 帐户的 API 使用指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示了如何将 Azure AD 与 Azure Maps 相集成的示例：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)