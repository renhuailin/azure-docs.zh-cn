---
title: 在 Visual Studio 中开发和调试模块 - Azure IoT Edge
description: 使用带有 Azure IoT Tools 的 Visual Studio 开发 C 或 C# IoT Edge 模块，并按照部署清单的配置将模块从 Azure IoT 中心推送到 IoT 设备。
services: iot-edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 07/19/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 69ac8ca51fb4bf418af3569e2d294053c1956134
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447351"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>使用 Visual Studio 2019 开发和调试适用于 Azure IoT Edge 的模块

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

本文展示了如何使用 Visual Studio 2019 开发和调试 Azure IoT Edge 模块。

适用于 Visual Studio 扩展的 Azure IoT Edge 工具具备以下优势：

* 在本地开发计算机上创建、编辑、生成、运行和调试 IoT Edge 解决方案和模块。
* 通过 Azure IoT 中心将 IoT Edge 解决方案部署到 IoT Edge 设备。
* 在 C 或 C# 中编写 Azure IoT 模块的代码，同时兼具 Visual Studio 开发的所有优势。
* 使用 UI 管理 IoT Edge 设备和模块。

本文介绍如何使用适用于 Visual Studio 2019 的 Azure IoT Edge 工具开发 IoT Edge 模块。 你还将了解如何将项目部署到 IoT Edge 设备。 目前，Visual Studio 2019 为用 C 和 C# 编写的模块提供支持。 支持的设备体系结构为 Windows X64 和 Linux X64 或 ARM32。 有关支持的操作系统、语言和体系结构的详细信息，请参阅[语言和体系结构支持](module-development.md#language-and-architecture-support)。
  
## <a name="prerequisites"></a>先决条件

本文假设你使用运行 Windows 的计算机作为开发计算机。 在 Windows 计算机上，可以开发 Windows 或 Linux 模块。

