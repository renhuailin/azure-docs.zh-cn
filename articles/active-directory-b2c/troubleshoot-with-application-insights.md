---
title: 使用 Application Insights 对自定义策略进行故障排除
titleSuffix: Azure AD B2C
description: 如何设置 Application Insights 来跟踪自定义策略的执行情况。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 06745d9a3ba5cb1e50e1adf23e88f442bcc31ab0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658493"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>使用 Application Insights 收集 Azure Active Directory B2C 日志

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

本文分步讲解如何从 Active Directory B2C (Azure AD B2C) 收集日志来诊断自定义策略存在的问题。 Application Insights 提供了一种方法来诊断异常和直观显示应用程序性能问题。 Azure AD B2C 有一项可将数据发送到 Application Insights 的功能。

只能在自定义策略的开发期间启用此处所述的详细活动日志。

> [!WARNING]
> 请勿在生产环境中将 `DeploymentMode` 设置为 `Development`。 日志会收集发送给以及发送自标识提供者的所有声明。 你作为开发人员对 Application Insights 日志中收集的所有个人数据负责。 仅当策略处于“开发人员模式”时才会收集这些详细日志。

## <a name="set-up-application-insights"></a>设置 Application Insights

如果还没有，请在订阅中设置一个 Application Insights 实例。

