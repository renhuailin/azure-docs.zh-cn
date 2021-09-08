---
title: 开发和调试 Azure IoT Edge 模块 | Microsoft Docs
description: 使用 Visual Studio Code 开发、生成和调试使用 C#、Python、Node.js、Java 或 C 的 Azure IoT Edge 模块
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 6c204308cff285860c0e887d41eca56ecc220226
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039049"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 开发和调试 Azure IoT Edge 模块

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 本文展示了如何使用 Visual Studio Code 作为主要工具来开发和调试模块。

可以使用两种方式在 Visual Studio Code 中调试用 C#、Node.js 或 Java 编写的模块：可在模块容器中附加一个进程，或在调试模式下启动模块代码。 若要调试用 Python 或 C 编写的模块，只能将其附加到 Linux amd64 容器中的进程。

如果你不熟悉 Visual Studio Code 的调试功能，请阅读有关[Debugging](https://code.visualstudio.com/Docs/editor/debugging)（调试）的信息。

本文说明了如何以多种语言为多种体系结构开发和调试模块。 目前，Visual Studio Code 为用 C#、C、Python、Node.js 和 Java 编写的模块提供支持。 支持的设备体系结构为 X64 和 ARM32。 有关支持的操作系统、语言和体系结构的详细信息，请参阅[语言和体系结构支持](module-development.md#language-and-architecture-support)。

>[!NOTE]
>[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中提供了对 Linux ARM64 设备的开发和调试支持。 有关详细信息，请参阅[在 Visual Studio Code（预览版）中开发和调试 ARM64 IoT Edge 模块](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)。

## <a name="prerequisites"></a>先决条件

可以使用运行 Windows、macOS 或 Linux 的计算机或虚拟机作为开发计算机。 在 Windows 计算机上，可以开发 Windows 或 Linux 模块。 若要开发 Windows 模块，请使用运行版本 1809/内部版本 17763 或更高版本的 Windows 计算机。 若要开发 Linux 模块，请使用符合 [Docker Desktop 要求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 计算机。

首先安装 [Visual Studio Code](https://code.visualstudio.com/)，然后添加以下扩展：

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- 特定于你正在开发的语言的 Visual Studio 扩展：
  - C#，包括 Azure Functions：[C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python:[Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java:[适用于 Visual Studio Code 的 Java 扩展包](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C:[扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

此外还需要安装一些其他特定于语言的工具来开发模块：

- C#，包括 Azure Functions：[.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python:[Python](https://www.python.org/downloads/) 和 [Pip](https://pip.pypa.io/en/stable/installing/#installation)用于安装 Python 包（通常包含在 Python 安装中）。

- Node.js：[Node.js](https://nodejs.org)。 还需要安装 [Yeoman](https://www.npmjs.com/package/yo) 和 [Azure IoT Edge Node.js 模块生成器](https://www.npmjs.com/package/generator-azure-iot-edge-module)。

- Java:[Java SE 开发工具包 10](/azure/developer/java/fundamentals/java-support-on-azure) 和 [Maven](https://maven.apache.org/)。 需要[设置`JAVA_HOME`环境变量](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)，使其指向 JDK 安装项目。

若要生成并部署模块映像，需使用 Docker 生成模块映像，并使用容器注册表来保存模块映像：

- 开发计算机上的 [Docker Community Edition](https://docs.docker.com/install/)。

- [Azure 容器注册表](../container-registry/index.yml)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。

除非使用 C 开发模块，否则还需要基于 Python 的 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)，以便设置本地开发环境以调试、运行和测试 IoT Edge 解决方案。 如果尚未这样做，请安装 [Python (2.7/3.6/3.7) 和 Pip](https://www.python.org/)，然后在终端中运行此命令安装“iotedgehubdev”  。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> 目前，iotedgehubdev 使用与 Python 3.8 不兼容的 docker-py 库。
>
> 如果有多个 Python，包括预安装的 python 2.7（例如，在 Ubuntu 或 macOS 上），请确保使用正确的 `pip` 或 `pip3` 来安装 **iotedgehubdev**

若要在设备上测试模块，至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中的步骤进行操作。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要先停止 EdgeHub 和 EdgeAgent，然后再继续下一步。

## <a name="create-a-new-solution-template"></a>创建新的解决方案模板

以下步骤说明如何使用 Visual Studio Code 和 Azure IoT Tools 以首选开发语言（包括使用 C# 编写的 Azure Functions）创建 IoT Edge 模块。 首先创建一个解决方案，然后生成该解决方案中的第一个模块。 每个解决方案可以包含多个模块。

1. 选择“查看” > “命令面板”   。

1. 在“命令面板”中，输入并运行 Azure IoT Edge：  New IoT Edge Solution 命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

1. 浏览到要在其中创建新解决方案的文件夹，然后选择“选择文件夹”  。

1. 输入解决方案的名称。

1. 选择首选开发语言的模块模板作为解决方案中的第一个模块。

1. 输入模块的名称。 选择容器注册表中唯一的名称。

1. 提供模块的映像存储库的名称。 Visual Studio Code 使用“localhost:5000/<你的模块名称\>”自动填充模块名  。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost  。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：\<registry name\>.azurecr.io。 仅替换字符串的 localhost:5000 部分，使最终结果类似于 \<*registry name*\>.azurecr.io/\<your module name\> 。

   ![提供 Docker 映像存储库](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。

该解决方案中有四个项：

- 一个 .vscode 文件夹，包含调试配置  。

- 一个 modules 文件夹，包含每个模块的子文件夹  。  在每个模块的文件夹中有一个 **module.json** 文件，用于控制模块的构建和部署方式。  需修改此文件，将模块部署容器注册表从 localhost 更改为远程注册表。 现在，只有一个模块。  但是可以在命令面板中使用以下命令添加更多模块：“Azure IoT Edge:  Add IoT Edge Module”。

- 一个 .env 文件，列出环境变量  。 如果 Azure 容器注册表是注册表，则其中将包含 Azure 容器注册表用户名和密码。

  在生产场景中，我们建议使用服务主体来提供对容器注册表（而不是 .env 文件）的访问。 有关详细信息，请参阅[管理对容器注册表的访问](production-checklist.md#manage-access-to-your-container-registry)。

  > [!NOTE]
  > 仅当为模块提供了映像存储库时，才会创建环境文件。 如果接受 localhost 默认值在本地进行测试和调试，则不需要声明环境变量。

- 一个 deployment.template.json 文件，列出新模块以及模拟可用于测试的数据的示例 SimulatedTemperatureSensor 模块   。 有关部署清单如何工作的详细信息，请参阅[了解如何使用部署清单部署模块和建立路由](module-composition.md)。

若要了解模拟温度模块的工作原理，请查看 [SimulatedTemperatureSensor.csproj 源代码](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)。

### <a name="set-iot-edge-runtime-version"></a>设置 IoT Edge 运行时版本

在创建部署资产时，IoT Edge 扩展默认为 IoT Edge 运行时的最新稳定版本。 目前，最新稳定版本是 1.2 版。 如果要为运行 1.1 长期支持版本或 1.0 早期版本的设备开发模块，请更新 Visual Studio Code 中的 IoT Edge 运行时版本以进行匹配。

1. 选择“查看” > “命令面板”   。

1. 在命令面板中，输入并运行“Azure IoT Edge: Set default IoT Edge runtime version”命令。

1. 从列表中选择 IoT Edge 设备正在运行的运行时版本。

选择新的运行时版本后，部署清单会动态更新以反映对运行时模块映像所做的更改。

## <a name="add-additional-modules"></a>添加其他模块

若要向解决方案添加其他模块，请从命令面板运行命令“Azure IoT Edge：  添加 IoT Edge 模块”。 也可以右键单击 Visual Studio Code 资源管理器视图中的“模块”文件夹或 `deployment.template.json` 文件，然后选择“添加 IoT Edge 模块”   。

## <a name="develop-your-module"></a>开发模块

解决方案附带的默认 C# 模块代码位于以下位置：

- Azure Function (C#)：modules > &lt;你的模块名称&gt; > &lt;你的模块名称&gt;.cs  
- C#：modules > &lt;你的模块名称&gt; > Program.cs 
- Python：modules > &lt;你的模块名称&gt; > main.py 
- Node.js：modules > &lt;你的模块名称&gt; > app.js 
- Java：modules > &lt;你的模块名称&gt; > src > main > java > com > edgemodulemodules > App.java 
- C：modules > &lt;你的模块名称&gt; > main.c 

设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试而无需触及任何代码。 生成该模块仅为简单从源（在此示例中，为模拟数据的 SimulatedTemperatureSensor 模块）获取输入并通过管道将其传送到 IoT 中心。

当你准备使用自己的代码自定义模板时，请使用 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>调试没有容器的模块 (C#、Node.js、Java)

如果使用 C#、Node.js 或 Java 进行开发，则模块需要在默认模块代码中使用“ModuleClient”对象，以便它可以启动、运行和路由消息  。 还将使用默认输入通道“input1”在模块接收消息时执行操作  。

### <a name="set-up-iot-edge-simulator"></a>设置 IoT Edge 模拟器

IoT Edge 模块需要 IoT Edge 环境才能运行和调试。 可以在开发计算机上使用 IoT Edge 模拟器，而不是运行完整的 IoT Edge 安全守护程序和运行时。 可以模拟设备以调试具有多个模块的解决方案，也可以模拟单模块应用程序。

选项 1：模拟 IoT Edge 解决方案：

1. 在左侧的“资源管理器”选项卡中，展开“Azure IoT 中心”部分 。 右键单击 IoT Edge 设备 ID，然后选择“设置 IoT Edge 模拟器”以使用设备连接字符串启动模拟器。
1. 通过读取集成终端中的进度详细信息，可以看到 IoT Edge 模拟器已成功设置。

选项 2：模拟单个 IoT Edge 模块：

1. 在 Visual Studio Code 命令面板中，运行“Azure IoT Edge: 启动单模块的 IoT Edge 中心模拟器”命令。
1. 提供要使用模块进行测试的任何输入的名称。 如果使用的是默认示例代码，则使用值“input1”。
1. 该命令触发“iotedgehubdev”CLI，然后启动 IoT Edge 模拟器并测试实用程序模块容器  。 如果模拟器已成功以单模块模式启动，则可以在集成终端中看到下面的输出。 还可以看到 `curl` 命令以帮助发送消息。 稍后将使用它。

   ![为单个模块应用设置 IoT Edge 模拟器](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   可以使用 Visual Studio Code 中的 Docker 资源管理视图来查看模块的运行状态。

   ![模拟器模块状态](media/how-to-develop-csharp-module/simulator-status.png)

   edgeHubDev  容器是本地 IoT Edge 模拟器的核心。 它可以在没有 IoT Edge 安全守护程序的情况下在开发计算机上运行，并为本机模块应用或模块容器提供环境设置。 “input”容器公开 REST API 以帮助将消息桥接到模块上的目标输入通道  。

### <a name="debug-module-in-launch-mode"></a>在启动模式下调试模块

模拟器成功启动后，可以调试模块代码。

1. 根据开发语言的要求准备环境以进行调试，在模块中设置断点，并选择要使用的调试配置：
   - **C#**
     - 在 Visual Studio Code 集成终端中，将目录更改为“&lt;你的模块名称&gt;”文件夹，然后运行以下命令以构建 .NET Core 应用程序。

       ```cmd
       dotnet build
       ```

     - 打开 `Program.cs` 文件并添加断点。

     - 通过从左侧菜单中选择调试图标或按 `Ctrl+Shift+D`，导航到 Visual Studio Code 的“调试”视图。 从下拉列表中选择调试配置“&lt;你的模块名称&gt; 本地调试(.NET Core)”  。

        > [!NOTE]
        > 如果 .NET Core `TargetFramework` 与 `launch.json` 中的程序路径不一致，则需要手动更新 `launch.json` 中的程序路径以匹配 .csproj 文件中的 `TargetFramework`，以便 Visual Studio Code 可以成功启动此程序。

   - **Node.js**
     - 在 Visual Studio Code 集成终端中，将目录更改为“&lt;你的模块名称&gt;”文件夹，然后运行以下命令以安装节点包

       ```cmd
       npm install
       ```

     - 打开 `app.js` 文件并添加断点。

     - 通过从左侧菜单中选择调试图标或按 `Ctrl+Shift+D`，导航到 Visual Studio Code 的“调试”视图。 从下拉列表中选择调试配置“&lt;你的模块名称&gt; 本地调试(Node.js)”  。
   - **Java**
     - 打开 `App.java` 文件并添加断点。

     - 通过从左侧菜单中选择调试图标或按 `Ctrl+Shift+D`，导航到 Visual Studio Code 的“调试”视图。 从下拉列表中选择调试配置“&lt;你的模块名称&gt; 本地调试(Java)”  。

1. 单击“开始调试”或按“F5”开始调试会话   。

1. 在 Visual Studio Code 集成终端中，运行以下命令向模块发送“Hello World”消息  。 这是在前面的步骤中设置 IoT Edge 模拟器时所显示的命令。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > 如果使用的是 Windows，请确保 Visual Studio Code 集成终端的 shell 为 Git Bash  或 WSL Bash  。 无法从 PowerShell 或命令提示符运行 `curl` 命令。
   > [!TIP]
   > 还可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具（而不是 `curl`）来发送消息。

1. 在 Visual Studio Code 调试视图中，将在左侧面板中看到变量。

1. 若要停止调试会话，请选择“停止”按钮或按“Shift + F5”，然后在命令面板中运行“Azure IoT Edge：   停止 IoT Edge 模拟器”以停止模拟器并清除。

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>使用 IoT Edge 模拟器在附加模式下进行调试 (C#、Node.js、Java、Azure Functions)

默认解决方案包含两个模块，一个是模拟的温度传感器模块，另一个是管道模块。 模拟的温度传感器向管道模块发送消息，然后将消息通过管道传送到 IoT 中心。 在创建的模块文件夹中，有适用于不同容器类型的多个 Docker 文件。 使用以扩展名“.debug”结尾的任何文件来生成用于测试的模块  。

目前，仅支持以附加模式进行调试，如下所示：

- C# 模块（包括 Azure Functions 的模块）支持在 Linux amd64 容器进行调试
- Node.js 模块支持在 Linux amd64、arm32v7 容器以及 Windows amd64 容器中进行调试
- Java 模块支持在 Linux amd64 和 arm32v7 容器中进行调试

> [!TIP]
> 可以通过单击 Visual Studio 代码状态栏中的项目来切换 IoT Edge 解决方案的默认平台的选项。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>为 IoT Edge 解决方案设置 IoT Edge 模拟器

在开发计算机中，可以启动 IoT Edge 模拟器（而不是安装 IoT Edge 安全守护程序）以运行 IoT Edge 解决方案。

1. 在左侧的“资源管理器”选项卡中，展开“Azure IoT 中心”部分 。 右键单击 IoT Edge 设备 ID，然后选择“设置 IoT Edge 模拟器”以使用设备连接字符串启动模拟器。

1. 通过读取集成终端中的进度详细信息，可以看到 IoT Edge 模拟器已成功设置。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>生成和运行用于调试的容器并在附加模式下进行调试

1. 打开模块文件 (`Program.cs`、`app.js``App.java` 或 `<your module name>.cs`) 并添加断点。

1. 在 Visual Studio Code 资源管理器视图中，右键单击解决方案的 `deployment.debug.template.json` 文件，然后选择“在模拟器中构建并运行 IoT Edge 解决方案”  。 可以在同一窗口中监视所有模块容器日志。 还可以导航到 Docker 视图以监视容器状态。

   ![监视变量](media/how-to-vs-code-develop-module/view-log.png)

1. 导航至 Visual Studio Code 调试视图，并选择模块的调试配置文件。 调试选项名称应类似于“&lt;你的模块名称&gt; 远程调试” 

1. 选择“开始调试”或按“F5”   。 选择要附加到的进程。

1. 在 Visual Studio Code 调试视图中，将在左侧面板中看到变量。

1. 若要停止调试会话，请先选择停止按钮或按“Shift + F5”，然后从命令面板中选择“Azure IoT Edge：   停止 IoT Edge 模拟器”。

> [!NOTE]
> 上面的示例展示了如何调试容器上的 IoT Edge 模块。 它为模块的容器 `createOptions` 设置添加了公开的端口。 完成模块的调试后，建议为可用于生产的 IoT Edge 模块删除那些公开的端口。
>
> 对于使用 C# 编写的模块（包括 Azure Functions），此示例基于 `Dockerfile.amd64.debug` 的调试版本，在生成容器映像时这将在容器映像中包括 .NET Core 命令行调试器 (VSDBG)。 对于生产就绪的 IoT Edge 模块，建议在调试完 C# 模块后在不使用 VSDBG 的情况下直接使用 Dockerfile。

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>使用 IoT Edge 运行时调试模块

每个模块文件夹中都有多个适用于不同容器类型的 Docker 文件。 使用以扩展名“.debug”结尾的任何文件来生成用于测试的模块  。

使用此方法调试模块时，模块将在 IoT Edge 运行时的顶层运行。 IoT Edge 设备和 Visual Studio Code 可位于同一台计算机上；更常见的情况是，Visual Studio Code 位于开发计算机上，IoT Edge 运行时和模块在另一台物理计算机上运行。 若要从 Visual Studio Code 进行调试，必须：

- 设置 IoT Edge 设备，使用 **.debug** Dockerfile 生成 IoT Edge 模块，然后将其部署到 IoT Edge 设备。
- 公开模块的 IP 和端口，以便可以附加调试程序。
- 更新 `launch.json`，使 Visual Studio Code 能够附加到远程计算机上的容器中的进程。 此文件位于工作区中的 `.vscode` 文件夹内，每当添加用于支持调试的新模块时，此文件都会更新。

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>生成模块并将其部署到 IoT Edge 设备

1. 在 Visual Studio 代码中，打开 `deployment.debug.template.json` 文件，其中包含模块映像的调试版本，并设置了正确的 `createOptions` 值。

1. 如果正在使用 Python 开发模块，请在继续之前按照以下步骤操作：
   - 打开文件 `main.py` 并在导入部分后添加此代码：

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - 将以下单行代码添加到要调试的回叫中：

      ```python
      ptvsd.break_into_debugger()
      ```

     例如，如果要调试 `receive_message_listener` 函数，则应插入该行代码，如下所示：

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. 在 Visual Studio Code 命令面板中：
   1. 运行“Azure IoT Edge:  Build and Push IoT Edge solution”命令。

   1. 选择解决方案的 `deployment.debug.template.json` 文件。

1. 在 Visual Studio Code 资源管理器视图的“Azure IoT 中心设备”部分中  ：
   1. 右键单击 IoT Edge 设备 ID，然后选择“为单个设备创建部署”。 

      > [!TIP]
      > 若要确认已选择的设备为 IoT Edge 设备，请选择它以展开模块列表并验证是否存在“$ edgeHub”和“$ edgeAgent”   。 每个 IoT Edge 设备都包含这两个模块。

   1. 导航到解决方案的“config”文件夹，选择 `deployment.debug.amd64.json` 文件，然后选择“选择 Edge 部署清单”   。

将在集成终端中看到已成功创建部署且具有一个部署 ID。

可以通过在终端中运行 `docker ps` 命令来检查容器状态。 如果 Visual Studio Code 和 IoT Edge 运行时在同一台计算机上运行，则还可以在 Visual Studio Code Docker 视图中检查状态。

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>为调试程序公开模块的 IP 和端口

如果模块与 Visual Studio Code 在同一台计算机上运行，你使用 localhost 附加到容器，并且 **.debug** Dockerfile、模块的容器 `createOptions` 设置和 `launch.json` 文件中已有正确的端口设置，则可以跳过本部分。 如果模块和 Visual Studio Code 在不同的计算机上运行，请根据开发语言执行以下步骤。

- **C#，包括 Azure Functions**

  [在开发计算机和 IoT Edge 设备上配置 SSH 通道](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes)，然后编辑要附加的 `launch.json` 文件。

- **Node.js**

  - 确保计算机上要调试的模块正在运行且可供调试程序附加，并且可从外部访问端口 9229。 可以通过在调试程序计算机上打开 `http://<target-machine-IP>:9229/json` 对此进行验证。 此 URL 应显示有关要调试的 Node.js 模块的信息。
  
  - 在开发计算机上打开 Visual Studio Code，然后编辑 `launch.json` 文件，以便 ***&lt;模块名称&gt;* Remote Debug (Node.js)** 配置文件（如果模块作为 Windows 容器运行，则为 **_&lt;模块名称&gt;_ Remote Debug (Node.js in Windows Container)** 配置文件）的地址值是所要调试的计算机的 IP。

- **Java**

  - 运行 `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`，与要调试的计算机建立 SSH 隧道。
  
  - 在开发计算机上打开 Visual Studio Code，然后编辑 `launch.json` 中的 ***&lt;模块名称&gt;* Remote Debug (Java)** 配置文件，以便可以附加到目标计算机。 若要详细了解如何编辑 `launch.json` 以及使用 Visual Studio Code 调试 Java，请参阅有关[配置调试程序](https://code.visualstudio.com/docs/java/java-debugging#_configuration)的部分。

- **Python**

  - 确保要调试的计算机上的端口 5678 已打开且可访问。

  - 在前面已插入到 `main.py` 的代码 `ptvsd.enable_attach(('0.0.0.0', 5678))` 中，将 **0.0.0.0** 更改为要调试的计算机的 IP 地址。 再次生成、推送并部署 IoT Edge 模块。

  - 在开发计算机上打开 Visual Studio Code，然后编辑 `launch.json`，使 ***&lt;模块名称&gt;* Remote Debug (Python)** 配置文件的 `host` 值使用目标计算机的 IP 地址而不是 `localhost`。

### <a name="debug-your-module"></a>调试模块

1. 在 Visual Studio Code 调试视图中，选择模块的调试配置文件。 调试选项名称应类似于“&lt;你的模块名称&gt; 远程调试” 

1. 打开开发语言的模块文件并添加断点：

   - **Azure 函数 (C#)** ：将断点添加到文件 `<your module name>.cs`。
   - **C#** ：将断点添加到文件 `Program.cs`。
   - **Node.js**：将断点添加到文件 `app.js`。
   - **Java**：将断点添加到文件 `App.java`。
   - **Python**：在添加了 `ptvsd.break_into_debugger()` 行的回调方法中，将断点添加到文件 `main.py`。
   - **C**：将断点添加到文件 `main.c`。

1. 选择“开始调试”或选择 F5   。 选择要附加到的进程。

1. 在 Visual Studio Code 调试视图中，将在左侧面板中看到变量。

> [!NOTE]
> 上面的示例展示了如何调试容器上的 IoT Edge 模块。 它为模块的容器 `createOptions` 设置添加了公开的端口。 完成模块的调试后，建议为可用于生产的 IoT Edge 模块删除那些公开的端口。

## <a name="build-and-debug-a-module-remotely"></a>远程生成和调试模块

凭借近期为支持 SSH 连接而对 Docker 和 Moby 引擎所做的更改，以及 Azure IoT 工具（可以将环境设置注入 Visual Studio Code 命令面板和 Azure IoT Edge 终端）中的新设置，现在可以在远程设备上生成和调试模块。

请参阅此 [IoT 开发人员博客文章](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)获取详细信息和分步说明。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)。

若要开发用于 IoT Edge 设备的模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。