* 若要使用 Windows 容器开发模块，请使用运行版本为 1809/内部版本 17763 或更高版本的 Windows 计算机。
* 若要使用 Linux 容器开发模块，请使用满足 [Docker Desktop 要求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 计算机。

在开发计算机上安装 Visual Studio Code。 确保在 Visual Studio 2019 安装中包含“Azure 开发”和“使用 C++ 的桌面开发”工作负荷。   可以[修改 Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) 以添加所需的工作负荷。

Visual Studio 2019 准备就绪后，还需要以下工具和组件：

* 从 Visual Studio 市场中下载并安装 [Azure IoT Edge 工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)，以在 Visual Studio 2019 中创建 IoT Edge 项目。

  > [!TIP]
  > 如果使用的是 Visual Studio 2017，请从 Visual Studio 市场下载并安装[适用于 VS 2017 的 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

* 在开发计算机上下载并安装 [Docker 社区版](https://docs.docker.com/install/)，以生成和运行模块映像。 将 Docker CE 设置为在 Linux 容器模式或 Windows 容器模式下运行，具体取决于待开发模块的类型。

* 设置本地开发环境，通过安装 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)，调试、运行和测试 IoT Edge 解决方案。 请安装 [Python (2.7/3.6+) 和 Pip](https://www.python.org/)，然后在终端中运行以下命令安装 iotedgehubdev 包  。 确保 Azure IoT EdgeHub 开发工具版本高于 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

* 安装 Vcpkg 库管理器，然后安装适用于 Windows 的 azure-iot-sdk-c 包。

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

* 创建用于存储模块映像的 [Azure 容器注册表](../container-registry/index.yml)或 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) 实例。

  > [!TIP]
  > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。

* 若要在设备上测试模块，至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要快速创建用于测试的 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中的步骤进行操作。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要停止 EdgeHub 和 EdgeAgent，然后再在 Visual Studio 中开始开发。

### <a name="check-your-tools-version"></a>检查工具版本

1. 在“扩展”菜单上，选择“管理扩展”   。 依次展开“已安装”>“工具”，可在此处找到“Azure IoT Edge Tools for Visual Studio”和“Cloud Explorer for Visual Studio”    。

1. 请记下所安装的版本。 可将此版本与 Visual Studio Marketplace 中的最新版本（[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)）进行比较

1. 如果你的版本比 Visual Studio Marketplace 上的版本旧，则可在 Visual Studio 中更新工具，如以下部分中所示。

### <a name="update-your-tools"></a>更新工具

1. 在“管理扩展”窗口中，依次展开“更新”>“Visual Studio Marketplace”，选择“Azure IoT Edge Tools”或“Cloud Explorer for Visual Studio”，再选择“更新”      。

1. 下载工具更新后，请关闭 Visual Studio 以触发使用 VSIX 安装程序的工具更新。

1. 在安装程序中，选择“确定”进行启动，然后选择“修改”以更新工具   。

1. 更新完成后，请选择“关闭”并重启 Visual Studio  。

## <a name="create-an-azure-iot-edge-project"></a>创建 Azure IoT Edge 项目

可在 Visual Studio 的 IoT Edge 项目模板中创建可部署到 IoT Edge 设备中的解决方案。 首先创建一个 Azure IoT Edge 解决方案，并在该解决方案中生成第一个模块。 每一个 IoT Edge 解决方案都可包含多个模块。

> [!TIP]
> Visual Studio 创建的 IoT Edge 项目结构与 Visual Studio Code 中的不同。

1. 在 Visual Studio 中，创建新的项目。

1. 在“创建新项目”页面中，搜索“Azure IoT Edge”。 选择与 IoT Edge 设备的平台和体系结构匹配的项目，然后单击“下一步”。

   ![创建新项目](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. 在“配置新项目”中，输入项目名称并指定位置，然后选择“创建”。

1. 在“添加模块”窗口中，选择想开发的模块类型。 还可以选择“现有模块”，可实现将现有 IoT Edge 模块加入部署。 指定模块名称和模块映像存储库。

   Visual Studio 使用“localhost:5000/<模块名\>”自动填充存储库 URL。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost  。 如果使用 Azure 容器注册表，则请用注册表设置中的登录服务器替换“localhost:5000”。 登录服务器类似于“ _\<registry name\>_ .azurecr.io”。最终结果应类似于“\<*registry name*\>.azurecr.io/ _\<module name\>_ ”。

   选择“添加”，将模块添加到项目中。

   ![添加应用程序和模块](./media/how-to-visual-studio-develop-csharp-module/add-module.png)

至此，已在 Visual Studio 解决方案内创建完成 IoT Edge 项目和 IoT Edge 模块。

模块文件夹内有模块代码文件，该文件名为 `program.cs` 或 `main.c`，具体取决于所选语言。 该文件夹还含有名为 `module.json` 的文件，用于描述模块的元数据。 各种 Docker 文件可提供将模块生成为 Windows 或 Linux 容器所需的信息。

项目文件夹内含包括项目中所有模块的列表。 该文件夹目前应只显示一个模块，但可以在此添加更多模块。 有关向项目添加模块的详细信息，请参阅后文的[生成和调试多个模块](#build-and-debug-multiple-modules)部分。

该项目文件夹同时还包含名为 `deployment.template.json` 的文件。 该文件是 IoT Edge 部署清单模板，用于定义将在设备上运行的所有模块，以及这些模块相互通信的方式。 有关部署清单的详细信息，请参阅[了解如何部署模块和建立路由](module-composition.md)。 打开此部署模板，可以看到其中包含“edgeAgent”和“edgeHub”这两个运行时模块，以及在此 Visual Studio 项目中创建的自定义模块。 另外，还有名为“SimulatedTemperatureSensor”的第四个模块。 此默认模块会生成用于测试模块的模拟数据。如果不需要此模块，亦可将其删除。 若要了解模拟温度传感器的工作原理，请查看 [SimulatedTemperatureSensor.csproj 源代码](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)。

## <a name="develop-your-module"></a>开发模块

添加新模块后，其已附带随时可用于生成并部署到设备的默认代码，以便无需操作任何代码即可开始测试。 模块代码在模块文件夹中的文件名为 `Program.cs`（适用于 C#）或 `main.c`（适用于 C）。

生成默认解决方案后，系统会将“SimulatedTemperatureSensor”模块中的模拟数据路由到模块，该模块会接收输入内容，然后将其发送到 IoT 中心。

准备好使用自己的代码自定义模块模板时，请使用 [Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以解决 IoT 解决方案的关键需求，例如安全性、设备管理和可靠性。

## <a name="set-up-the-iotedgehubdev-testing-tool"></a>设置 iotedgehubdev 测试工具

IoT EdgeHub 开发工具提供本地开发和调试体验。 该工具可在没有 IoT Edge 运行时的情况下帮助启动 IoT Edge 模块，以便可在本地创建、开发、测试、运行和调试 IoT Edge 模块和解决方案。 无需将映像推送到容器注册表并将其部署到设备进行测试。

有关详细信息，请参阅 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)。

若要初始化该工具，请输入 IoT 中心的 IoT Edge 设备连接字符串。

1. 在 Azure 门户、Azure CLI 或 Visual Studio Cloud Explorer 中检索 IoT Edge 设备连接字符串。 

1. 在“工具”菜单中选择“Azure IoT Edge Tools” > “设置 IoT Edge 模拟器”。

1. 粘贴连接字符串并单击“确定”。

> [!NOTE]
> 只需在开发计算机上执行一次这些步骤，所得结果将自动应用于所有后续 Azure IoT Edge 解决方案。 如果需要更改为另一个连接字符串，可以再次执行此过程。

## <a name="build-and-debug-a-single-module"></a>生成和调试单个模块

通常，需要先测试和调试每个模块，然后再在具有多个模块的整个解决方案中运行此模块。

1. 在“解决方案资源管理器”中，右键单击“模块文件夹”，然后在菜单中选择“设为启动项目”。

   ![设置启动项目](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按“F5”或单击工具栏中的“运行”按钮运行模块。 首次运行可能需要 10&ndash;20 秒。

   ![运行模块](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模块已成功初始化，你应会看到 .NET Core 控制台应用已启动。

1. 设置用于检查模块的断点。

   * 如果在 C# 环境中开发，请在“Program.cs”的 `PipeMessage()`函数中设置断点。
   * 如果是 C 环境，在“main.c”的 `InputQueue1Callback()` 函数中设置断点。

1. 在“Git Bash shell”或“WSL Bash shell”内运行以下命令即可发送消息，以便测试模块。 （无法从 PowerShell 或命令提示符运行 `curl` 命令。）

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![调试单个模块](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

   应触发断点。 可在 Visual Studio“局部变量”窗口中监视变量  。

   > [!TIP]
   > 还可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具（而非 `curl`）发送消息。

1. 按 Ctrl + F5 或单击“停止”按钮以停止调试  。

## <a name="build-and-debug-multiple-modules"></a>生成和调试多个模块

开发完单个模块之后，需要运行并调试具有多个模块的整个解决方案。

1. 在“解决方案资源管理器”中，右键单击项目文件夹，将第二个模块添加到解决方案中。 在菜单中选择“添加” > “新 IoT Edge 模块”。

   ![将新模块添加到现有的 IoT Edge 项目中](./media/how-to-visual-studio-develop-csharp-module/add-new-module.png)

1. 打开文件 `deployment.template.json`，可以看到新模块已添加到“模块”部分中。 “路由”部分也已添加新路由，以便将消息从新模块发送到 IoT 中心。 若要将数据从模拟温度传感器发送到新模块，请添加另一个路由，具体示例如下所示： 

    ```json
   "sensorTo<NewModuleName>&quot;: &quot;FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/<NewModuleName>/inputs/input1\")"
    ```

1. 右键单击项目文件夹，并在上下文菜单中选择“设为启动项目”。

1. 创建断点，然后按 F5 以同时运行和调试多个模块  。 此时应会看到多个 .NET Core 控制台应用窗口，每个窗口表示一个不同的模块。

   ![调试多个模块](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按 Ctrl+F5 或选择“停止”按钮可停止调试  。

## <a name="build-and-push-images"></a>生成并推送映像

1. 请确保 IoT Edge 项目是启动项目，而不是其中的单个模块。 选择“调试”或“发布”作为配置，以生成模块映像   。

    > [!NOTE]
    > 如果选择“调试”，Visual Studio 使用 `Dockerfile.(amd64|windows-amd64).debug` 生成 Docker 映像。 生成容器映像时，它将在该映像中包含 .NET Core 命令行调试器 VSDBG。 对于生产就绪的 IoT Edge 模块，建议使用“发布”配置，此模块可在没有 VSDBG 的情况下使用 `Dockerfile.(amd64|windows-amd64)`。

1. 如果使用的是专用注册表（如 Azure 容器注册表 (ACR)），请使用以下 Docker 命令登录。  可以从 Azure 门户的注册表的“访问密钥”页中获取用户名和密码。  如果使用的是本地注册表，可[运行本地注册表](https://docs.docker.com/registry/deploying/#run-a-local-registry)。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. 如果使用的是专用注册表（如 Azure 容器注册表），则需要将注册表登录信息添加到文件 `deployment.template.json` 中的运行时设置。 将占位符替换为实际 ACR 管理员用户名、密码和注册表名。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

   >[!NOTE]
   >本文使用 Azure 容器注册表的管理员登录凭据，其十分适用于开发和测试方案。 为生产方案做好准备后，建议使用最低权限身份验证选项（如服务主体）。 有关详细信息，请参阅[管理对容器注册表的访问](production-checklist.md#manage-access-to-your-container-registry)。

1. 在“解决方案资源管理器”中，右键单击项目文件夹，选择“生成并推送 IoT Edge 模块”，为每个模块生成和推送 Docker 映像。

## <a name="deploy-the-solution"></a>部署解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可使用 Cloud Explorer for Visual Studio 部署模块。 为方案和 `deployment.json` 文件准备好部署清单后，接下来只需要选择一个设备来接收部署即可。

1. 通过单击“视图” > “Cloud Explorer”打开 Cloud Explorer。 确保已登录到 Visual Studio 2019。

1. 在 Cloud Explorer 中，展开订阅并找到要部署的 Azure IoT 中心和 Azure IoT Edge 设备  。

1. 右键单击 IoT Edge 设备，为其创建部署。 在 Visual Studio 解决方案的 **config** 文件夹中，导航到为平台配置的部署清单（如 `deployment.arm32v7.json`）。

1. 单击“刷新”按钮以查看与 **SimulatedTemperatureSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 一起运行的新模块。

## <a name="view-generated-data"></a>查看生成的数据

1. 若要监视特定 IoT Edge 设备的 D2C 消息，请在“Cloud Explorer”的 IoT 中心内选择该消息，然后单击“操作”窗口中的“开始监视内置事件终结点”。

1. 若要停止监视数据，请选择“操作”窗口中的“停止监视内置事件终结点”   。

## <a name="next-steps"></a>后续步骤

若要开发用于 IoT Edge 设备的自定义模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
