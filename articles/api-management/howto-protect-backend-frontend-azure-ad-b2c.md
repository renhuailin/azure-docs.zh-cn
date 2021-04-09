---
title: 在 Azure API 管理中使用 Active Directory B2C 保护 SPA 后端
description: 结合使用 Azure Active Directory B2C、Azure API 管理和 Easy Auth 通过 OAuth 2.0 保护 API，使其通过启用了 PKCE 的 SPA 身份验证流从 JavaScript SPA 进行调用。
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954915"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>通过 OAuth 2.0、Azure Active Directory B2C 和 Azure API 管理保护 SPA 后端

此方案说明如何配置 Azure API 管理实例以保护 API。
我们将使用 Azure AD B2C SPA（身份验证代码 + PKCE）流获取令牌，同时使用 EasyAuth 通过 API 管理来确保 Azure Functions 后端的安全。

## <a name="aims"></a>目的

我们将了解如何通过 Azure Functions 和 Azure AD B2C 在简化方案中使用 API 管理。 你需要创建一个调用 API 的 JavaScript (JS) 应用，它使用 Azure AD B2C 登录用户。 然后，你将使用 API 管理服务的 validate-jwt、CORS 和“按密钥限制速率”策略功能来保护后端 API。

为了深度防御，我们随后会使用 EasyAuth 在后端 API 中再次验证令牌，并确保只有 API 管理服务可调用 Azure Functions 后端。

## <a name="what-will-you-learn"></a>学习的内容

> [!div class="checklist"]
> * 在 Azure Active Directory B2C 中设置单页应用和后端 API
> * 创建 Azure Functions 后端 API
> * 将 Azure Functions API 导入 Azure API 管理
> * 保护 Azure API 管理中的 API
> * 通过 Microsoft 标识平台库 (MSAL.js) 调用 Azure Active Directory B2C 授权终结点
> * 存储 HTML/Vanilla JS 单页应用程序，并从 Azure Blob 存储终结点提供该应用

## <a name="prerequisites"></a>先决条件

若要执行本文中的步骤，必须提供：

* 一个 Azure (StorageV2) 常规用途 V2 存储帐户，用于托管前端 JS 单页应用。
* 一个 Azure API 管理实例。任何层都适用，包括“消耗”层，不过某些适用于完整方案的功能在此层中不可用（按密钥限制速率和专用虚拟 IP）- 本文稍后部分将在适当的地方阐述这些限制。
* 一个空的 Azure 函数应用（在消耗计划中运行 V3.1 .NET Core 运行时），用于托管调用 API
* 一个 Azure AD B2C 租户，它与某订阅相关联。

尽管在实际情况下，会在生产工作负载中使用同一区域中的资源，但是在本操作指南文章中，部署的区域并不重要。

## <a name="overview"></a>概述

下图显示了正在使用的组件以及该过程完成后这些组件之间的流。
![正在使用的组件和流](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "正在使用的组件和流")

下面是简要的步骤概述：

1. 创建具有范围的 Azure AD B2C 调用（前端、API 管理）和 API 应用程序，并授予 API 访问权限
1. 创建注册和登录策略，以允许用户使用 Azure AD B2C 进行登录 
1. 使用新的 Azure AD B2C 客户端 ID 和密钥配置 API 管理，以在开发人员控制台中启用 OAuth2 用户授权
1. 生成函数 API
1. 配置函数 API 以使用新的 Azure AD B2C 客户端 ID 和密钥启用 EasyAuth 并锁定到 APIM VIP
1. 在 API 管理中生成 API 定义
1. 为 API 管理 API 配置设置 Oauth2
1. 设置 CORS 策略，并添加 validate-jwt 策略以验证每个传入请求的 OAuth 令牌 
1. 生成调用应用程序以使用 API
1. 上传 JS SPA 示例
1. 使用新的 Azure AD B2C 客户端 ID 和密钥配置示例 JS 客户端应用 
1. 测试客户端应用程序

   > [!TIP]
   > 在演练此文档时，我们将捕获大量信息和密钥等内容，你可能会发现有一个文本编辑器打开来临时存储以下几项配置有方便。  
   >
   > B2C 后端客户端 ID：  
   > B2C 后端客户端密钥：  
   > B2C 后端 API 范围 URI：  
   > B2C 前端客户端 ID：  
   > B2C 用户流终结点 URI：  
   > B2C 已知的 OpenID 终结点：   
   > B2C 策略名称：Frontendapp_signupandsignin 函数 URL：  
   > APIM API 基 URL：存储主终结点 URL：  

