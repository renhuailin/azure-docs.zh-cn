---
title: 如何即插即用 bridge 构建、部署和扩展 IoT |Microsoft Docs
description: 标识 IoT 即插即用 bridge 组件。 了解如何扩展桥，以及如何在 IoT 设备、网关和 IoT Edge 模块上运行它。
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ece9f62e64eb64b1f34af46b42d57ec583f8f214
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675814"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>构建、部署和扩展 IoT 即插即用 bridge

IoT 即插即用桥允许将连接到网关的现有设备连接到 IoT 中心。 使用桥将 IoT 即插即用接口映射到连接的设备。 IoT 即插即用接口定义设备发送的遥测、设备与云之间同步的属性，以及设备所响应的命令。 可以在 Windows 或 Linux 网关上安装和配置开源桥接应用程序。

本文详细说明如何执行以下操作：

- 配置桥。
- 通过创建新的适配器来扩展桥。
- 如何在各种环境中生成并运行桥接。

有关演示如何使用桥的简单示例，请参阅 [如何将在 Linux 或 Windows 上运行的 IoT 即插即用 bridge 示例连接到 Iot 中心](howto-use-iot-pnp-bridge.md)。

本文中的指南和示例假定基本熟悉 [Azure 数字孪生](../digital-twins/overview.md) 和 [IoT 即插即用](overview-iot-plug-and-play.md)。

## <a name="general-prerequisites"></a>一般先决条件

### <a name="supported-os-platforms"></a>支持的操作系统平台

支持以下 OS 平台和版本：

|平台  |支持的版本  |
|---------|---------|
|Windows 10 |     支持所有 Windows Sku。 例如： IoT Enterprise、Server、Desktop、IoT Core。 *对于相机运行状况监视功能，建议使用20H1 或更高版本。所有其他功能在所有 Windows 10 版本上都可用。*  |
|Linux     |已在 Ubuntu 18.04 上测试和支持，尚未测试其他分发版的功能。         |
||

### <a name="hardware"></a>硬件

- 支持上述操作系统 Sku 和版本的任何硬件平台。
- 本机支持串行、USB、蓝牙和相机外设和传感器。 可以扩展 IoT 即插即用桥以支持任何自定义外设或传感器。

### <a name="azure-iot-products-and-tools"></a>Azure IoT 产品和工具

- **Azure Iot 中心** -需要 azure 订阅中的 [azure iot 中心](../iot-hub/index.yml) 将设备连接到。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。 如果没有 IoT 中心，请 [按照以下说明创建一个](../iot-hub/iot-hub-create-using-cli.md)。 基本层 IoT 中心不包含 IoT 即插即用支持。
- **Azure iot 管理器** -若要与 IoT 即插即用设备进行交互，可以使用 Azure IoT 浏览器工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。 将 Azure IoT 资源管理器工具配置为连接到 IoT 中心，并在所克隆的 **iot** - *pnpbridge\docs\schemas* 文件夹中查找模型定义。

## <a name="configure-a-bridge"></a>配置桥

可以通过两种方法配置桥：

- 如果桥在 IoT 设备或网关上以本机方式运行，请使用配置文件。 此方案可以使用 Linux 或 Windows 计算机。
- 如果桥作为 IoT Edge 运行时中的 IoT Edge 模块运行，请使用模块对所需的属性。 此方案假定 IoT Edge 运行时承载于 Linux 环境中。

### <a name="configuration-file"></a>配置文件

当即插即用桥在 IoT 设备或网关上本地运行时，它将使用配置文件执行以下操作：

- 获取 IoT Central 或 IoT 中心连接信息。
- 配置要为其发布 IoT 即插即用接口的设备。

使用以下选项之一将配置文件提供给 bridge：

- 将配置文件的路径作为命令行参数传递给 bridge 可执行文件。
- 使用 *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* 文件夹中的现有 *config.js* 文件。

GitHub 存储库中提供了 [配置文件架构](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) 。

