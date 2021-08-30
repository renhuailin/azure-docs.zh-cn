---
title: 通过多步骤 Web 测试进行监视 - Azure Application Insights
description: 使用 Azure Application Insights 设置多步骤 Web 测试，以便监视 Web 应用程序
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: d248340aa272a6a1fef386ca755ed46536668ad9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447991"
---
# <a name="multi-step-web-tests"></a>多步骤 Web 测试

可通过多步骤 Web 测试来监视录制的 URL 序列和与网站的交互。 本文将逐步讲解使用 Visual Studio Enterprise 创建多步骤 Web 测试的过程。

> [!NOTE]
> [Azure 政府](../../azure-government/index.yml)云中不支持多步骤 Web 测试。

> [!NOTE]
> 多步骤 Web 测试归为经典测试，可以在“可用性”窗格中的“添加经典测试”下找到。

## <a name="multi-step-webtest-alternative"></a>多步骤 Web 测试替代方案

多步骤 Web 测试依赖于 Visual Studio WebTest 文件。 我们已[宣布](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)，Visual Studio 2019 将是支持 WebTest 功能的最后一个版本。 尽管我们不会添加任何新功能，但目前仍支持 Visual Studio 2019 中的 Web 测试功能，并且在该产品的支持生命周期内仍将继续提供支持，了解这一点至关重要。 

建议使用 [TrackAvailability](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) 来提交[自定义可用性测试](./availability-azure-functions.md)，而不是多步骤 Web 测试。 这是针对多请求或身份验证测试场景的长期支持解决方案。 使用 TrackAvailability() 和自定义可用性测试，可以在所需的任何计算上运行测试，并使用 C# 轻松编写新测试。

## <a name="pre-requisites"></a>先决条件

* Visual Studio 2017 Enterprise 或更高版本。
* Visual Studio Web 性能和负载测试工具。

查找测试工具必备组件。 启动“Visual Studio 安装程序” > “单个组件” > “调试和测试” > “Web 性能和负载测试工具”   。

