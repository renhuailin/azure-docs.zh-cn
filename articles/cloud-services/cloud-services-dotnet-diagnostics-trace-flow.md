---
title: 使用 Azure 诊断跟踪云服务（经典）应用程序中的流
description: 将跟踪消息添加到 Azure 应用程序中，以协作进行调试、性能度量、监视、流量分析等。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 027450a2e5e2a0c749c2f4b02148ffe849f2a182
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823937"
---
# <a name="trace-the-flow-of-a-cloud-services-classic-application-with-azure-diagnostics"></a>使用 Azure 诊断跟踪云服务（经典）应用程序的流

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

跟踪是在应用程序运行时监视其执行情况的一种方式。 可以使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace)、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 和 [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) 类在日志、文本文件或其他设备中记录与错误及应用程序执行情况相关的信息，供以后进行分析。 有关跟踪的详细信息，请参阅[跟踪和检测应用程序](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)。

## <a name="use-trace-statements-and-trace-switches"></a>使用 Trace 语句和 Trace 开关
在云服务应用程序中实施跟踪时，可以将 [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) 添加到应用程序配置中，并在应用程序代码中调用 System.Diagnostics.Trace 或 System.Diagnostics.Debug。 对辅助角色使用配置文件 *app.config*，对 Web 角色使用配置文件 *web.config*。 使用 Visual Studio 模板创建新的托管服务时，系统会针对你所添加的角色将 Azure 诊断自动添加到项目中，并将 DiagnosticMonitorTraceListener 添加到相应的配置文件中。

有关如何放置 Trace 语句的信息，请参阅[如何：向应用程序代码添加 Trace 语句](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)。

可以将 [Trace 开关](/dotnet/framework/debug-trace-profile/trace-switches)放置到代码中，从而控制是否进行跟踪以及跟踪的范围。 这样即可在生产环境中监视应用程序的状态。 这在业务应用程序中特别重要，因为业务应用程序会在多个计算机中使用多个运行的组件。 有关详细信息，请参阅[如何：配置 Trace 开关](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches)。

## <a name="configure-the-trace-listener-in-an-azure-application"></a>在 Azure 应用程序中配置跟踪侦听器
Trace、Debug 和 TraceSource 都要求设置“侦听器”来收集和记录已发送的消息。 侦听器将收集、存储和路由跟踪消息。 它们会将跟踪输出传输到适当的目标，如日志、窗口或文本文件。 Azure 诊断使用 [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) 类。

完成以下过程之前，必须初始化 Azure 诊断监视器。 若要执行此操作，请参阅[在 Microsoft Azure 中启用诊断](cloud-services-dotnet-diagnostics.md)。

请注意，如果使用的模板是 Visual Studio 提供的，则会自动添加侦听器的配置。

### <a name="add-a-trace-listener"></a>添加跟踪侦听器
1. 打开针对角色的 web.config 或 app.config 文件。
2. 将以下代码添加到文件。 更改 Version 属性，以使用引用的程序集的版本号。 除非有所更新，否则程序集的版本不一定随着每个 Azure SDK 发行版发生变化。
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > 确保与 Microsoft.WindowsAzure.Diagnostics 程序集建立项目引用。 更新上述 xml 中的版本号，以便与引用的 Microsoft.WindowsAzure.Diagnostics 程序集的版本匹配。
   > 
   > 
3. 保存 config 文件。

有关侦听器的详细信息，请参阅[跟踪侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)。

完成添加侦听器的步骤后，即可将 Trace 语句添加到代码中。

### <a name="to-add-trace-statement-to-your-code"></a>将 Trace 语句添加到代码中
1. 打开应用程序的源文件。 例如用于辅助角色或 Web 角色的 \<RoleName>.cs 文件。
2. 添加以下 using 指令（如果尚未添加）：
    ```
        using System.Diagnostics;
    ```
3. 添加 Trace 语句，以便捕获有关应用程序状态的信息。 可以使用多种方法来来格式化 Trace 语句的输出。 有关详细信息，请参阅[如何：向应用程序代码添加 Trace 语句](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)。
4. 保存源文件。