> [!TIP]
> 如果在 VS Code 中编辑桥配置文件，则可以使用此架构文件来验证文件。

### <a name="iot-edge-module-configuration"></a>IoT Edge 模块配置

当桥作为 IoT Edge 运行时的 IoT Edge 模块运行时，将从云中发送配置文件作为 `PnpBridgeConfig` 所需属性的更新。 桥在配置适配器和组件之前，会等待此属性更新。

## <a name="extend-the-bridge"></a>扩展桥

若要扩展桥的功能，可以创作自己的桥接适配器。

桥使用适配器执行以下操作：

- 建立设备和云之间的连接。
- 启用设备和云之间的数据流。
- 从云中启用设备管理。

每个桥接适配器必须：

- 创建数字孪生接口。
- 使用接口可将设备端功能绑定到基于云的功能，如遥测、属性和命令。
- 与设备硬件或固件建立控制和数据通信。

每个桥接适配器都可以根据适配器连接到设备以及与设备交互的方式与特定类型的设备进行交互。 即使与设备的通信使用握手协议，桥适配器也可以通过多种方式来解释设备中的数据。 在这种情况下，桥适配器使用配置文件中的适配器信息来确定适配器用于分析数据的 *接口配置* 。

若要与设备交互，桥接适配器使用由基础操作系统或设备供应商提供的设备和 Api 所支持的通信协议。

为了与云交互，桥接适配器使用 Azure IoT 设备 C SDK 提供的 Api 发送遥测数据，创建数字克隆接口，发送属性更新，并为属性更新和命令创建回调函数。

### <a name="create-a-bridge-adapter"></a>创建桥适配器

桥需要桥接适配器来实现 [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) 接口中定义的 api：

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

在此界面中：

- `PNPBRIDGE_ADAPTER_CREATE` 创建适配器并设置接口管理资源。 适配器还可以依赖于全局适配器参数来创建适配器。 对于单个适配器，将调用此函数一次。
- `PNPBRIDGE_COMPONENT_CREATE` 创建数字克隆客户端接口并绑定回调函数。 适配器启动到设备的通信通道。 适配器可以设置资源来启用遥测流，但在调用之前，不会开始报告遥测 `PNPBRIDGE_COMPONENT_START` 。 为配置文件中的每个接口组件调用一次此函数。
- `PNPBRIDGE_COMPONENT_START` 调用以让桥接适配器开始将遥测数据从设备转发到数字克隆客户端。 为配置文件中的每个接口组件调用一次此函数。
- `PNPBRIDGE_COMPONENT_STOP` 停止遥测流。
- `PNPBRIDGE_COMPONENT_DESTROY` 销毁数字克隆客户端和关联的接口资源。 当桥断开或发生错误时，将为配置文件中的每个接口组件调用一次此函数。
- `PNPBRIDGE_ADAPTER_DESTROY` 清理桥适配器资源。

### <a name="bridge-core-interaction-with-bridge-adapters"></a>桥接与桥接适配器的内核交互

以下列表概述了桥启动时将发生的情况：

1. 桥启动后，桥接程序管理器将查找配置文件中定义的每个接口组件，并 `PNPBRIDGE_ADAPTER_CREATE` 在相应的适配器上调用。 适配器可以使用全局适配器配置参数设置资源来支持各种 *接口配置*。
1. 对于配置文件中的每个设备，桥管理器会通过 `PNPBRIDGE_COMPONENT_CREATE` 在相应的桥接适配器中调用来启动接口创建。
1. 适配器接收接口组件的任何可选适配器配置设置，并使用此信息设置与设备的连接。
1. 适配器将创建数字克隆客户端接口，并将回调函数绑定到属性更新和命令。 建立设备连接应在数字克隆接口创建成功后阻止回调的返回。 活动设备连接独立于桥创建的活动接口客户端。 如果连接失败，适配器将假定设备处于非活动状态。 桥适配器可以选择重新尝试建立此连接。
1. 桥接适配器管理器创建了配置文件中指定的所有接口组件后，它会将所有接口注册到 Azure IoT 中心。 注册是一种阻止的异步调用。 当调用完成时，它会触发桥接程序中的回调，然后即可开始处理云中的属性和命令回调。
1. 然后，桥接适配器管理器对 `PNPBRIDGE_INTERFACE_START` 每个组件调用，桥接适配器开始向数字克隆客户端报告遥测数据。