![Visual Studio 安装程序 UI 的屏幕截图，其中已选择“单个组件”和“Web 性能和负载测试工具”项旁边的复选框。](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 多步骤 Web 测试具有与之相关的额外成本。 如需了解详细信息，请参阅[官方定价指南](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="record-a-multi-step-web-test"></a>录制多步骤 Web 测试 

> [!WARNING]
> 我们不再建议使用多步骤记录器。 记录器是针对具有基本交互的静态 HTML 页面开发的，不提供新式网页的功能体验。

有关创建 Visual Studio Web 测试的指南，请参阅[官方 Visual Studio 2019 文档](/visualstudio/test/how-to-create-a-web-service-test)。

## <a name="upload-the-web-test"></a>上传 Web 测试

1. 在 Application Insights 门户的“可用性”窗格上，选择“添加经典测试”，然后选择“多步骤”作为“SKU” 。
2. 上传多步骤 Web 测试。
3. 设置测试位置、频率和警报参数。
4. 选择“创建”。

### <a name="frequency--location"></a>频率和位置

|设置| 说明
|----|----|----|
|**测试频率**| 设置从每个测试位置运行测试的频率。 如果有五个测试位置，且默认频率为五分钟，则平均每隔一分钟测试站点一次。|
|**测试位置**| 是服务器从其将 Web 请求发送到 URL 的位置。 **建议最低测试位置数目为 5**，以确保可以将网站中的问题与网络问题区分开来。 最多可以选择 16 个位置。

### <a name="success-criteria"></a>成功条件

|设置| 说明
|----|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源  。|
| **HTTP 响应** | 视为成功的返回状态代码。 代码 200 指示返回了正常网页。|
| **内容匹配** | 类似于“欢迎!”的字符串。 我们测试区分大小写的匹配项是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。 **内容匹配仅支持英文字符** |

### <a name="alerts"></a>警报

|设置| 说明
|----|----|----|
|**近实时（预览）** | 我们建议使用近实时警报。 在创建可用性测试后会配置此类警报。  |
|**警报位置阈值**|建议最少 3/5 个位置。 警报位置阈值和测试位置数目之间的最佳关系是，警报位置阈值  =  测试位置数 - 2，至少有 5 个测试位置 。|

## <a name="configuration"></a>配置

### <a name="plugging-time-and-random-numbers-into-your-test"></a>在测试中插入时间和随机数字

假设要测试的工具从外部源获取与时间相关的数据（例如股票）。 录制 Web 测试时，必须使用具体的时间，但要将它们设置为测试参数：StartTime 和 EndTime。

![我的股票应用屏幕截图](./media/availability-multistep/app-insights-72webtest-parameters.png)

运行测试时，EndTime 应该始终为当前时间，StartTime 在 15 分钟前。

Web 测试日期时间插件提供了处理参数化时间的方法。

1. 针对所需的每个变量参数值添加一个 Web 测试插件。 在 Web 测试工具栏中，选择“添加 Web 测试插件”。
    
    ![添加 Web 测试插件](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    本示例使用两个日期时间插件实例。 一个实例设置为“15 分钟前”，另一个实例设置为“现在”。

2. 打开每个插件的属性。 为插件命名，然后将它设置为使用当前时间。 对于其中一个插件，将“添加分钟”设置为 -15。

    ![上下文参数](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 在 Web 测试参数中，使用 {{plug-in name}} 来引用插件名称。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

现在，将测试上传到门户。 它会在每次运行测试时使用动态值。

### <a name="dealing-with-sign-in"></a>处理登录

如果用户登录应用，可以使用许多选项来模拟登录，以便可以在登录后测试页面。 使用的方法取决于应用提供的安全性类型。

在所有情况下，应该只针对测试目的在应用程序中创建帐户。 如果可能，请限制此测试帐户的权限，以便 Web 测试不会影响实际用户。

**简单的用户名和密码** 按常规方式录制 Web 测试。 先删除 Cookie。

**SAML 身份验证**

|属性名称| 说明|
|----|-----|
| 受众 URI | SAML 令牌的受众 URI。  这是访问控制服务 (ACS) 的 URI，包括 ACS 命名空间和主机名。 |
| 证书密码 | 将授予对嵌入式私钥的访问权限的客户端证书的密码。 |
| 客户端证书  | 具有 Base64 编码格式的私钥的客户端证书值。 |
| 名称标识符 | 令牌的名称标识符。 |
| 不晚于 | 令牌有效的时间跨度。  默认为 5 分钟。 |
| 不早于 | 过去创建令牌的时间跨度将有效（用于解决时间差异）。  默认值为（负）5 分钟。 |
| 目标上下文参数名称 | 将接收生成断言的上下文参数。 |


**客户端机密** 如果应用的某个登录路由涉及到客户端机密，请使用该路由。 例如，Azure Active Directory (AAD) 就是提供客户端机密登录的服务。 在 AAD 中，客户端机密是应用密钥。

下面是使用应用密钥的 Azure Web 应用的 Web 测试示例：

![示例屏幕截图](./media/availability-multistep/client-secret.png)

使用客户端机密 (AppKey) 从 AAD 获取令牌。
从响应中提取持有者令牌。
使用授权标头中的持有者令牌调用 API。
确保 Web 测试是实际客户端（即在 AAD 中有自身的应用）并使用其 clientId 和 app key。 测试中的服务在 AAD 中也有自身的应用：此应用的 appID URI 反映在“resource”字段中的 Web 测试内。

### <a name="open-authentication"></a>开放身份验证
开放身份验证的示例包括使用 Microsoft 或 Google 帐户登录。 许多使用 OAuth 的应用提供替代的客户端机密，因此第一个技巧就是调查这种可能性。

如果测试必须使用 OAuth 登录，则常规方法是：

使用 Fiddler 等工具检查 Web 浏览器、身份验证站点与应用之间的流量。
使用不同的计算机或浏览器或者以较长的间隔执行两次以上的登录（使令牌过期）。
通过比较不同的会话，识别从身份验证站点返回的令牌，然后在登录后将此令牌传递给应用服务器。
使用 Visual Studio 录制 Web 测试。
参数化令牌，设置参数来指定从验证器返回令牌的时间，并在站点查询中使用该参数。 （Visual Studio 会尝试参数化测试，但无法正确参数化令牌。）

## <a name="troubleshooting"></a>疑难解答

专用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [URI ping Web 测试](monitor-web-app-availability.md)