## <a name="configure-the-backend-application"></a>配置后端应用程序

在门户中打开“Azure AD B2C”边栏选项卡，然后执行以下步骤。

1. 选择“应用注册”选项卡
1. 单击“新建注册”按钮。
1. 从“重定向 URI”选择框中，选择“Web”。
1. 现在设置显示名称，请选择一个与正在创建的服务相关的唯一名称。 在本示例中，使用的名称是“后端应用程序”。
1. 使用回复 URL 的占位符，例如“https://jwt.ms”（Microsoft 拥有的令牌解码网站），我们稍后将更新这些 URL。
1. 确保已选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)”选项
1. 对于本示例，请取消选中“授予管理员同意”框，因为目前不需要 offline_access 权限。
1. 单击“注册”。
1. 记录后端应用程序客户端 ID 供以后使用，它显示在“应用程序(客户端)ID”下。
1. 在“管理”下选择“证书和密码”选项卡，然后单击“新建客户端密码”以生成身份验证密钥（接受默认设置，再单击“添加”）。
1. 单击“添加”时，将“值”下的密钥复制到安全的位置，以供稍后用作“后端客户端密码”- 请注意，此对话框是复制此密钥的唯一机会。
1. 现在，在“管理”下选择“公开 API”选项卡。
1. 系统将提示你设置 AppID URI，此时请选择并记录默认值。
1. 为函数 API 创建范围并将其命名为“Hello”，随后你可对所有可输入内容的选项使用“Hello”短语，记录填充的完整范围值 URI，再单击“添加范围”。
1. 选择门户左上角的“Azure AD B2C”痕迹导航栏，返回到 Azure AD B2C 边栏选项卡的根目录。

   > [!NOTE]
   > Azure AD B2C 范围是 API 中的有效权限，其他应用程序可以从其应用程序的“API 访问权限”边栏选项卡请求该 API 的访问权限，因此，只需为所调用的 API 创建应用程序权限。

## <a name="configure-the-frontend-application"></a>配置前端应用程序

1. 选择“应用注册”选项卡
1. 单击“新建注册”按钮。
1. 从“重定向 URI”选择框中，选择“单页应用程序(SPA)”。
1. 现在设置显示名称和 AppID URI，请选择一个与将使用此 AAD B2C 应用注册的前端应用程序相关的唯一名称。 在此示例中，可使用“前端应用程序”
1. 如同首次应用注册一样，将支持的帐户类型选择保留为默认值（通过用户流对用户进行身份验证）
1. 使用回复 URL 的占位符，例如“https://jwt.ms”（Microsoft 拥有的令牌解码网站），我们稍后将更新这些 URL。
1. 将“授予管理员同意”框保留在勾选状态
1. 单击“注册”。
1. 记录前端应用程序客户端 ID 供以后使用，它显示在“应用程序(客户端)ID”下。
1. 切换到“API 权限”选项卡。
1. 授予对后端应用程序的访问权限，方法是依次单击“添加权限”、“我的 API”、“后端应用程序”和“权限”，再选择上一部分中创建的范围，然后单击“添加权限”
1. 单击“为{租户}授予管理员同意”，然后在弹出对话框中单击“是”。 此弹出窗口同意“前端应用程序”使用在之前创建的“后端应用程序”中定义的“hello”权限。
1. 现在，应用的所有权限应会在状态列下显示为绿色勾号

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>创建“注册和登录”用户流

