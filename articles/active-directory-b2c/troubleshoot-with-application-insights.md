---
title: 自定义策略与 Application Insights 疑难解答
titleSuffix: Azure AD B2C
description: 如何设置 Application Insights 来跟踪自定义策略的执行。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1628d78c9d1e4db1f59982d696dcc886646fe604
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132051"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>利用 Application Insights 收集 Azure Active Directory B2C 日志

本文提供从 Active Directory B2C (Azure AD B2C) 收集日志的步骤，以便可以诊断自定义策略的问题。 Application Insights 提供了一种方法来诊断异常和直观显示应用程序性能问题。 Azure AD B2C 包含一项功能，用于将数据发送到 Application Insights。

此处所述的详细活动日志 **只** 应在开发自定义策略时启用。

> [!WARNING]
> 不要 `DeploymentMode` `Developer` 在生产环境中将设置为。 日志收集发送到标识提供程序的所有声明。 作为开发人员，您需要负责在 Application Insights 日志中收集的任何个人数据。 仅当策略处于 **开发人员模式下**时，才收集这些详细日志。

## <a name="set-up-application-insights"></a>设置 Application Insights

如果还没有，请在订阅中创建 Application Insights 的实例。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 " **目录 + 订阅** " 筛选器，然后选择包含 Azure 订阅的目录 (不是 Azure AD B2C 目录) 。
1. 选择左侧导航菜单中的 " **创建资源** "。
1. 搜索并选择 " **Application Insights**"，然后选择 " **创建**"。
1. 填写窗体，选择 " **查看 + 创建**"，然后选择 " **创建**"。
1. 部署完成后，选择 " **前往资源**"。
1. 在 Application Insights 菜单中的 " **配置** " 下，选择 " **属性**"。
1. 记录 **检测密钥** ，以便在后面的步骤中使用。

## <a name="configure-the-custom-policy"></a>配置自定义策略

1. 打开信赖方 (RP) 文件，例如 *SignUpOrSignin.xml*"。
1. 将以下属性添加到 `<TrustFrameworkPolicy>` 元素：

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 如果它尚不存在，请将一个 `<UserJourneyBehaviors>` 子节点添加到该 `<RelyingParty>` 节点。 它必须紧跟在之后 `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` 。
1. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。 请确保将替换为 `{Your Application Insights Key}` 前面记录的 Application Insights **检测密钥** 。

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` 告诉 Applicationinsights.config 通过处理管道加速遥测。 适用于开发，但在较大的卷上受到限制。 在生产环境中，将设置 `DeveloperMode` 为 `false` 。
    * `ClientEnabled="true"` 发送用于跟踪页面视图和客户端错误的 Applicationinsights.config 客户端脚本。 可以在 Application Insights 门户中的 **browserTimings** 表中查看这些项。 通过设置 `ClientEnabled= "true"` ，你可以将 Application Insights 添加到页面脚本，并获取页面加载和 ajax 调用、计数、浏览器异常和 ajax 失败的详细信息以及用户和会话计数的计时。 此字段是 **可选**的， `false` 默认情况下，设置为。
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

在 Application Insights 中看到新日志之前，延迟时间通常不到五分钟。

1. 打开在 [Azure 门户](https://portal.azure.com)中创建的 Application Insights 资源。
1. 在 " **概述** " 页上，选择 " **日志**"。
1. 在 Application Insights 中打开新选项卡。

下面是可用于查看日志的查询列表：

| 查询 | 说明 |
|---------------------|--------------------|
`traces` | 查看 Azure AD B2C 生成的所有日志 |
`traces | where timestamp > ago(1d)` | 查看 Azure AD B2C 为前一天生成的所有日志

条目可能较长。 导出到 CSV 进行更深入的了解。

有关查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

## <a name="configure-application-insights-in-production"></a>在生产环境中配置 Application Insights

若要改善您的生产环境性能和更好的用户体验，请务必将策略配置为忽略不重要的消息。 使用以下配置仅将严重错误消息发送到 Application Insights。 

1. 将 `DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) 的属性设置为 `Production` 。 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. 将 `DeveloperMode` [JourneyInsights](relyingparty.md#journeyinsights) 的设置为 `false` 。

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. 上载和测试策略。

## <a name="next-steps"></a>后续步骤

社区已开发一个用户旅程查看器来帮助标识开发人员。 它将读取 Application Insights 实例，并提供用户旅程事件的有序视图。 可以获取源代码并将其部署在自己的解决方案中。

Microsoft 不支持用户旅程 player，并严格按原样提供。

可以从 GitHub 上的 Application Insights 中找到读取事件的查看器的版本，如下所示：

[Azure-示例/active directory-b2c-高级策略](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
