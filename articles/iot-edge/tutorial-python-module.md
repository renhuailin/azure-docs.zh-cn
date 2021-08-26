---
title: 教程 - 使用 Azure IoT Edge 的自定义 Python 模块教程
description: 本教程介绍如何使用 Python 代码创建 IoT Edge 模块并将其部署到边缘设备。
services: iot-edge
author: kgremban
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: ca31b4ed8cbc7a6328daf468c3a026665a801f1f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726369"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>教程：使用 Linux 容器开发和部署 Python IoT Edge 模块

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

使用 Visual Studio Code 开发 Python 代码并将其部署到运行 Azure IoT Edge 的设备。

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程将引导你在 IoT Edge 设备（已在快速入门中设置）上创建和部署用于筛选传感器数据的 IoT Edge 模块。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 创建 IoT Edge Python 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

本教程演示如何在 Visual Studio Code 中使用 Python 开发模块，以及如何将其部署到 IoT Edge 设备。

IoT Edge 不支持使用 Windows 容器的 Python 模块。

通过下表了解使用 Linux 容器开发和部署 Python 模块的选项：

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![将 VS Code 用于 Linux AMD64 上的 Python 模块](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![将 VS Code 用于 Linux ARM32 上的 Python 模块](./media/tutorial-c-module/green-check.png) |  |

在开始学习本教程之前，应已完成介绍如何设置用于开发 Linux 容器的开发环境的上一教程：[使用 Linux 容器开发 IoT Edge 模块](tutorial-develop-for-linux.md)。 完成该教程后，已应准备好以下必备组件：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个运行 Azure IoT Edge 的设备。 可以按照快速入门设置 [Linux 设备](quickstart-linux.md)或 [Windows 设备](quickstart.md)。
* 一个容器注册表，例如 [Azure 容器注册表](../container-registry/index.yml)。
* 配置了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

若要开发以 Python 编写的 IoT Edge 模块，请在开发计算机上安装下述额外的必备组件：

* 适用于 Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
* [Python](https://www.python.org/downloads/)。
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) 用于安装 Python 包（通常包含在 Python 安装中）。

>[!Note]
>确保 `bin` 文件夹在平台的路径中。 通常为 `~/.local/`（针对 UNIX 和 macOS）或 `%APPDATA%\Python`（针对 Windows）。

## <a name="create-a-module-project"></a>创建模块项目

以下步骤使用 Visual Studio Code 和 Azure IoT 工具创建 IoT Edge Python 模块。

### <a name="create-a-new-project"></a>创建新项目

创建可以使用你自己的代码进行自定义的 Python 解决方案模板。

1. 在 Visual Studio Code 中，选择“查看” > “命令面板”，以打开 VS Code 命令面板。 

2. 在命令面板中，输入并运行命令“Azure: **Sign in** 命令并按照说明登录到 Azure 帐户。 如果已登录，则可跳过此步骤。

3. 在“命令面板”中，输入并运行 Azure IoT Edge：**New IoT Edge solution** 命令。 按提示操作，提供以下信息，以便创建解决方案：

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“Python 模块”。  |
   | 提供模块名称 | 将模块命名为 **PythonModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是基于你在上一步中提供的名称预先填充的。 将 localhost:5000 替换为 Azure 容器注册表中的“登录服务器”值。 可以在 Azure 门户的容器注册表的“概述”页中检索登录服务器。 <br><br>最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/pythonmodule。 |

   ![提供 Docker 映像存储库](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器存储库的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。

IoT Edge 扩展尝试从 Azure 中拉取容器注册表凭据并将其填充到环境文件中。 检查凭据是否已包含在内。 如果没有，请立即添加：

1. 在 VS Code 资源管理器中，打开 **.env** 文件。
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。
3. 保存 .env 文件。

>[!NOTE]
>本教程使用 Azure 容器注册表的管理员登录凭据，这对于开发和测试方案非常方便。 为生产方案做好准备后，建议使用最低权限身份验证选项（如服务主体）。 有关详细信息，请参阅[管理容器注册表的访问权限](production-checklist.md#manage-access-to-your-container-registry)。

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发 Python 模块。 需要选择面向每个解决方案的体系结构，因为每种体系结构类型的容器的生成和运行方式均不相同。 默认值为 Linux AMD64。

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”  。

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

每个模板都包含示例代码，该代码从 **SimulatedTemperatureSensor** 模块提取模拟传感器数据并将其路由到 IoT 中心。 在此节中，请添加代码，以便扩展用于分析消息的 **PythonModule**，然后再发送它们。

1. 在 VS Code 资源管理器中，打开 **modules** > **PythonModule** > **main.py**。

2. 在 **main.py** 文件顶部导入 **json** 库。

    ```python
    import json
    ```

3. 在全局计数器下添加 **TEMPERATURE_THRESHOLD** 和 **TWIN_CALLBACKS** 变量。 温度阈值设置一个值，若要向 IoT 中心发送数据，测量的计算机温度必须超出该值。

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. 将 **input1_listener** 函数替换为以下代码：

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. 更新 **侦听器** 以同时侦听孪生更新。

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. 保存 main.py 文件。

7. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。

8. 将 **PythonModule** 模块孪生添加到部署清单。 在 **moduleContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容：

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![将模块孪生添加到部署模板](./media/tutorial-python-module/module-twin.png)

9. 保存 deployment.template.json 文件。

## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 PythonModule，该函数会筛选出其中报告的计算机温度处于可接受限制范围内的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 打开 VS Code 集成终端，方法是选择“视图” > “终端” 。

2. 在终端中输入以下命令，以登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会收到一条安全警告，推荐使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

3. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

   首次执行此过程可能需要几分钟时间，但下次运行命令时速度会变快。

## <a name="deploy-modules-to-device"></a>将模块部署到设备

使用 Visual Studio Code 资源管理器和 Azure IoT Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为方案准备了部署清单，即 config 文件夹中的 deployment.amd64.json 文件。 现在需要做的就是选择一个设备来接收部署。

确保 IoT Edge 设备已启动并运行。

1. 在 Visual Studio Code 资源管理器中的“Azure IoT 中心”部分下，展开“设备”可查看 IoT 设备的列表。 

2. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。

3. 选择 config 文件夹中的 deployment.amd64.json 文件，然后单击“选择 Edge 部署清单”。   不要使用 deployment.template.json 文件。

4. 在设备下，展开“模块”可查看已部署的正在运行的模块的列表。 单击“刷新”按钮。 此时应看到新的 **PythonModule** 与 **SimulatedTemperatureSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 一起运行。

    启动模块可能需要数分钟时间。 IoT Edge 运行时需要接收其新的部署清单，从容器运行时下拉模块映像，然后启动每个新模块。

## <a name="view-the-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。

1. 在 Visual Studio Code 资源管理器中右键单击 IoT Edge 设备的名称，选择“开始监视内置事件终结点”。

2. 查看抵达 IoT 中心的消息。 消息到达可能需要一段时间。 IoT Edge 设备必须接收其新部署并启动所有模块。 然后，我们对 PythonModule 代码所做的更改将等到机器温度达到 25 度时才发送消息。 IoT 中心还会将消息类型“警报”添加到达到该温度阈值的任何消息。 

## <a name="edit-the-module-twin"></a>编辑模块孪生

我们已使用部署清单中的 PythonModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio Code 中，展开 IoT Edge 设备下的详细信息以查看正在运行的模块。

2. 右键单击“PythonModule”并选择“编辑模块孪生”   。

3. 在所需属性中找到 **TemperatureThreshold**。 将其值更改为比上次报告的温度高出 5 到 10 度的新温度。

4. 保存模块孪生文件。

5. 右键单击模块孪生编辑窗格中的任意位置，然后选择“更新模块孪生”。

6. 监视传入的设备到云消息。 应会看到，在达到新的温度阈值之前，消息会停止发送。

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。

可以继续学习后续教程，了解如何借助 Azure IoT Edge 部署 Azure 云服务，在边缘位置处理和分析数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)