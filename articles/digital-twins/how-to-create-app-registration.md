---
title: 创建应用注册
titleSuffix: Azure Digital Twins
description: 了解如何创建 Azure AD 应用注册作为客户端应用的身份验证选项。
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b22552147bec789c4b1d796cad2c52165d266334
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108207710"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>创建与 Azure 数字孪生配合使用的应用注册

使用 Azure 数字孪生实例时，我们往往会通过客户端应用程序（例如自定义客户端应用或 [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md) 之类的示例）与该实例进行交互。 这些应用程序需要在 Azure 数字孪生中完成身份验证才能与该实例交互，而应用可以使用的某些 [身份验证机制](how-to-authenticate-client.md)涉及到 [ Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **应用注册**。

并非所有身份验证方案都需要应用注册。 但是，如果使用的身份验证策略或代码示例确实需要应用注册（包括 **客户端 ID** 和 **租户 ID**），你可以参考本文设置一个应用注册。

## <a name="using-azure-ad-app-registrations"></a>使用 Azure AD 应用注册

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 是 Microsoft 推出的基于云的标识和访问管理服务。 在 Azure AD 中设置 **应用注册** 是向客户端应用授予对 Azure 数字孪生的访问权限的一种方式。

在此应用注册中，可以配置对 [Azure 数字孪生 API](how-to-use-apis-sdks.md) 的访问权限。 以后，客户端应用可以使用注册的 **客户端和租户 ID 值** 对应用注册进行身份验证，从而获得已配置的、对 API 的访问权限。

>[!TIP]
> 你可能希望在每次需要应用注册时才设置新的应用注册，或者，你可能希望仅设置应用注册一次，建立单个应用注册以在需要该应用注册的所有方案之间共享它。

## <a name="create-the-registration"></a>创建注册

首先在 Azure 门户中导航到“[Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)”（可以使用此链接，或者通过门户搜索栏找到它）。 从服务菜单中选择“应用注册”，然后选择“+ 新建注册”。 

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Azure 门户中“Azure AD 服务”页的视图，其中突出显示了“应用注册”菜单选项和“+ 新建注册”按钮":::

在随后出现的“注册应用程序”页中填写请求的值：
* **名称**：与注册关联的 Azure AD 应用程序显示名称
* **支持的帐户类型**：选择“仅此组织目录中的帐户(仅默认目录 - 单租户)”
* **重定向 URI**：Azure AD 应用程序的 *Azure AD 应用程序回复 URL*。 为 `http://localhost` 添加一个“公共客户端/本机(移动和桌面)”URI。

完成后，点击“注册”按钮。

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="“注册应用程序”页的视图，其中填写了所描述的值":::

设置完注册后，门户会将你重定向到该注册的详细信息页。

## <a name="collect-client-id-and-tenant-id"></a>收集客户端 ID 和租户 ID

接下来，从应用注册的详细信息页收集有关它的一些重要值：

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="重要的应用注册值的门户视图":::

记下 **你的** 页面上显示的“应用程序(客户端) ID”和“目录(租户) ID”。  客户端应用使用此注册在 Azure 数字孪生中进行身份验证时需要这些值。

## <a name="provide-azure-digital-twins-api-permission"></a>提供 Azure 数字孪生 API 权限

接下来，为已创建的应用注册配置对 Azure 数字孪生 API 的基线权限。

在应用注册的门户页中，从菜单中选择“API 权限”。 在随后出现的权限页上，点击“+ 添加权限”按钮。

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Azure 门户中应用注册的视图，其中突出显示了“API 权限”菜单选项和“+ 添加权限”按钮":::

在随后出现的“请求 API 权限”页中，切换到“我的组织使用的 API”选项卡并搜索“Azure 数字孪生”。   从搜索结果中选择“Azure 数字孪生”，以继续分配对 Azure 数字孪生 API 的权限。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="“请求 API 权限”页搜索结果的视图，其中显示了“Azure 数字孪生”，及其“应用程序(客户端) ID”0b07f429-9f4b-4714-9392-cc5e8e80c8b0。":::

>[!NOTE]
> 如果你的订阅仍然包含来自以前的服务公共预览版（2020 年 7 月之前）中的现有 Azure 数字孪生实例，则你需要改为搜索并选择“Azure 智能空间服务”。 这是同一组 API 的旧名称（请注意“应用程序(客户端) ID”与上面的屏幕截图中相同），除了改为执行此步骤以外，你的体验在其他方面没有变化。
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="“请求 API 权限”页搜索结果的视图，其中显示了“Azure 智能空间服务”":::

接下来，选择要授予的对这些 API 的权限。 展开“读取(1)”权限，然后选中标有“Read.Write”的框，以向此应用注册授予读取者和写入者权限。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="“请求 API 权限”页的视图，其中选择了对 Azure 数字孪生 API 的“Read.Write”权限":::

完成后点击“添加权限”。

### <a name="verify-success"></a>验证是否成功

在“API 权限”页上，验证现在是否出现了与 Azure 数字孪生对应的、反映读/写权限的条目：

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Azure AD 应用注册的 API 权限门户视图，其中显示了 Azure 数字孪生的“读/写访问权限”":::

还可以在应用注册的 *manifest.json* 中验证与 Azure 数字孪生的连接。添加 API 权限后，会使用 Azure 数字孪生信息自动更新此文件。

为此，请从菜单中选择“清单”以查看应用注册的清单代码。 滚动到代码窗口的底部，并在 `requiredResourceAccess` 下查看这些字段。 这些值应与以下屏幕截图中的值相匹配：

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Azure AD 应用注册清单的门户视图。嵌套在“requiredResourceAccess”下，有一个“resourceAppId”值为“0b07f429-9f4b-4714-9392-cc5e8e80c8b0”，一个“resourceAccess > id”值为“4589bd03-58cb-4e6c-b17f-b580e39652f8”":::

如果缺少这些值，请重试[有关添加 API 权限的部分](#provide-azure-digital-twins-api-permission)中所述的步骤。

## <a name="other-possible-steps-for-your-organization"></a>适用于你的组织的其他可行步骤

你的组织可能需要订阅所有者/管理员执行其他操作才能成功设置应用注册。 根据组织的特定设置，所需的步骤可能会有所不同。

下面是订阅所有者/管理员可能需要执行的一些常见潜在活动。 可从 Azure 门户中的“Azure AD 应用注册”页执行这些操作和其他操作。
* 授予管理员同意进行应用注册。 你的组织可能已在 Azure AD 中对你的订阅中的所有应用注册全局启用了“需要管理员同意”。 如果是这样，则所有者/管理员需要在应用注册的“API 权限”页上为你的公司选择以下按钮，以使应用注册有效：

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="“API 权限”下的“授予管理员同意”按钮的门户视图":::
  - 如果已成功授予同意，则 Azure 数字孪生对应的条目应会显示“状态”值“已授予 (你的公司)”
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="显示已在 API 权限下为公司授予了管理员同意的门户视图":::
* 激活公共客户端访问
* 设置特定的回复 URL 用于进行 Web 和桌面访问
* 允许隐式 OAuth2 身份验证流

有关应用注册及其不同设置选项的详细信息，请参阅将应用程序注册到 Microsoft 标识平台。

## <a name="next-steps"></a>后续步骤

在本文中，你设置了一个可用于通过 Azure 数字孪生 API 对客户端应用程序进行身份验证的 Azure AD 应用注册。

接下来请了解身份验证机制，包括一个使用应用注册的机制，以及其他不使用应用注册的机制：
* [如何：编写应用验证码](how-to-authenticate-client.md)