1. 选择 Azure AD B2C 痕迹导航栏，返回到 B2C 边栏选项卡的根目录。
1. 切换到“策略”下的“用户流”选项卡。
1. 单击“新建用户流”
1. 选择“注册和登录”用户流类型，选择“推荐”，然后选择“创建”
1. 为策略指定一个名称并记录，以供以后使用。 在本例中，可使用“Frontendapp_signupandsignin”；请注意，这将带有“B2C_1_”前缀，构成“B2C_1_Frontendapp_signupandsignin”
1. 在“标识提供者”和“本地帐户”下，选中“电子邮件注册”（或“用户 ID 注册”，具体取决于 B2C 租户的配置），然后单击“确定”。 使用此配置是因为我们将注册本地 B2C 帐户，而不是推延给其他标识提供者（如社交标识提供者）来使用用户的现有社交媒体帐户。
1. 将 MFA 和条件访问设置保留在默认状态下。
1. 在“用户属性和声明”下，单击“显示更多…”，然后选择希望用户输入并在令牌中返回的声明选项。 至少勾选“显示名称”(Display Name) 和“电子邮件地址”(Email Address) 进行收集，要返回的是“Display Name”和“Email Addresses”（请注意，要收集的是 emailaddress（单数），而要求返回的是 email addresses（复数）），然后依次单击“确定”和“创建”。
1. 在列表中选择你创建的用户流，然后单击“运行用户流”按钮。
1. 此操作将打开“运行用户流”边栏选项卡，此时请选择前端应用程序，复制用户流终结点，并将其保存供以后查看。
1. 复制并存储顶部的链接，将其记录为“已知的 OpenID 配置终结点”供以后使用。

   > [!NOTE]
   > 使用 B2C 策略可以公开 Azure AD B2C 登录终结点，使其能够捕获不同的数据组件，并以不同的方式使用户登录。
   > 
   > 在本例中，我们配置了注册或登录流（策略）。 这也公开了已知的配置终结点，在这两种情况下，我们创建的策略都通过“p=”查询字符串参数在 URL 中进行了标识。  
   >
   > 完成此操作后，你现在就有了一个功能性的企业对消费者标识平台，可将用户登录到多个应用程序。

## <a name="build-the-function-api"></a>生成函数 API

1. 切换回到 Azure 门户中的标准 Azure AD 租户，以便我们可再次配置你订阅中的项。
1. 转到 Azure 门户的“函数应用”边栏选项卡，打开空函数应用，然后依次单击“函数”和“添加”。
1. 在出现的浮出控件中选择“在门户中开发”，在“选择模板”下选择“HTTP 触发器”，接着在“模板详细信息”中，将其命名为“hello”（授权级别设为“函数”），然后选择“添加”。
1. 切换到“代码 + 测试”边栏选项卡，并将下面的示例代码复制粘贴到显示的现有代码的上方。
1. 选择“保存”。

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > 刚粘贴的 c # 脚本函数代码仅向函数日志中记录一行内容，并返回文本“Hello World”，其中包含一些动态数据（日期和时间）。

1. 从左侧边栏选项卡中选择“集成”，然后在“触发器”框中单击 HTTP（请求）链接。
1. 从“选定的 HTTP 方法”下拉列表中取消选中 HTTP POST 方法，只保留选择 GET，然后单击“保存”。
1. 切换回到“代码 + 测试”选项卡，单击“获取函数 URL”，然后复制显示的 URL 并将其保存供以后查看。

   > [!NOTE]
   > 你刚才创建的绑定只会指示 Functions 对你刚才复制的 URL (`https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey`) 的匿名 HTTP GET 请求做出响应。 现在，我们有一个可缩放的无服务器 HTTP API，它能够返回非常简单的有效负载。
   >
   > 你现可使用上面刚复制和保存的 URL 版本，测试从 Web 浏览器调用此 API 的情况。 你还可删除 URL 的查询字符串参数的“?code=secretkey”部分，并再次测试，证明 Azure Functions 将返回 401 错误。

