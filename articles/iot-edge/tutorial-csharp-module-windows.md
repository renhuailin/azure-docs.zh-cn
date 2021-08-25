---
title: 教程 - 使用 Azure IoT Edge 开发适用于 Windows 的 C# 模块
description: 本教程介绍如何使用 C# 代码创建 IoT Edge 模块并将其部署到运行 IoT Edge 的 Windows 设备。
services: iot-edge
author: kgremban
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d7fe67fa9722328be1e730c18893032f98883df7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740639"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>教程：使用 Windows 容器开发 C# IoT Edge 模块

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文介绍如何使用 Visual Studio 开发 C# 代码并将其部署到运行 Azure IoT Edge 的 Windows 设备。

>[!NOTE]
>IoT Edge 1.1 LTS 是最后一个支持 Windows 容器的发布通道。 从版本 1.2 开始，将不再支持 Windows 容器。 请考虑使用或移动至 [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) 以在 Windows 设备上运行 IoT Edge。

可以使用 Azure IoT Edge 模块来部署直接在 IoT Edge 设备中实现业务逻辑的代码。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用 Visual Studio 创建基于 C# SDK 的 IoT Edge 模块。
> * 使用 Visual Studio 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 模块仅在温度超过指定阈值时向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

本教程演示如何使用 Visual Studio 2019 在 C# 中开发模块，以及如何将其部署到 Windows 设备。 如果要使用 Linux 容器开发模块，请改为参阅[使用 Linux 容器开发 C# IoT Edge 模块](tutorial-csharp-module.md)。

若要了解使用 Windows 容器开发和部署 C# 模块的选项，请参阅下表：

| C# | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;和&nbsp;2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64 开发 | ![在 Visual Studio Code 中开发 WinAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) | ![在 Visual Studio 中开发 WinAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) |
| Windows AMD64 调试 |   | ![在 Visual Studio 中调试 WinAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) |

在开始学习本教程之前，请按照[使用 Windows 容器开发 IoT Edge 模块](tutorial-develop-for-windows.md)教程中的说明设置开发环境。 完成后，你的环境会包含以下必备组件：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 Windows 设备](how-to-install-iot-edge-windows-on-windows.md)。
* 一个容器注册表，例如 [Azure 容器注册表](../container-registry/index.yml)。
* 配置了 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 扩展的 [Visual Studio 2019](/visualstudio/install/install-visual-studio)。
* 配置为运行 Windows 容器的 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。

> [!TIP]
> 如果使用的是 Visual Studio 2017（版本 15.7 或更高版本），请从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) 下载并安装适用于 Visual Studio 2017 的 Azure IoT Edge Tools。

## <a name="create-a-module-project"></a>创建模块项目

在本部分，我们使用 Visual Studio 和 Azure IoT Edge Tools 扩展创建 IoT Edge 模块项目。 创建项目模板后，你将添加新代码，使模块根据其报告属性筛选出消息。

### <a name="create-a-new-project"></a>创建新项目

Azure IoT Edge Tools 为 Visual Studio 中支持的所有 IoT Edge 模块语言提供项目模板。 这些模板包含部署用于测试 IoT Edge 的工作模块所需的所有文件和代码。 你也可以从它们着手，使用你自己的业务逻辑对它们进行自定义。

1. 打开 Visual Studio 2019，然后选择“创建新项目”。

1. 在“创建新项目”窗格上，搜索“IoT Edge”，然后在结果列表中，选择“Azure IoT Edge (Windows amd64)”项目  。

   ![IoT Edge“创建新项目”窗格的屏幕截图。](./media/tutorial-csharp-module-windows/new-project.png)

1. 选择“**下一页**”。

    “配置新项目”窗格随即打开。

   ![“配置新项目”窗格的屏幕截图。](./media/tutorial-csharp-module-windows/configure-project.png)

1. 在“配置新项目”窗格中，重命名项目和解决方案，使名称更具描述性，例如 CSharpTutorialApp 。 

