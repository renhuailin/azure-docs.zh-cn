---
title: 使用 Azure Functions 创建和运行自定义可用性测试
description: 本文档将介绍如何使用 TrackAvailability() 创建一个 Azure 函数，该函数会根据 TimerTrigger 函数中给定的配置定期运行。 此测试的结果将发送到 Application Insights 资源，你可以在其中查询可用性结果数据并对其发出警报。 使用自定义测试，你可以编写比使用门户 UI 更为复杂的可用性测试、监视 Azure VNET 内部的应用、更改终结点地址或创建可用性测试（如果在你所在的区域中不可用）。
ms.topic: conceptual
ms.date: 05/06/2021
ms.openlocfilehash: 891f69bd42dc228e01e7b341630e7f37522f9828
ms.sourcegitcommit: c1b0d0b61ef7635d008954a0d247a2c94c1a876f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2021
ms.locfileid: "109628138"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure Functions 创建和运行自定义可用性测试

本文将介绍如何使用 TrackAvailability() 创建一个 Azure 函数，该函数会根据 TimerTrigger 函数中给定的配置和你自己的业务逻辑定期运行。 此测试的结果将发送到 Application Insights 资源，你可以在其中查询可用性结果数据并对其发出警报。 这样，你就可以创建自定义测试，类似于通过门户的[可用性监视](./monitor-web-app-availability.md)执行的测试。 使用自定义测试，你可以编写比使用门户 UI 更为复杂的可用性测试、监视 Azure VNET 内部的应用、更改终结点地址或创建可用性测试（即使该功能在你所在的区域中不可用）。

> [!NOTE]
> 此示例只是为了向你展示TrackAvailability() API 调用在 Azure 函数中的工作机制， 而不是为了演示如何编写基础 HTTP 测试代码/业务逻辑，后者是将其转变为功能完全正常的可用性测试所需的。 默认情况下，如果逐步完成此示例，你将创建一个基础的可用性 HTTP GET 测试。

## <a name="create-a-timer-trigger-function"></a>创建计时器触发器函数

1. 创建 Azure Functions 资源。
    - 如果已有 Application Insights 资源：
        - 默认情况下，Azure Functions 会创建 Application Insights 资源，但如果你想要使用已创建的资源之一，则需在创建过程中指定。
        - 按照有关如何[创建 Azure Functions 资源](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app)的说明进行以下修改：
            - 在“监视”选项卡上，选择“Application Insights”下拉框，然后键入或选择资源的名称。
                :::image type="content" source="media/availability-azure-functions/app-insights-resource.png" alt-text="在“监视”选项卡上，选择现有的 Application Insights 资源。":::
    - 如果尚未为计时器触发的函数创建 Application Insights 资源：
        - 默认情况下，在创建 Azure Functions 应用程序时，它将为你创建一个 Application Insights 资源。 按照有关如何[创建 Azure Functions 资源](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app)的说明进行操作。
    > [!NOTE]
    > 可以在消耗、高级或应用服务计划中托管函数。 如果测试的是 V 网络或测试非公共终结点，则需要使用高级计划来替换消耗计划。 在“托管”选项卡上选择你的计划。
2. 创建计时器触发器函数。
    1. 在函数应用中，选择“函数”选项卡。
    1. 选择“添加”，然后在“添加函数”选项卡中选择以下配置：
        1. 开发环境：“在门户中开发”
        1. 选择模板：“计时器触发器”
    1. 选择“添加”以创建计时器触发器函数。

    :::image type="content" source="media/availability-azure-functions/add-function.png" alt-text="显示如何向函数应用添加计时器触发器函数的屏幕截图。" lightbox="media/availability-azure-functions/add-function.png":::

## <a name="add-and-edit-code-in-the-app-service-editor"></a>在应用服务编辑器中添加和编辑代码

导航到已部署的函数应用，然后在“开发工具”下选择“应用服务编辑器”选项卡。

若要创建新文件，请在计时器触发器函数下右键单击（例如“TimerTrigger1”），然后选择“新建文件”。 然后键入该文件的名称并按 enter。

1. 创建名为“function.proj”的新文件，然后将以下代码粘贴到其中：

    ```xml
    <Project Sdk="Microsoft.NET.Sdk"> 
        <PropertyGroup> 
            <TargetFramework>netstandard2.0</TargetFramework> 
        </PropertyGroup> 
        <ItemGroup> 
            <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version --> 
        </ItemGroup> 
    </Project> 
    
    ```

     :::image type="content" source="media/availability-azure-functions/function-proj.png" alt-text=" 应用服务编辑器中的 function.proj 的屏幕截图。" lightbox="media/availability-azure-functions/function-proj.png":::