## <a name="configure-and-secure-the-function-api"></a>配置和保护函数 API

1. 需要配置函数应用中的两个额外区域（身份验证和网络限制）。
1. 首先，让我们来配置身份验证/授权，请通过痕迹导航栏导航回到函数应用的根边栏选项卡。
1. 然后，在“设置”下选择“身份验证/授权”。
1. 启用“应用服务身份验证”功能。
1. 将“请求未经身份验证时需执行的操作”下拉列表设置为“使用 Azure Active Directory 登录”。
1. 在“身份验证提供程序”下，选择“Azure Active Directory”。
1. 从“管理模式”开关中选择“高级”。
1. 将后端应用程序的[应用程序]客户端 ID（来自 Azure AD B2C）粘贴到“客户端 ID”框中
1. 将已知的 OpenID 配置终结点从注册和登录策略粘贴到“颁发者 URL”框中（前面已记录此配置）。
1. 单击“显示密码”，将后端应用程序的客户端密码粘贴到相应的框中。
1. 选择“确定”，这会返回到“标识提供者选择”边栏选项卡/屏幕。
1. 将高级设置下的[令牌存储](../app-service/overview-authentication-authorization.md#token-store)保留在启用状态（默认设置）。
1. 在边栏选项卡左上方单击“保存”。

   > [!IMPORTANT]
   > 现在，你的函数 API 已部署。如果未提供正确的 JWT 作为 Authorization: Bearer 标头，该 API 应引发 401 响应；而在出现有效请求时，它应返回数据。  
   > 通过配置“使用 Azure AD 登录”选项，已在 EasyAuth 中添加了其他深层防御安全性，以处理未经身份验证的请求。 请注意，这会更改后端函数应用和前端 SPA 之间未经授权的请求行为，因为 EasyAuth 将发出到 AAD 的 302 重定向，而不是 401“未授权”响应，我们将在稍后使用 API 管理来更正此问题。  
   >
   > 我们仍未应用 IP 安全性，如果拥有有效的密钥和 OAuth2 令牌，则任何人都可以从任何位置调用它，理想情况下，我们希望强制所有请求都通过 API 管理传入。  
   > 
   > 如果使用的是 APIM 消耗层，则[没有专用的 Azure API 管理虚拟 IP](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) 列入具有函数访问限制的允许列表中。 在 Azure API 管理标准 SKU 及更高版本中，[VIP 是单租户且针对资源生存期](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)。 对于 Azure API 管理消耗层，可通过上面复制的 URI 部分中的共享机密函数密钥锁定 API 调用。 此外，对于消耗层，下面的步骤 12-17 不适用。

1. 关闭“身份验证/授权”边栏选项卡 
1. 打开门户的“API 管理”边栏选项卡，然后打开你的实例 。
1. 记录“概述”选项卡上显示的专用 VIP。
1. 返回到门户的“Azure Functions”边栏选项卡，然后再次打开你的实例 。
1. 选择“网络”，然后选择“配置访问限制”
1. 单击“添加规则”，然后输入在上面步骤 3 中复制的 VIP，格式为 xx.xx.xx.xx/32。
1. 如果要继续与函数门户交互，并执行以下可选步骤，还应在此处添加自己的公共 IP 地址或 CIDR 范围。
1. 列表中有允许条目后，Azure 会添加隐式拒绝规则以阻止所有其他地址。

需要将 CIDR 格式的地址块添加到 IP 限制面板。 如果需要添加单个地址（例如 API 管理 VIP），需要按 xx.xx.xx.xx/32 格式添加它。

   > [!NOTE]
   > 现在，除了 API 管理或你的地址之外，应不能从任何位置调用函数 API。

1. 打开“API 管理”边栏选项卡，然后打开你的实例 。
1. 在“API”下选择“API”边栏选项卡。
1. 从“添加新 API”窗格中选择“函数应用”，然后从弹出窗口顶部选择“完整”。
1. 单击“浏览”，选择要在其中托管 API 的函数应用，然后单击“选择”。 接下来，再次单击“选择”。
1. 为 API 提供名称和描述，以便内部使用 API 管理，并将其添加到“无限制”产品。
1. 复制并记录 API 的“基 URL”，然后单击“创建”。
1. 单击“设置”选项卡，然后在“订阅”下关闭“需要订阅”复选框，因为在本例中我们将使用 Oauth JWT 令牌来限制速率。 请注意，如果使用的是消耗层，则在生产环境中仍然需要此项。 

   > [!TIP]
   > 如果使用 APIM 的消耗层，则不受限的产品将无法开箱即用。 而是要在“API”下导航到“产品”，然后点击“添加”。  
   > 键入“无限制”作为产品名称和说明，并从屏幕左下角的“+”API 标注中选择刚添加的 API。 选择“已发布”复选框。 将其余设置保留为默认值。 最后，点击“创建”按钮。 这会创建“不受限”产品并将其分配给 API。 随后可自定义你的新产品。

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>配置并捕获正确的存储终结点设置

1. 在 Azure 门户中打开“存储帐户”边栏选项卡 
1. 选择所创建的帐户，然后从“设置”部分选择“静态网站”边栏选项卡（如果看不到“静态网站”选项，请检查是否已创建 V2 帐户）。
1. 将静态 Web 托管功能设置为“已启用”，将索引文档名称设置为“index.html”，然后单击“保存”。
1. 记下“主终结点”的内容供以后使用，因为此位置是托管前端站点的位置。

   > [!TIP]
   > 可使用 Azure Blob 存储加 CDN 重写或使用 Azure 应用服务来托管 SPA，不过 Blob 存储的静态网站托管功能提供了一个默认容器来从 Azure 存储中提供静态 Web 内容/HTML/JS/CSS，该功能还将推断默认页面，而无需我们执行任何操作。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>设置 CORS 和 validate-jwt 策略 

> 无论使用何种 APIM 层，都应遵循以下部分。 存储帐户 URL 来自于在本文开头的先决条件中提供的存储帐户。
1. 切换到门户的“API 管理”边栏选项卡，然后打开你的实例。
1. 选择“API”，然后选择“所有 API”。
1. 在“入站处理”下，单击代码视图按钮“</>”以显示策略编辑器。
1. 编辑入站部分并粘贴以下 xml，使其类似于以下内容。
1. 在“策略”中，替换以下参数
1. 将 {PrimaryStorageEndpoint}（你在上一部分复制的“主存储终结点”）、{b2cpolicy-well-known-openid}（你之前复制的“已知的 OpenID 配置终结点”）和 {backend-api-application-client-id}（后端 API 的 B2C 应用程序/客户端 ID）替换为之前保存的正确值。
1. 如果使用的是 API 管理的消耗层，则应删除“按密钥限制速率”策略，因为使用 Azure API 管理的消耗层时，此策略不可用。

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > 现在，Azure API 管理能够响应来自 JavaScript SPA 应用的跨域请求，并且会在将请求转发到函数 API 之前，对正在传递的 JWT 身份验证令牌执行限制、限速和预验证。
   > 
   > 恭喜，你现在拥有 Azure AD B2C、API 管理和 Azure Functions 协同配合来发布、保护和使用 API 了！

   > [!TIP]
   > 如果使用的是 API 管理消耗层，则不会通过 JWT 使用者或传入的 IP 地址来限制速率（“消耗”层目前不支持按密钥限制调用速率策略），你可按调用速率配额进行限制，具体请查看[此处](./api-management-access-restriction-policies.md#LimitCallRate)。  
   > 此示例是一个 JavaScript 单页应用程序，因此我们只对速率限制和计费调用使用 API 管理密钥。 实际的授权和身份验证由 Azure AD B2C 处理并封装在 JWT 中，此过程会进行两次验证，先通过 API 管理验证一次，再通过后端 Azure 函数进行验证。

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>将 JavaScript SPA 示例上传到静态存储

1. 依然是在“存储帐户”边栏选项卡中，从“Blob 服务”部分选择“容器”边栏选项卡，然后单击右侧窗格中显示的 $web 容器。
1. 将以下代码作为 index.html 保存到计算机上的本地文件中，然后将文件 index.html 上传到 $web 容器。

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. 浏览到之前在上一部分存储的静态网站主终结点。

   > [!NOTE]
   > 恭喜，你刚才将一个 JavaScript 单页应用部署到了 Azure 存储静态内容托管。  
   > 我们尚未使用你的 Azure AD B2C 详细信息配置 JS 应用，因此如果你打开页面，它将不起作用。

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>为 Azure AD B2C 配置 JS SPA

1. 现在我们知道了所有内容的位置：我们可通过相应的 API 管理 API 地址和正确的 Azure AD B2C 应用程序/客户端 ID 来配置 SPA。
1. 返回到 Azure 门户中的“存储”边栏选项卡 
1. 在“设置”下选择“容器” 
1. 从列表中选择“$web”容器
1. 从列表中选择 index.html blob 
1. 单击“编辑” 
1. 更新 msal config 部分中的身份验证值，使其与你之前在 B2C 中注册的前端应用程序相匹配。 使用代码注释获取有关配置值应如何显示的提示。
颁发机构值的格式应为： https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantname}.onmicrosoft.com}/{signupandsigninpolicyname}。如果使用的是示例名，且 B2C 租户名称为“contoso”，那么颁发机构应为“https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin”。
1. 设置 API 值，使其与后端地址相匹配（之前记录的 API 基 URL，以及之前为后端应用程序记录的“b2cScopes”值）。
1. 点击“保存”(Save)

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>设置 Azure AD B2C 前端应用的重定向 URI

