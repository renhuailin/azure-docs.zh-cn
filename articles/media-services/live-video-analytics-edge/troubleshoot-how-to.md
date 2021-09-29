---
title: 对 IoT Edge 上的实时视频分析进行故障排除 - Azure
description: 本文介绍对 IoT Edge 上的实时视频分析进行故障排除的步骤。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: b7594a8d5e958b05c001f3943437e86b5830bd80
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667077"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>对 IoT Edge 上的实时视频分析进行故障排除

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

本文介绍对 Azure IoT Edge 上的实时视频分析 (LVA) 进行故障排除的步骤。

## <a name="troubleshoot-deployment-issues"></a>排查部署问题

### <a name="diagnostics"></a>诊断

在部署实时视频分析的过程中，需设置 Azure 资源，如 IoT 中心和 IoT Edge 设备。 作为诊断问题的第一步，请务必确保按照以下说明正确设置 Edge 设备：

1. [运行 `check` 命令](../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [检查 IoT Edge 版本](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [检查 IoT Edge 安全管理器的状态及其日志](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)。
1. [查看经过 IoT Edge 中心的消息](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [重启容器](../../iot-edge/troubleshoot.md#restart-containers)。
1. [检查防火墙和端口配置规则](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="pre-deployment-issues"></a>部署前的问题

如果边缘基础结构正常，则可以查找部署清单文件的问题。 若要在 IoT Edge 设备上与任何其他 IoT 模块一起部署 IoT Edge 模块上的实时视频分析，请使用包含 IoT Edge 中心、IoT Edge 代理和其他模块及其属性的部署清单。 可以使用以下命令部署清单文件：

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
如果 JSON 代码格式不正确，则可能会收到以下错误：   
&nbsp;&nbsp;&nbsp;未能分析参数“content”的文件“\<deployment manifest.json\>”中的 JSON，出现异常：“额外数据：第 101 行第 1 列 (char 5325)”

如果遇到此错误，建议检查 JSON 中是否缺少括号或存在文件结构的其他问题。 可以使用客户端（如 [Notepad++ 与 JSON Viewer 插件](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)）或联机工具（如 [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/)）来验证文件结构。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>在部署期间：用媒体图直接方法诊断 

在 IoT Edge 设备上正确部署 IoT Edge 模块上的实时视频分析后，可以通过调用[直接方法](direct-methods.md)来创建和运行媒体图。  
>[!NOTE]
>  直接方法调用只应对 `lvaEdge` 模块使用。

可以使用 Azure 门户通过直接方法来运行媒体图诊断：

1. 在 Azure 门户中，转到连接到 IoT Edge 设备的 IoT 中心。

1. 查找“自动设备管理”，然后选择“IoT Edge” 。  

1. 在 Edge 设备列表中，选择要诊断的设备。  
         
    ![显示 Edge 设备列表的 Azure 门户的屏幕截图](./media/troubleshoot-how-to/lva-sample-device.png)


1. 检查响应代码是否为 200-OK。 [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)的其他响应代码包括：
    * 400 - 部署配置格式不正确或无效。
    * 417 - 没有为设备设置部署配置。
    * 412 - 部署配置中的架构版本无效。
    * 406 - IoT Edge 设备脱机或不发送状态报告。
    * 500 - IoT Edge 运行时中出现了一个错误。

    > [!TIP]
    > 如果在环境中运行 Azure IoT Edge 模块时遇到问题，请使用 [Azure IoT Edge 标准诊断步骤](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)作为故障排除和诊断的指南。
### <a name="post-deployment-direct-method-error-code"></a>后期部署：直接方法错误代码
1. 如果收到状态 `501 code`，请检查以确保直接方法名称正确。 如果方法名称和请求有效负载准确，则应获得结果，并显示成功代码 =200。 
1. 如果请求有效负载不准确，将显示状态 `400 code` 以及指示错误代码和消息的响应有效负载，这些错误代码和消息应该有助于诊断直接方法调用的问题。
    * 检查报告的属性和所需属性有助于了解模块属性是否已与部署同步。 如果没有，可以重启 IoT Edge 设备。 
    * 使用[直接方法](direct-methods.md)指南来调用一些方法，尤其是一些简单的方法，如 GraphTopologyList。 本指南还指定了所需的请求和响应有效负载以及错误代码。 简单的直接方法成功后，可以确保实时视频分析 IoT Edge 模块功能正常。
        
       ![IoT Edge 模块的“直接方法”窗格的屏幕截图。](./media/troubleshoot-how-to/direct-method.png) 

1. 如果“在部署中指定”和“由设备报告”列指示“是”，则可以针对 IoT Edge 模块上的实时视频分析调用直接方法 。 选择该模块后，会转到一个页面，可以在其中查看所需的属性和报告的属性，并可以调用直接方法。 请记住以下几点： 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>后期部署：在运行期间诊断日志中的问题 

IoT Edge 模块的容器日志应包含诊断信息，以帮助调试模块运行时的问题。 可以[检查容器日志中的问题](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)，并对问题进行自我诊断。 

如果你已经运行了前面的所有检查，但仍然遇到问题，请使用 [`support bundle` 命令](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)从 IoT Edge 设备收集日志，以供 Azure 团队进一步分析。 可以[与我们联系](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以获取支持，并提交收集的日志。

## <a name="common-error-resolutions"></a>常见错误解决方法

实时视频分析作为 IoT Edge 模块部署到 Edge 设备上，并且与 IoT Edge 代理和中心模块协作。 在部署实时视频分析时会遇到的一些常见错误是由底层 IoT 基础结构的问题导致的。 这些错误包括：

* [IoT Edge 代理在大约一分钟后停止](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
* [IoT Edge 代理无法访问某个模块的映像 (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
* [IoT Edge 代理模块报告“配置文件为空”，且设备上不会启动任何模块](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
* [IoT Edge 中心未能启动](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
* [由于主机名无效，IoT Edge 安全守护程序失败](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
* [实时视频分析或任何其他自定义 IoT Edge 模块无法将消息发送到边缘中心，出现 404 错误](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
* [IoT Edge 模块部署成功，然后从设备中消失](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

    > [!TIP]
    > 如果在环境中运行 Azure IoT Edge 模块时遇到问题，请使用 [Azure IoT Edge 标准诊断步骤](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)作为故障排除和诊断的指南。

运行[实时视频分析资源安装脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)时可能会遇到问题。 一些常见问题包括：

* 在没有所有者权限的情况下使用订阅。 这将导致脚本失败，并出现 ForbiddenError 或 AuthorizationFailed 错误 。
    * 要解决此问题，请确保你对计划使用的订阅拥有所有者权限。 如果不能自己完成此操作，请与订阅管理员联系以授予适当权限。
* **由于策略冲突而导致模板部署失败。**
    * 要解决此问题，请与 IT 管理员合作，确保创建虚拟机的调用绕过阻止 ssh 身份验证。 这不是必需的，因为我们使用的是需要用户名和密码才能与 Azure 资源通信的安全堡垒网络。 成功创建虚拟机，将其部署并附加到 IoT 中心后，这些凭据将存储在 Cloud Shell 的 ~/clouddrive/lva-sample/vm-edge-device-credentials.txt 文件中。
* 安装脚本无法创建服务主体和/或 Azure 资源。
    * 若要解决此问题，请检查你的订阅和 Azure 租户是否未达到其最大服务限制。 详细了解 [Azure AD 服务限制和局限性](../../active-directory/enterprise-users/directory-service-limits-restrictions.md)以及 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

> [!TIP]
> 如果有任何其他可能需要帮助的问题，请[收集日志并提交支持票证](#collect-logs-for-submitting-a-support-ticket)。 还可以通过发送电子邮件至 [amshelp@microsoft.com](mailto:amshelp@microsoft.com) 与我们联系。
### <a name="live-video-analytics-working-with-external-modules"></a>用于外部模块的实时视频分析

通过媒体图扩展处理器的实时视频分析可以扩展媒体图，以使用 HTTP 或 gRPC 协议发送和接收来自其他 IoT Edge 模块的数据。 作为[特定示例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)，媒体图可以通过 HTTP 协议将视频帧作为图像发送到外部推理模块（如 Yolo v3），并接收基于 JSON 的分析结果。 在这种拓扑中，事件的目标主要是 IoT 中心。 如果在中心上看不到推理事件，请检查以下各项：

* 检查媒体图要发布到的中心是否与要检查的中心相同。 创建多个部署时，最终可能会获得多个中心，并可能会错误地检查错误的事件中心。
* 在 Azure 门户中，检查外部模块是否已部署并正在运行。 在此处的示例图中，rtspsim、yolov3、tinyyolov3 和 logAnalyticsAgent 是在 lvaEdge 模块外部运行的 IoT Edge 模块。

    [ ![显示 Azure IoT 中心内模块运行状态的屏幕截图。](./media/troubleshoot-how-to/iot-hub-azure.png) ](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* 检查是否要将事件发送到正确的 URL 终结点。 外部 AI 容器公开一个 URL 和一个端口，通过该端口接收并返回 POST 请求中的数据。 此 URL 被指定为 HTTP 扩展处理器的 `endpoint: url` 属性。 如[拓扑 URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) 中所示，终结点设置为推理 URL 参数。 请确保参数的默认值或传入的值是准确的。 可以使用客户端 URL (cURL) 来测试它是否正常工作。  

    例如，下面是一个在本地计算机上运行的 Yolo v3 容器，其 IP 地址为 172.17.0.3。  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返回的结果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > 使用 [Docker 检查命令](https://docs.docker.com/engine/reference/commandline/inspect/)查找计算机的 IP 地址。
    
* 如果正在使用媒体图扩展处理器运行图形的一个或多个实例，则应使用 `samplingOptions` 字段来管理视频源的每秒帧数 (fps)。 

   * 在某些情况下，边缘计算机的 CPU 或内存使用率很高，可能会丢失某些推理事件。 若要解决此问题，请在 `samplingOptions` 字段上将 `maximumSamplesPerSecond` 属性设置为低值。 可以在图的每个实例上将其设置为 0.5 ("maximumSamplesPerSecond":"0.5")，然后重新运行该实例以检查中心上的推理事件。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>并行的多个直接方法 - 超时失败 

IoT Edge 上的实时视频分析提供了一种基于直接方法的编程模型，该模型支持设置多个拓扑和多个图形实例。 在拓扑和图形设置的过程中，可在 IoT Edge 模块上调用多个直接方法调用。 如果并行调用了多个方法调用，特别是启动和停止图形的方法调用，则可能会遇到一些超时故障，如下面所示： 

程序集初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 引发了异常。 Microsoft.Azure.Devices.Common.Exceptions.IotHubException：Microsoft.Azure.Devices.Common.Exceptions.IotHubException：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

建议不要并行调用直接方法。 按顺序调用它们（也就是说，仅在前一个直接方法调用完成之后才进行另一个直接方法调用）。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>收集日志以提交支持票证

如果自助式故障排除步骤无法解决问题，请转到 Azure 门户，然后[打开支持票证](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> 这些日志可能包含个人身份信息 (PII)，例如 IP 地址。 一旦检查完日志的所有本地副本并关闭支持票证，就会立即删除这些副本。  

若要收集应添加到票证的相关日志，请遵循以下说明进行操作，并将日志文件上传到支持请求的“详细信息”窗格中。  
1. [配置实时视频分析模块以收集详细日志](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [启用调试日志](#live-video-analytics-debug-logs)
1. 再现问题
1. 从门户中的“IoT 中心”页连接到虚拟机
    1. 压缩 debugLogs 文件夹中的所有文件。

       > [!NOTE]
       > 这些日志文件不用于自我诊断。 它们是供 Azure 工程团队使用以分析你的问题。

       * 在以下命令中，请务必将 $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 替换为之前在步骤 2 中设置的边缘设备上的调试日志的位置 。  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. 将 debugLogs.zip 文件附加到支持票证。
1. 运行 [support bundle 命令](#use-the-support-bundle-command)，收集日志并附加到支持票证。

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>配置实时视频分析模块以收集详细日志
配置实时视频分析模块，通过设置 `logLevel` 和 `logCategories` 来收集详细日志，如下所示：
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

可通过两种方式实现此目的：
* 在 Azure 门户中，更新实时视频分析模块的模块标识孪生属性   [ ![模块标识孪生属性。](media/troubleshoot-how-to/module-twin.png) ](media/troubleshoot-how-to/module-twin.png#lightbox)    
* 或者可以在部署清单文件中，在实时视频分析模块的属性节点中添加这些项

### <a name="use-the-support-bundle-command"></a>使用 support-bundle 命令

需要从 IoT Edge 设备收集日志时，最简单的方法是使用 `support-bundle` 命令。 此命令收集：

- 模块日志
- IoT Edge 安全管理器和容器引擎日志
- IoT Edge 检查 JSON 输出
- 有用的调试信息

1. 运行带有 --since 标志的 `support-bundle` 命令以指定你希望日志覆盖的时间。 例如，指定为 2h 将获得最近两个小时的日志。 可以更改此标志的值，以包含不同时间段的日志。

    ```
    sudo iotedge support-bundle --since 2h
    ```

   此命令将在运行命令的目录中创建一个名为 support_bundle.zip 的文件。 
   
1. 将 support_bundle.zip 文件附加到支持票证。

### <a name="live-video-analytics-debug-logs"></a>实时视频分析调试日志

若要在 IoT Edge 模块上配置实时视频分析以生成调试日志，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并转到 IoT 中心。
1. 在左窗格中，选择“IoT Edge”。
1. 在设备列表中，选择目标设备的 ID。
1. 在窗格顶部，选择“设置模块”。

   ![Azure 门户中“设置模块”按钮的屏幕截图。](media/troubleshoot-how-to/set-modules.png)

1. 在“IoT Edge 模块”部分中，查找并选择“lvaEdge” 。
1. 选择“容器创建选项”。
1. 在“绑定”部分中，添加以下命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 此命令将绑定边缘设备和容器之间的日志文件夹。 如果要在其他位置收集日志，请使用以下命令，将 $LOG_LOCATION_ON_EDGE_DEVICE 替换为想要使用的位置：`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. 选择“更新”。
1. 选择“查看 + 创建”  。 成功的验证消息将发布在绿色横幅下。
1. 选择“创建”。
1. 更新“模块标识孪生”，以指向 DebugLogsDirectory 参数，该参数指向收集日志的目录：

    a. 在“模块”表下，选择“lvaEdge” 。  
    b. 在窗格顶部，选择“模块标识孪生”。 此时将打开一个可编辑窗格。  
    c. 在“所需的密钥”下，添加以下键/值对：  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 此命令将绑定边缘设备和容器之间的日志文件夹。 如果要在设备上的其他位置收集日志：
    > 1. 在“绑定”部分中，为调试日志位置创建一个绑定，将 $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 和 $DEBUG_LOG_LOCATION 替换为想要使用的位置：`/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`  
    > 2. 使用以下命令，将 $DEBUG_LOG_LOCATION 替换为上一步中使用的位置：  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. 选择“保存”。


1. 可以通过将“模块标识孪生”中的值设置为 NULL 来停止日志收集。 返回“模块标识孪生”页，并将以下参数更新为：

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>有关日志记录的最佳实践

[监视和日志记录](monitoring-logging.md)应有助于理解分类以及如何生成日志，以帮助调试 LVA 问题。 

由于每种语言的 gRPC 服务器实现各不相同，因此没有在服务器内部添加日志记录的标准方法。  

例如，如果使用 .NET Core 构建 gRPC 服务器，gRPC 服务将在“Grpc”类别下添加日志。 若要启用来自 gRPC 的详细日志，请通过将以下项添加到日志记录中的 LogLevel 子部分，在 appsettings.json 文件中将 Grpc 前缀配置为调试级别： 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

也可以使用 ConfigureLogging 在 Startup.cs 文件中进行配置： 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[.NET 上 gRPC 中的日志记录和诊断](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1)为从 gRPC 服务器收集一些诊断日志提供了一些指导。 

### <a name="a-failed-grpc-connection"></a>失败的 gRPC 连接 

如果图形处于活动状态并正通过照相机进行流式处理，则连接将由实时视频分析进行维护。 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>监视和平衡 CPU 和 GPU 资源在这些资源成为瓶颈时的负载

实时视频分析不监视或不提供任何硬件资源监视。 开发人员必须使用硬件制造商监视解决方案。 但是，如果使用的是 Kubernetes 容器，则可以使用 [Kubernetes 仪表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)监视设备。 

.NET Core 文档中的 gRPC 还共享有关[性能最佳做法](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1)和[负载平衡](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing)的一些有价值的信息。  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>当推理服务器未收到任何帧，且你收到了“未知”协议错误时，将对推理服务器进行故障排除 

若要获取有关该问题的详细信息，可以执行以下几项操作。  

* 在实时视频分析模块的所需属性中包含“ediaPipeline”日志类别，并确保日志级别设置为 `Information`。  
* 若要测试网络连接，可以在边缘设备上运行以下命令。 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   如果命令输出的是一串乱七八糟的文本，则 telnet 可以成功打开与推理服务器的连接，并打开二进制 gRPC 通道。 如果未看到这种情况，则 telnet 将报告网络错误。 
* 在推理服务器中，可以在 gRPC 库中启用其他日志记录。 这可能会提供有关 gRPC 通道本身的其他信息。 这种操作因语言而异，一下是 [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) 的说明。 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>从 gRPC 缓冲区中选取更多图像，而不会向第一个缓冲区发送回结果

作为 gRPC 数据传输协定的一部分，应确认实时视频分析发送到 gRPC 推理服务器的所有消息。 不确认收到图像帧会破坏数据协定，并可能导致意外情况。  

若要将 gRPC 服务器与实时视频分析配合使用，可以使用共享内存来获得最佳性能。 此操作要求使用由编程语言/环境公开的 Linux 共享内存功能。 

1. 打开 Linux 共享内存句柄。
1. 收到帧后，访问共享内存中的地址偏移量。
1. 确认帧处理完成，使实时视频分析可以回收其内存。

   > [!NOTE]
   > 如果长时间延迟向实时视频分析确认接收到帧，则可能使共享内存变满并导致数据丢失。
1. 将每个帧存储在推理服务器上所选的数据结构（列表、数组等）中。
1. 然后，可以在具有所需数量的图像帧时运行处理逻辑。
1. 准备就绪后，将推理结果返回到实时视频分析。

## <a name="next-steps"></a>后续步骤

[教程：将基于事件的视频录制到云中并从云中播放](event-based-video-recording-tutorial.md)