> [!TIP]
> Application Insights 的单个实例可用于多个 Azure AD B2C 租户。 然后可以在查询中按租户或策略名称进行筛选。 有关详细信息，请[参阅 Application Insights 中的日志](#see-the-logs-in-application-insights)示例。

要在订阅中使用现有的 Application Insights 实例，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure 订阅的 Azure AD 目录，而不是 Azure AD B2C 目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录 + 订阅”页上，在“目录名称”列表中找到 Azure AD 目录，然后选择“切换”。
1. 打开之前创建的 Application Insights 资源。
1. 在“概述”页上，记下“检测密钥” 

若要在订阅中创建 Application Insights 的实例，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure 订阅的 Azure AD 目录，而不是 Azure AD B2C 目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录 + 订阅”页上，在“目录名称”列表中找到 Azure AD 目录，然后选择“切换”。
1. 在左侧导航菜单中，选择“创建资源”。
1. 搜索“Application Insights”并将其选中，然后选择“创建” 。
1. 填写窗体，选择“查看 + 创建”，然后选择“创建” 。
1. 完成部署后，选择“转到资源”。
1. 在 Application Insights 菜单的“配置”下，选择“属性” 。
1. 记录检测密钥供稍后步骤使用。

## <a name="configure-the-custom-policy"></a>配置自定义策略

1. 打开信赖方 (RP) 文件，例如 SignUpOrSignin.xml。
1. 将以下属性添加到 `<TrustFrameworkPolicy>` 元素：

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 如果它尚不存在，请将 `<UserJourneyBehaviors>` 子节点添加到 `<RelyingParty>` 节点。 它必须位于 `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` 之后。
1. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。 确保将 `{Your Application Insights Key}` 替换为之前记录的 Application Insights 检测密钥。

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` 指示 Application Insights 使遥测数据更快地通过处理管道。 适用于开发，但在高容量时受到限制。 在生产环境中，将 `DeveloperMode` 设置为 `false`。
    * `ClientEnabled="true"` 发送用于跟踪页面视图和客户端错误的 ApplicationInsights 客户端脚本。 你可在 Application Insights 门户中的 browserTimings 表中查看这些内容。 通过设置 `ClientEnabled= "true"`，将 Application Insights 添加到页面脚本，并获取页面加载和 AJAX 调用的时间、浏览器异常和 AJAX 失败的计数和详细信息，以及用户和会话计数。 此字段可选，默认情况下设置为 `false`。
    * `ServerEnabled="true"` 将现有 UserJourneyRecorder JSON 作为自定义事件发送到 Application Insights。

    例如：

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. 上传该策略。

## <a name="see-the-logs-in-application-insights"></a>在 Application Insights 中查看日志

在 Application Insights 中看到新日志之前会有一小段延迟（通常不到 5 分钟）。

1. 打开在 [Azure 门户](https://portal.azure.com)中创建的 Application Insights 资源。
1. 在“概述”页面上，选择“日志” 。
1. 在 Application Insights 中打开新选项卡。

下面是可用于查看日志的查询列表：

| 查询 | 说明 |
|---------------------|--------------------|
| `traces` | 获取 Azure AD B2C 生成的所有日志 |
| `traces | where timestamp > ago(1d)` | 获取 Azure AD B2C 为前一天生成的所有日志。|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  获取过去两小时的所有日志和错误。|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | 获取 Azure AD B2C contoso.onmicrosoft.com 租户生成的所有日志，用户旅程为 b2c_1a_signinandup 。 |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| 获取 Azure AD B2C 为相关 ID 生成的所有日志。 将相关 ID 替换为你的相关 ID。 | 

条目可能较长。 导出到 CSV 进行更深入的了解。

有关查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/logs/log-query-overview.md)。

## <a name="see-the-logs-in-vs-code-extension"></a>查看 VS Code 扩展中的日志

建议为 [VS Code](https://code.visualstudio.com/) 安装 [Azure AD B2C 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)。 使用 Azure AD B2C 扩展，将按策略名称、相关 ID（Application Insights 显示相关 ID 的第一位数字）和日志时间戳组织日志。 此功能可帮助你根据本地时间戳查找相关日志，并查看 Azure AD B2C 执行的用户旅程。

> [!NOTE]
> 社区已开发适用于 Azure AD B2C 的 VS Code 扩展来帮助标识开发人员。 Microsoft 不支持此扩展，它严格按原样提供。

### <a name="set-application-insights-api-access"></a>设置 Application Insights API 访问权限

设置 Application Insights 并配置自定义策略后，需要获取 Application Insights API ID，然后创建 API 密钥 。 Azure AD B2C 扩展使用 API ID 和 API 密钥读取 Application Insights 事件（遥测）。 API 密钥的管理与密码类似。 请勿泄露。

> [!NOTE]
> Azure AD B2C 使用你先前创建的 Application Insights 检测密钥将遥测发送到 Application Insights。 仅在 Azure AD B2C 策略中使用检测密钥，而不在 VS Code 扩展中使用。

获取 Application Insights ID 和密钥：

1. 在 Azure 门户中，打开应用程序的 Application Insights 资源。
1. 选择“设置”，然后选择“API 访问” 。
1. 复制应用程序 ID
1. 选择“创建 API 密钥”
1. 选中“读取遥测”框。
1. 在关闭“创建 API 密钥”边栏选项卡之前复制该密钥并将其保持在安全位置。 如果丢失了密钥，则需要创建另一个密钥。

    ![演示如何创建 API 访问密钥的屏幕截图。](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>设置 Azure AD B2C VS Code 扩展

现在你已经拥有了 Azure Application Insights API ID 和密钥，可以配置 VS Code 扩展以读取日志。 Azure AD B2C VS Code 扩展提供了两个设置范围：

- **用户全局设置** - 这些设置全局适用于打开的任何 VS Code 实例。
- **工作区设置** - 这些设置存储在工作区内，仅在工作区打开时才适用（使用 VS Code“打开文件夹”）。

1. 在“Azure AD B2C 跟踪”资源管理器中，单击“设置”图标 。

    ![演示如何选择 Application Insights 设置的屏幕截图。](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. 提供 Azure Application Insights ID 和密钥 。
1. 单击“保存” 

保存设置后，Application Insights 日志将显示在“Azure AD B2C 跟踪(App Insights)”窗口中。

![适用于 VS Code 的 Azure AD B2C 扩展的屏幕截图，其中显示了 Azure Application Insights 跟踪。](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>在生产环境中配置 Application Insights

为了提高生产环境性能和用户体验，有必要配置策略来使其忽略不重要的消息。 在生产环境中使用以下配置。 

1. 将 [TrustFrameworkPolicy](trustframeworkpolicy.md) 的 `DeploymentMode` 属性设置为 `Production`。 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. 将 [JourneyInsights](relyingparty.md#journeyinsights) 的 `DeveloperMode` 设置为 `false`。

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. 上传并测试策略。



## <a name="next-steps"></a>后续步骤

- 了解如何[排查 Azure AD B2C 自定义策略问题](troubleshoot.md)

::: zone-end