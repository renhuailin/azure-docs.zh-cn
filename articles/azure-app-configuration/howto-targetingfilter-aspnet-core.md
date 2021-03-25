---
title: 分阶段地向目标受众推出功能
titleSuffix: Azure App Configuration
description: 了解如何分阶段地向目标受众推出功能
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929678"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>分阶段地向目标受众推出功能

借助功能标志，可在应用程序中动态激活或停用功能。 功能筛选器在每次评估功能标志时确定该标志的状态。 `Microsoft.FeatureManagement` 库中有 `TargetingFilter`，它可为指定的一系列用户和组，或者指定的一部分用户启用功能标志。 `TargetingFilter`“具有粘性”。 这意味着单个用户收到某项功能后，将在所有未来请求中一直看到该项功能。 可在演示过程中使用 `TargetingFilter` 为特定帐户启用某项功能，从而向不同的组或“更新圈”等中的用户逐步推出新功能。

在本文中，你将了解如何结合使用 `TargetingFilter` 和 Azure 应用程序配置来向指定的用户和组推出 ASP.NET Core Web 应用中的新功能。

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>创建具有功能标志和身份验证的 Web 应用

若要基于用户和组推出功能，需要使用允许用户登录的 Web 应用。

1. 使用以下命令创建一个对本地数据库进行身份验证的 Web 应用：

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. 生成并运行，然后选择右上角的“注册”链接，新建一个用户帐户。 使用电子邮件地址 `test@contoso.com`。 在“注册确认”屏幕上，选择“单击此处以确认帐户” 。

1. 遵循[快速入门：将功能标志添加到 ASP.NET Core 应用](./quickstart-feature-flag-aspnet-core.md)，从而将功能标志添加到新的 Web 应用。

1. 在应用程序配置中切换功能标志。 验证此操作是否控制导航栏上 Beta 项的可见性。

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>更新 Web 应用代码以使用 TargetingFilter

此时，可使用功能标志为所有用户启用或禁用 `Beta` 功能。 若要为某些用户启用功能标志，同时为其他用户禁用该标志，请更新代码以使用 `TargetingFilter`。 在本例中，你要将登录用户的电子邮件用作用户 ID，将电子邮件地址的域名部分用作组。 你要将用户和组添加到 `TargetingContext`。 `TargetingFilter` 使用此上下文来确定每个请求的功能标志的状态。

1. 更新到最新版的 `Microsoft.FeatureManagement.AspNetCore` 包。

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. 添加 TestTargetingContextAccessor.cs 文件：

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. 在 Startup.cs 中，添加对 Microsoft.FeatureManagement.FeatureFilters 命名空间的引用 ：

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. 调用 `AddFeatureManagement()` 后，更新 ConfigureServices 方法以注册 `TargetingFilter`：

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. 更新 ConfigureServices 方法，将先前步骤中创建的 `TestTargetingContextAccessor` 添加到服务集合。 每次评估功能标志时，TargetingFilter 都用它来确定目标上下文。

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

整个 ConfigureServices 方法将如下所示：

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>更新功能标志以使用 TargetingFilter

1. 在 Azure 门户中，转到应用程序配置存储并选择“功能管理器”。

1. 选择在快速入门中创建的 Beta 功能标志的上下文菜单。 选择“编辑”。

    > [!div class="mx-imgBorder"]
    > ![编辑 Beta 功能标志](./media/edit-beta-feature-flag.png)

1. 如果尚未选中“编辑”屏幕中的“启用功能标志”复选框，请将其选中 。 然后，选中“使用功能筛选器”复选框。

1. 选择“目标”单选按钮。

1. 选择以下选项：

    - **默认百分比**：0
    - **组**：在“名称”中输入 contoso.com，在“百分比”中输入 50
    - **用户**：`test@contoso.com`

    功能筛选器屏幕将如下所示：

    > [!div class="mx-imgBorder"]
    > ![条件功能标志](./media/feature-flag-filter-enabled.png)

    这些设置会导致以下行为：

    - “用户”部分列出了 `test@contoso.com`，因此始终为用户 `test@contoso.com` 启用功能标志。
    - “组”部分列出了 contoso.com，且百分比为 50，因此为 contoso.com 组中其他用户中 50% 的用户启用功能标志    。
    - “默认百分比”设为 0，因此始终为其他所有用户禁用功能 。

1. 选择“应用”来保存这些设置，然后回到“功能管理器”屏幕 。

1. 功能标志的功能筛选器现显示为“目标”。 此状态指示，功能标志将根据“目标”功能筛选器实施的条件，基于每个请求进行启用或禁用。

## <a name="targetingfilter-in-action"></a>TargetingFilter 的实际运用

若要查看此功能标志的效果，请生成并运行应用程序。 一开始，“默认百分比”选项设为 0，因此 Beta 项不在工具栏上显示。

立即使用注册时设置的密码以 `test@contoso.com` 身份登录。 由于 `test@contoso.com` 被指定为目标用户，因此 Beta 项现显示在工具栏上。

以下视频显示了正在起作用的这一行为。

> [!div class="mx-imgBorder"]
> ![TargetingFilter 的实际运用](./media/feature-flags-targetingfilter.gif)

可使用 `@contoso.com` 电子邮件地址创建其他用户，查看组设置的行为。 这些用户中 50% 的用户将看到 Beta 项。 另外 50% 的用户看不到 Beta 项。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [功能管理概述](./concept-feature-management.md)