1. 选择“创建”来创建项目。

   “添加模块”窗格随即打开。

   ![用于配置项目的“添加模块”窗格的屏幕截图。](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. 在“配置新项目”页上执行以下操作：

   a. 在左侧窗格中，选择“C# 模块”模板。  
   b. 在“模块名称”框中，输入“CSharpModule” 。  
   c. 在“存储库 URL”框中，将 localhost:5000 替换为 Azure 容器注册表中的“登录服务器”值，格式如下：`<registry name>.azurecr.io/csharpmodule`  

    > [!NOTE]
    > 映像存储库包含容器注册表的名称和容器映像的名称。 系统已基于模块项目名称值预先填充容器映像。  可以在 Azure 门户的容器注册表的“概述”页面中检索登录服务器。

1. 选择“添加”  以创建项目。

### <a name="add-your-registry-credentials"></a>添加注册表凭据

部署清单与 IoT Edge 运行时共享容器注册表的凭据。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 使用 Azure 容器注册表的“访问密钥”部分提供的凭据。 

1. 在 Visual Studio 解决方案资源管理器中打开 deployment.template.json 文件。

1. 在 $edgeAgent 所需属性中查找 registryCredentials 属性。 该属性的注册表地址应使用创建项目时提供的信息自动填充。 用户名和密码字段应包含变量名称。 例如：

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. 在模块解决方案中打开环境 (ENV) 文件。 默认情况下，此文件会隐藏在解决方案资源管理器中，可能需要选择“显示所有文件”按钮才能显示它。 该 ENV 文件应包含与你在 deployment.template.json 文件中看到的相同的用户名和密码变量。

1. 从 Azure 容器注册表中添加“用户名”和“密码”值。  

1. 保存对 ENV 文件所做的更改。

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

默认模块代码在输入队列上接收消息，并通过输出队列传递消息。 让我们添加一些额外的代码，以便模块在将消息转发到 IoT 中心之前，在边缘处理消息。 更新模块，以便模块分析每条消息中的温度数据，只有在温度超过特定阈值时才将消息发送到 IoT 中心。

1. 在 Visual Studio 中选择“CSharpModule” > “Program.cs” 。

1. 在 **CSharpModule** 命名空间的顶部，为稍后要使用的类型添加三个 **using** 语句：

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. 将 **temperatureThreshold** 变量添加到 counter 变量后面的 **Program** 类中。 temperatureThreshold 变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. 将 **MessageBody**、**Machine** 和 **Ambient** 类添加到变量声明后面的 **Program** 类中。 这些类将为传入消息的正文定义所需的架构。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

1. 查找 Init 方法。 此方法创建并配置 **ModuleClient** 对象，使模块能够连接到本地 Azure IoT Edge 运行时以发送和接收消息。 代码还会注册一个回调，以通过 **input1** 终结点从 IoT Edge 中心接收消息。

   将整个 Init 方法替换为以下代码：

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   更新的此 Init 方法仍使用 ModuleClient 与 IoT Edge 运行时建立连接，但同时添加了新的功能。 它读取模块孪生的所需属性，以检索 **temperatureThreshold** 值。 然后，它创建一个回调，用于侦听将来对模块孪生所需属性做出的任何更新。 使用此回调可以远程更新模块孪生中的温度阈值，而更改将合并到模块中。

   更新的 Init 方法还会更改现有的 **SetInputMessageHandlerAsync** 方法。 在示例代码中，*input1* 上的传入消息将使用 *PipeMessage* 函数进行处理，但我们想要改用将在以下步骤中创建的 *FilterMessages* 函数。

1. 将新的 **onDesiredPropertiesUpdate** 方法添加到 **Program** 类。 此方法从模块孪生接收所需属性的更新，然后更新 temperatureThreshold 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. 删除示例 **PipeMessage** 方法，并将其替换为新的 **FilterMessages** 方法。 每当模块从 IoT Edge 中心接收消息，就会调用此方法。 此方法可筛选掉报告的温度低于温度阈值（通过模块孪生设置）的消息。 此方法还将 MessageType 属性添加到消息，其值设置为“警报” 。

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

1. 保存 Program.cs 文件。

1. 在 IoT Edge 解决方案中打开 *deployment.template.json* 文件。 此文件告知 IoT Edge 代理要部署哪些模块，并告知 IoT Edge 中心如何在它们之间路由消息。 此处，要部署的模块是 SimulatedTemperatureSensor 和 CSharpModule。

1. 将 **CSharpModule** 模块孪生添加到部署清单。 在 `modulesContent` 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容：

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![屏幕截图，显示正在添加到部署模板的模块孪生。](./media/tutorial-csharp-module-windows/module-twin.png)

1. 保存 *deployment.template.json* 文件。

## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已创建一个 IoT Edge 解决方案并将代码添加到 CSharpModule，目的是筛选出在其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

### <a name="sign-in-to-docker"></a>登录 Docker

1. 在开发计算机上使用以下命令登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   你可能会收到一条建议使用 `--password-stdin` 的安全警告。 虽然建议将其作为生产方案的最佳做法，但这不在本教程的讨论范围内。 有关详细信息，请参阅 [docker login 参考](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)。

### <a name="build-and-push"></a>生成并推送

1. 在 Visual Studio 解决方案资源管理器中，右键单击要生成的项目的名称。 默认名称为 AzureIotEdgeApp1；由于生成的是 Windows 模块，因此扩展名应是 Windows.Amd64 。

1. 选择“生成并推送 IoT Edge 模块”。

   “生成并推送”命令会启动三项操作：
   * 首先，它在名为 config 的解决方案中创建一个新文件夹，其中包含完整的部署清单。 它是根据部署模板和其他解决方案文件中的信息生成的。 
   * 其次，它会运行 `docker build`，以基于目标体系结构的相应 Dockerfile 生成容器映像。 
   * 最后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

   首次执行此过程可能需要几分钟时间，但下次运行命令时速度将会更快。

## <a name="deploy-modules-to-the-device"></a>将模块部署到设备

使用 Visual Studio Cloud Explorer 和 Azure IoT Edge Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为方案准备了部署清单，即 config 文件夹中的 deployment.windows-amd64.json 文件 。 现在需要做的就是选择一个设备来接收部署。

确保 IoT Edge 设备已启动并运行。

1. 在 Visual Studio Cloud Explorer 中，展开资源以查看 IoT 设备列表。

1. 右键单击要接收部署的 IoT Edge 设备的名称。

1. 选择“创建部署”。

1. 在 Visual Studio 文件资源管理器中，选择解决方案的 config 文件夹中的 deployment.windows-amd64.json 文件 。

1. 刷新 Cloud Explorer，以查看设备下面列出的已部署模块。

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行但未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以使用 IoT Edge Tools 扩展查看抵达 IoT 中心的消息。

1. 在 Visual Studio Cloud Explorer 中，选择 IoT Edge 设备的名称。

1. 在“操作”列表中，选择“开始监视内置事件终结点”。 

1. 查看抵达 IoT 中心的消息。 消息可能需要在一段时间后才会抵达，因为 IoT Edge 设备必须接收其新部署并启动所有模块。 对 CSharpModule 代码所做的更改必须等到计算机温度达到 25 度，然后才能发送消息。 此代码还会将消息类型“警报”添加到指示已达到该温度阈值的任何消息。

   ![显示抵达 IoT 中心的消息的“输出”窗口的屏幕截图。](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>编辑模块孪生

你已使用 CSharpModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio 中打开 *deployment.windows-amd64.json* 文件。 

   不要打开 deployment.template 文件 。 如果在解决方案资源管理器中的 config 文件内未看到部署清单，请在解决方案资源管理器工具栏中选择“显示所有文件”图标。

1. 查找 CSharpModule 孪生，将 temperatureThreshold 参数的值更改为比上次报告的温度高出 5 到 10 度的新温度。

1. 保存 *deployment.windows-amd64.json* 文件。

1. 再次按照部署说明操作，将更新的部署清单应用到设备。

1. 监视传入的设备到云消息。 在达到新的温度阈值之前，消息会停止发送。

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，则可以保留并重复使用在本教程中创建的资源和配置。 还可以继续使用相同的 IoT Edge 设备作为测试设备。

否则，为避免产生费用，你可以删除此处使用的本地配置和 Azure 资源。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。

若要了解如何借助 Azure IoT Edge 部署 Azure 云服务以在边缘位置处理和分析数据，请继续学习后续教程。

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure 流分析](tutorial-deploy-stream-analytics.md)
> [Azure 机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)
