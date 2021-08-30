---
title: 在 Microsoft Azure Maps 中管理身份验证
titleSuffix: Azure Maps
description: 熟悉 Azure Maps 身份验证。 了解哪种方法最适合哪种方案。 了解如何使用门户查看身份验证设置。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/10/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
custom.ms: subject-rbac-steps
ms.openlocfilehash: d087daf38c455fd4d87ea571ff5f3a0ab9ad0527
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454759"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户时，会生成密钥和客户端 ID。 此密钥和客户端 ID 用于支持 Azure Active Directory (Azure AD) 身份验证和共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

 >[!IMPORTANT]
 >当你[使用共享密钥身份验证来调用 Azure Maps](./azure-maps-authentication.md#shared-key-authentication) 时，建议使用主密钥作为订阅密钥。 辅助密钥最好在滚动密钥更改等场景中使用。 有关详细信息，请参阅 [Azure Maps 中的身份验证](./azure-maps-authentication.md)。

查看 Azure Maps 身份验证详细信息：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 导航到 Azure 门户菜单。 选择“所有资源”，然后选择你的 Azure Maps 帐户。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="选择 Azure Maps 帐户。":::

3. 在左侧窗格中的“设置”下，选择“身份验证” 。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="身份验证详细信息。":::

## <a name="choose-an-authentication-category"></a>选择身份验证类别

根据应用程序的需求，有特定的应用程序安全途径。 Azure AD 定义了特定的身份验证类别，以支持各种身份验证流。 若要为应用程序选择最佳类别，请参阅[应用程序类别](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories)。

> [!NOTE]
> 即使是使用共享密钥身份验证，了解类别和方案也有助于保护应用程序的安全。

## <a name="choose-an-authentication-and-authorization-scenario"></a>选择身份验证和授权场景

下表列出了 Azure Maps 中常见的身份验证和授权场景。 使用链接以了解每个场景的详细配置信息。

> [!IMPORTANT]
> 对于生产应用程序，建议使用 Azure 基于角色的访问控制 (Azure RBAC) 来实现 Azure AD。

| 场景                                                                                    | 身份验证 | 授权 | 开发工作 | 运营工作 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [可信守护程序/非交互式客户端应用程序](./how-to-secure-daemon-app.md)        | 共享密钥     | 不可用           | 中型             | 高               |
| [可信守护程序/非交互式客户端应用程序](./how-to-secure-daemon-app.md)        | Azure AD       | 高          | 低                | 中             |
| [具有交互式单一登录的 Web 单页面应用程序](./how-to-secure-spa-users.md) | Azure AD       | 高          | 中等             | 中             |
| [具有非交互式登录的 Web 单页面应用程序](./how-to-secure-spa-app.md)      | Azure AD       | 高          | 中等             | 中             |
| [具有交互式单一登录的 Web 应用程序](./how-to-secure-webapp-users.md)          | Azure AD       | 高          | 高               | 中等             |
| [IoT 设备/输入受限设备](./how-to-secure-device-code.md)                     | Azure AD       | 高          | 中等             | 中             |

## <a name="view-built-in-azure-maps-role-definitions"></a>查看内置的 Azure Maps 角色定义

查看内置的 Azure Maps 角色定义：

1. 在左侧窗格中，选择“访问控制(IAM)”。

2. 选择“角色”选项卡。

3. 在搜索框中输入“Azure Maps”。

结果将显示适用于 Azure Maps 的内置角色定义。

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="查看内置的 Azure Maps 角色定义。":::

## <a name="view-role-assignments"></a>查看角色分配

若要查看已为 Azure Maps 授予访问权限的用户和应用，请转到 **访问控制 (IAM)** 。 在此处选择“角色分配”，然后按“Azure Maps”进行筛选。

1. 在左侧窗格中，选择“访问控制(IAM)”。

2. 选择“角色分配”选项卡。

3. 在搜索框中输入“Azure Maps”。

结果将显示当前的 Azure Maps 角色分配。

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="查看已被授予访问权限的用户和应用。":::

## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

从 Azure AD 令牌终结点请求令牌。 在 Azure AD 请求中，使用以下详细信息：

| Azure 环境      | Azure AD 令牌终结点             | Azure 资源 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 公有云     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 政府版云 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

有关为用户和服务主体从 Azure AD 请求访问令牌的详细信息，请参阅 [Azure AD 的身份验证方案](../active-directory/develop/authentication-vs-authorization.md)。  若要查看特定场景，请参阅[场景表](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario)。

## <a name="manage-and-rotate-shared-keys"></a>管理和轮换共享密钥

Azure Maps 订阅密钥类似于 Azure Maps 帐户的根密码。 始终要小心保护订阅密钥。 使用 Azure Key Vault 安全地管理和轮换密钥。 避免将访问密钥分发给其他用户、对它们进行硬编码或将它们以纯文本的形式保存在其他人可以访问的任何位置。 如果你认为你的密钥可能已泄露，请对它进行轮换。

> [!NOTE]
> 如果可能，建议使用 Azure AD（而不是共享密钥）来授权请求。 Azure AD 的安全性高于共享密钥，并且更易于使用。

### <a name="manually-rotate-subscription-keys"></a>手动轮换订阅密钥

为了帮助保护 Azure Maps 帐户的安全，建议定期轮换订阅密钥。 如果可能，请使用 Azure 密钥保管库管理访问密钥。 如果没有使用 Key Vault，则需要手动轮换密钥。

分配了两个订阅密钥，以便轮换使用。 拥有两个密钥可确保应用程序在整个过程中能够持续访问 Azure Maps。

若要在 Azure 门户中轮换 Azure Maps 订阅密钥，请执行以下操作：

1. 更新应用程序代码以引用 Azure Maps 帐户的辅助密钥并进行部署。
2. 在 [Azure 门户](https://portal.azure.com/)中，导航到你的 Azure Maps 帐户。
3. 在“设置”下选择“身份验证” 。
4. 若要为 Azure Maps 帐户重新生成主密钥，请选择主密钥旁边的“重新生成”按钮。
5. 更新应用程序代码以引用新的主密钥并进行部署。
6. 以相同方式重新生成辅助密钥。

> [!WARNING]
> 我们建议你同时仅在所有应用程序中使用其中一个密钥。 如果在某些地方使用密钥 1，同时又在其他地方使用密钥 2，那么你将无法轮换密钥，除非让某些应用程序失去访问权限。

## <a name="next-steps"></a>后续步骤

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示了如何将 Azure AD 与 Azure Maps 相集成的示例：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