### <a name="design-guidelines"></a>设计准则

开发新的桥接适配器时，请遵循以下准则：

- 确定支持哪些设备功能，以及使用此适配器的组件的接口定义。
- 确定适配器需要在配置文件中定义的接口和全局参数。
- 确定支持组件属性和命令所需的低级设备通信。
- 确定适配器应如何分析设备中的原始数据，并将其转换为 IoT 即插即用接口定义指定的遥测类型。
- 实现前面所述的桥适配器接口。
- 将新适配器添加到适配器清单并构建桥。

### <a name="enable-a-new-bridge-adapter"></a>启用新的桥接适配器

通过在 [adapter_manifest](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)中添加引用来启用桥中的适配器：

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> 桥接适配器回调按顺序调用。 适配器不应阻止回调，因为这会阻止桥接核心进行进度。

### <a name="sample-camera-adapter"></a>照相机适配器示例

[照相机适配器自述文件](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md)介绍了可以启用的示例照相机适配器。

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>在 IoT 设备或网关上构建并运行桥

| 平台 | 支持 |
| :-----------: | :-----------: |
| Windows |  是 |
| Linux | 是 |

### <a name="prerequisites"></a>必备条件

若要完成本部分，需要在本地计算机上安装以下软件：

- 支持编译 c + + 的开发环境（如 [Visual studio (社区版、专业版或企业版)](https://visualstudio.microsoft.com/downloads/) ），请确保在安装 visual Studio 时包括 NuGet 包管理器组件和 **使用 c + + 进行桌面开发** 工作负荷。
- [CMake](https://cmake.org/download/) -安装 CMake 时，请选择 " **将 CMake 添加到系统路径**" 选项。
- 如果是在 Windows 上构建，还需要下载 [windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。

### <a name="source-code"></a>源代码

将 [IoT 即插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge) 存储库克隆到本地计算机：

```cmd/sh
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

预计上一个命令需要几分钟才能运行。

> [!NOTE]
> 如果在 Windows 上出现故障时遇到问题 `git submodule update` ，请尝试以下命令来解决问题： `git config --system core.longpaths true` 。

### <a name="build-the-bridge-on-windows"></a>在 Windows 上生成桥

打开 **VS 2019 的开发人员命令提示** ，导航到包含所克隆的存储库的文件夹，并运行以下命令：

```cmd
cd pnpbridge\scripts\windows

build.cmd
```

预计上一个命令需要几分钟才能运行。

或者，你可以在 Visual Studio 中打开生成的 *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge .sln* 解决方案文件，以编辑、重新生成和调试代码。

> [!TIP]
> 此项目使用 CMAKE 来生成项目文件。 如果未更新相应的 CMAKE 文件，则在 Visual Studio 中对项目所做的任何修改可能会丢失。

### <a name="build-the-bridge-on-linux"></a>在 Linux 上构建桥

使用 bash shell，导航到包含已克隆的存储库的文件夹，并运行以下命令：

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>配置通用传感器

在 windows 中的 `pnp_bridge_connection_parameters` *iot-plug-and-play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console 文件夹中的* 文件夹下的 *config.js上* 的节点下修改以下参数，或在 windows 中修改 *iot-即插即用/pnpbridge/pnpbridge_linux cmake/pnpbridge/console* 文件夹中的以下参数：

如果正在使用连接字符串连接到 IoT 中心：

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> `symmetric_key`该值必须与连接字符串中的 SAS 密钥匹配。

如果要使用 DPS 连接到 IoT 中心或 IoT Central 应用程序：

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

查看配置文件的其余部分，以查看在此示例中配置了哪些接口组件和全局参数。

### <a name="start-the-bridge-in-windows"></a>在 Windows 中启动该桥

通过在命令提示符下运行桥来启动桥接：

```cmd
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> 若要使用不同位置的配置文件，请在运行桥时传递其路径作为命令行参数。
  
> [!TIP]
> 如果已将内置照相机或 USB 摄像机连接到电脑，则可以启动使用照相机的应用程序，例如内置 **相机** 应用。 当 **照相机** 应用程序正在运行时，桥控制台输出会显示监视统计信息，并且帧速率将通过从数字克隆接口到 IoT 中心进行报告。

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>生成桥并作为 IoT Edge 模块运行

| 平台 | 支持 |
| :-----------: | :-----------: |
| Windows |  否 |
| Linux | 是 |

### <a name="prerequisites"></a>必备条件

若要完成本部分，需要一个免费或标准层 Azure IoT 中心。 若要了解如何创建 IoT 中心，请参阅 [创建 iot 中心](../iot-hub/iot-hub-create-through-portal.md)。

本部分中的步骤假定在 Windows 10 计算机上安装了以下开发环境。 这些工具可让你构建 IoT Edge 模块并将其部署到 IoT Edge 设备：

- 适用于 Linux 的 Windows 子系统 (WSL) 2 运行 Ubuntu 18.04 LTS。 若要了解详细信息，请参阅 [适用于 windows 10 的适用于 Linux 的 Windows 子系统安装指南](https://docs.microsoft.com/windows/wsl/install-win10)。
- 适用于 Windows 的 Docker Desktop 配置为使用 WSL 2。 若要了解详细信息，请参阅 [Docker DESKTOP WSL 2 后端](https://docs.docker.com/docker-for-windows/wsl/)。
- 安装在安装了以下三个扩展的[Windows 环境中 Visual Studio Code](https://code.visualstudio.com/docs/setup/windows) ：

  - [远程开发扩展包](https://aka.ms/vscode-remote/download/extension) -此扩展使你能够在 WSL 2 中生成并运行代码。
  - [Docker for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) -必须在 VS Code **WSL： Ubuntu-18.04** 环境下启用此扩展。
  - [适用于 Visual Studio Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -必须在 VS Code **WSL： Ubuntu-18.04** 环境中启用此扩展。

- 在 WSL 2 环境中运行以下命令，以安装所需的生成工具：

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- 在 WSL 2 环境中安装的 [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) 用于管理 Azure 资源。

  > [!TIP]
  > 如果需要，可以 `az` 在预安装 CLI 的 [Azure Cloud Shell](https://shell.azure.com/) 中运行命令。

### <a name="create-an-iot-edge-device"></a>创建 IoT Edge 设备

此处的命令将创建一个在 Azure 虚拟机中运行的 IoT Edge 设备。 如果无权访问真实设备，在虚拟机中运行 IoT Edge 设备对于测试部署非常有用。

若要在 IoT 中心创建 IoT Edge 设备注册，请在 WSL 2 环境中运行以下命令。 使用 `az login` 命令登录到 Azure 订阅：

```bash
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

若要创建安装了 IoT Edge 运行时的 Azure 虚拟机，请运行以下命令。 用适当的值更新占位符：

```bash
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

现在，你已在虚拟机中运行 IoT Edge 运行时。 你可以使用以下命令来验证 **$edgeAgent** 和 **$edgeHub** 是否正在设备上运行：

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> 此虚拟机正在运行时，将向你计费。 请确保在不使用它时将其关闭，并在完成后将其删除。

### <a name="download-the-source-code"></a>下载源代码

在以下步骤中，将在 WSL 2 环境中生成 Docker 映像。 若要克隆 **iot-即插即** 用存储库，请在适当文件夹中的 WSL 2 bash shell 中运行以下命令：

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

预计上一个命令需要几分钟才能运行。

### <a name="update-the-dockerfile"></a>更新 *Dockerfile*

启动 VS Code，打开命令面板，输入 *远程 WSL：在 WSL 中打开文件夹*，然后打开包含克隆的存储库的 *iot-即插即用* 文件夹。

打开 *pnpbridge\Dockerfile.amd64* 文件。 编辑环境变量定义，如下所示：

```dockerfile
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> 可以使用以下命令检索设备连接字符串： `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

还有适用于其他体系结构的示例 *dockerfile* 。

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>构建 IoT 即插即用 bridge 模块映像

在 VS Code 中，打开命令面板，输入 *Docker 注册表：登录到 DOCKER CLI*，然后选择你的 Azure 订阅和容器注册表。 此命令允许 Docker 连接到容器注册表并上传模块映像。

打开文件 *上的 pnpbridge/module.js* 。 添加 `{your container registry name}.azurecr.io/iotpnpbridge` 为容器映像存储库， `v1` 作为版本。

在 VS Code 中，右键单击 **资源管理器** 视图中的文件 *上的 pnpbridge/module.js* ，选择 "**生成并推送 IoT Edge 模块映像**"，并选择 " **amd64** " 作为平台。

在 VS Code 的 **Docker** 视图中，你可以浏览容器注册表的内容，现在包括 **iotpnpbridge： V1** 模块映像。

### <a name="create-a-container-registry"></a>创建容器注册表

IoT Edge 设备从容器注册表下载其模块映像。 此示例使用 Azure 容器注册表。

在 **网桥-资源** 资源组中创建 Azure 容器注册表。 然后，启用对容器注册表的管理员访问权限，并获取 IoT Edge 设备下载模块映像所需的凭据：

```bash
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>创建部署清单

IoT Edge 部署清单指定要部署到 IoT Edge 设备的模块和配置值。

在 VS Code 中，打开文件 *上的 pnpbridge/deployment.template.js* ：

- `registryCredentials`用前一命令中的值更新。 `address`值类似于 `{your container registry name}.azurecr.io` 。
- 更新 `image` 的值 `ModulePnpBridge` 。 模块图像如下所示： `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` 。
- 将替换为 `PnPBridgeConfig` 以下 JSON，以配置 CO2 检测适配器：

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  保存更改。

在 VS Code 中，右键单击 **资源管理器** 视图中的文件 *上的 pnpbridge/deployment.template.js* ，选择 "**生成 IoT Edge 部署清单**"。 此命令生成部署清单 *上的 pnpbridge/config/deployment.amd64.js* 。

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>将桥部署到 IoT Edge 设备

在 VS Code 中，打开命令面板，输入 " *Azure IoT 中心"：选择 "Iot 中心*"，然后选择订阅和 IoT 中心。

在 VS Code 中，右键单击 "**资源管理器**" 视图中的文件 *上的 pnpbridge/config/deployment.amd64.js* ，选择 "**为单一设备创建部署**"，然后选择 "桥接-**设备**"。

若要查看设备上的模块的状态，请运行以下命令：

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

正在运行的模块的列表现在包括配置为使用 CO2 检测适配器的 **ModulePnpBridge** 模块。

### <a name="tidy-up"></a>整理

若要从 Azure 订阅中删除虚拟机和容器注册表，请运行以下命令：

```bash
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>文件夹结构

*pnpbridge\deps\azure-iot-sdk-c-pnp*：包含适用于 c Sdk 的 Azure IOT 设备 sdk 的 Git 子模块。

*pnpbridge\scripts*：生成脚本。

*pnpbridge\src*： IoT 即插即用 bridge core 的源代码。

*pnpbridge\src\adapters*：各种 IoT 即插即用 bridge 适配器的源代码。

## <a name="next-steps"></a>后续步骤

若要详细了解 IoT 即插即用 bridge，请访问 [iot 即插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存储库。