1. 打开“Azure AD B2C”边栏选项卡，然后导航到 JavaScript 前端应用程序的应用程序注册。
1. 单击“重定向 URI”并删除之前输入的占位符“https://jwt.ms”。
1. 为主（存储）终结点添加新的 URI（删除末尾的正斜杠）。

   > [!NOTE]
   > 此配置将导致前端应用程序的客户端收到具有 Azure AD B2C 中的相应声明的访问令牌。  
   > SPA 将能够将此作为持有者令牌添加到后端 API 调用的 https 标头中。  
   > 
   > API 管理将先预验证令牌，速率限制 Azure ID（用户）和调用方 IP 地址发出的 JWT 的使用者对终结点的调用（取决于 API 管理的服务层，详见上面的说明），然后再通过请求传递给负责接收的 Azure 函数 API，添加函数安全密钥。  
   > SPA 将在浏览器中呈现响应。
   >
   > 恭喜，你已配置了可完美协作的 Azure AD B2C、Azure API 管理、Azure Functions、Azure 应用服务授权！

现在，我们有了一个具有简单安全的 API 的简单应用，我们来对其进行测试。

## <a name="test-the-client-application"></a>测试客户端应用程序

1. 打开在之前创建存储帐户中记下的示例应用 URL。
1. 单击右上角的“登录”，单击后将弹出 Azure AD B2C 注册或登录配置文件。
1. 该应用会按你的 B2C 资料名称来欢迎你。
1. 现在单击“调用 API”，页面应会更新显示从你受保护的 API 发回的值。
1. 如果反复单击“调用 API”按钮，并且你正在 API 管理的开发人员层或更高层级上运行，那么应注意，你的解决方案将开始限制 API 的速率，且此功能应会在应用中报告并显示相应的消息。

## <a name="and-were-done"></a>大功告成

可以调整和编辑上述步骤，以通过 API 管理实现 Azure AD B2C 的多种不同用途。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Active Directory 和 OAuth2.0](../active-directory/develop/authentication-vs-authorization.md)。
* 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
* 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](api-management-howto-mutual-certificates.md)。
* [创建 API 管理服务实例](get-started-create-service-instance.md)。
* [管理第一个 API](import-and-publish.md)。