2. 创建名为“runAvailabilityTest.csx”的新文件，然后将以下代码粘贴到其中：

    ```csharp
    using System.Net.Http; 
    
    public async static Task RunAvailabilityTestAsync(ILogger log) 
    { 
        using (var httpClient = new HttpClient()) 
        { 
            // TODO: Replace with your business logic 
            await httpClient.GetStringAsync("https://www.bing.com/"); 
        } 
    } 
    ```

3. 将以下代码复制到 run.csx 文件中（这将替换预先存在的代码）：

    ```csharp
    #load "runAvailabilityTest.csx" 
    
    using System; 
    
    using System.Diagnostics; 
    
    using Microsoft.ApplicationInsights; 
    
    using Microsoft.ApplicationInsights.Channel; 
    
    using Microsoft.ApplicationInsights.DataContracts; 
    
    using Microsoft.ApplicationInsights.Extensibility; 
    
    private static TelemetryClient telemetryClient; 
    
    // ============================================================= 
    
    // ****************** DO NOT MODIFY THIS FILE ****************** 
    
    // Business logic must be implemented in RunAvailabilityTestAsync function in runAvailabilityTest.csx 
    
    // If this file does not exist, please add it first 
    
    // ============================================================= 
    
    public async static Task Run(TimerInfo myTimer, ILogger log, ExecutionContext executionContext) 
    
    { 
        if (telemetryClient == null) 
        { 
            // Initializing a telemetry configuration for Application Insights based on connection string 
    
            var telemetryConfiguration = new TelemetryConfiguration(); 
            telemetryConfiguration.ConnectionString = Environment.GetEnvironmentVariable("APPLICATIONINSIGHTS_CONNECTION_STRING"); 
            telemetryConfiguration.TelemetryChannel = new InMemoryChannel(); 
            telemetryClient = new TelemetryClient(telemetryConfiguration); 
        } 
    
        string testName = executionContext.FunctionName; 
        string location = Environment.GetEnvironmentVariable("REGION_NAME"); 
        var availability = new AvailabilityTelemetry 
        { 
            Name = testName, 
    
            RunLocation = location, 
    
            Success = false, 
        }; 
    
        availability.Context.Operation.ParentId = Activity.Current.SpanId.ToString(); 
        availability.Context.Operation.Id = Activity.Current.RootId; 
        var stopwatch = new Stopwatch(); 
        stopwatch.Start(); 
    
        try 
        { 
            using (var activity = new Activity("AvailabilityContext")) 
            { 
                activity.Start(); 
                availability.Id = Activity.Current.SpanId.ToString(); 
                // Run business logic 
                await RunAvailabilityTestAsync(log); 
            } 
            availability.Success = true; 
        } 
    
        catch (Exception ex) 
        { 
            availability.Message = ex.Message; 
            throw; 
        } 
    
        finally 
        { 
            stopwatch.Stop(); 
            availability.Duration = stopwatch.Elapsed; 
            availability.Timestamp = DateTimeOffset.UtcNow; 
            telemetryClient.TrackAvailability(availability); 
            telemetryClient.Flush(); 
        } 
    } 
    
    ```

## <a name="check-availability"></a>检查可用性

为了确保一切正常工作，可以查看 Application Insights 资源的“可用性”选项卡中的图。

> [!NOTE]
> 使用 TrackAvailability() 创建的测试显示时会在测试名称旁边带有“CUSTOM”字样。

 :::image type="content" source="media/availability-azure-functions/availability-custom.png" alt-text="显示成功结果的“可用性”选项卡。" lightbox="media/availability-azure-functions/availability-custom.png":::

若要查看端到端事务详细信息，请在“深入钻取”下选择“成功”或“失败”，然后选择一个示例。 还可以通过选择图上的数据点来访问端到端事务详细信息。

:::image type="content" source="media/availability-azure-functions/sample.png" alt-text="选择示例可用性测试。":::

:::image type="content" source="media/availability-azure-functions/end-to-end.png" alt-text="端到端事务详细信息。" lightbox="media/availability-azure-functions/end-to-end.png":::

## <a name="query-in-logs-analytics"></a>在“日志(分析)”中查询

可以使用“日志(分析)”查看可用性结果、依赖关系等。 若要详细了解“日志”，请访问[日志查询概述](../logs/log-query-overview.md)。

:::image type="content" source="media/availability-azure-functions/availabilityresults.png" alt-text="可用性结果。" lightbox="media/availability-azure-functions/availabilityresults.png":::

:::image type="content" source="media/availability-azure-functions/dependencies.png" alt-text="屏幕截图显示了“新建查询”选项卡，其依赖项数量限制为 50。" lightbox="media/availability-azure-functions/dependencies.png":::

## <a name="next-steps"></a>后续步骤

- [应用程序映射](./app-map.md)
- [事务诊断](./transaction-diagnostics.md)
