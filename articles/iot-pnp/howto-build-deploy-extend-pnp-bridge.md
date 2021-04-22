---
title: 如何构建和部署 IoT 即插即用网桥 | Microsoft Docs
description: 识别 IoT 即插即用网桥组件。 了解如何在 IoT 设备和网关上运行它，以及如何将它作为 IoT Edge 模块运行。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c43a49c6b4675e98b557ff73d6f8b6468d74da8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057433"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>构建和部署 IoT 即插即用网桥

通过 [IoT 即插即用网桥](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture)，可将已附加到网关的现有设备连接到 IoT 中心。 可使用网桥将 IoT 即插即用接口映射到已附加的设备。 IoT 即插即用接口定义设备发送的遥测、设备与云之间同步的属性，以及设备所响应的命令。 可以在 Windows 或 Linux 网关上安装和配置开源网桥应用程序。 此外，网桥还可以作为 Azure IoT Edge 运行时模块运行。

本文详细介绍如何执行以下操作：

- 配置网桥。
- 如何在各种环境中构建和运行网桥。

有关演示网桥用法的简单示例，请参阅[如何将在 Linux 或 Windows 上运行的 IoT 即插即用网桥示例连接到 IoT 中心](howto-use-iot-pnp-bridge.md)。

本文中的指南和示例假定你基本熟悉 [Azure 数字孪生](../digital-twins/overview.md)和 [IoT 即插即用](overview-iot-plug-and-play.md)。

## <a name="general-prerequisites"></a>一般先决条件

### <a name="supported-os-platforms"></a>支持的 OS 平台

支持以下 OS 平台和版本：

|平台  |支持的版本  |
|---------|---------|
|Windows 10 |     支持所有 Windows SKU。 例如 IoT 企业版、服务器、桌面、IoT 核心版。 对于相机运行状况监视功能，建议使用 20H1 或更高版本。所有其他功能在所有 Windows 10 版本上均可用。  |
|Linux     |在 Ubuntu 18.04 上已经过测试且受到支持，其他分发版上的功能尚未经过测试。         |
||

### <a name="hardware"></a>硬件

- 支持上述 OS SKU 和版本的任何硬件平台。
- 本机支持串行、USB、蓝牙以及相机外设和传感器。 可将 IoT 即插即用网桥扩展为支持任何自定义外设或传感器。

### <a name="azure-iot-products-and-tools"></a>Azure IoT 产品和工具

- **Azure IoT 中心** - Azure 订阅中需要有一个 [Azure IoT 中心](../iot-hub/index.yml)，你的设备将连接到该中心。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。 如果你没有 IoT 中心，[请遵照此处的说明创建一个](../iot-hub/iot-hub-create-using-cli.md)。 基本层的 IoT 中心不包含 IoT 即插即用支持。
- **Azure IoT 资源管理器** - 若要与 IoT 即插即用设备进行交互，可使用 Azure IoT 资源管理器工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。 配置 Azure IoT 资源管理器工具以连接到 IoT 中心，并在你克隆的 iot-plug-and-play-bridge 中的 pnpbridge\docs\schemas 文件夹中查找模型定义。

## <a name="configure-a-bridge"></a>配置网桥

可通过两种方法配置网桥：

- 如果网桥在 IoT 设备或网关上本机运行，请使用配置文件。 此方案可使用 Linux 或 Windows 计算机。
- 如果网桥作为 IoT Edge 模块在 IoT Edge 运行时中运行，请使用模块孪生所需的属性。 此方案假定 IoT Edge 运行时托管在 Linux 环境中。

### <a name="configuration-file"></a>配置文件

当即插即用网桥在 IoT 设备或网关上本机运行时，它将使用配置文件执行以下操作：

- 获取 IoT Central 或 IoT 中心连接信息。
- 配置要为其发布 IoT 即插即用接口的设备。

使用以下选项之一向网桥提供配置文件：

- 将配置文件的路径作为命令行参数传递给网桥可执行文件。
- 使用 pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console 文件夹中现有的 config.json 文件 。

GitHub 存储库中提供了[配置文件架构](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json)。

> [!TIP]
> 如果在 VS Code 中编辑网桥配置文件，可使用此架构文件来验证文件。

### <a name="iot-edge-module-configuration"></a>IoT Edge 模块配置

当网桥作为 IoT Edge 模块在 IoT Edge 运行时上运行时，配置文件将作为更新从云中发送到 `PnpBridgeConfig` 所需属性。 网桥会先等待此属性更新完毕，然后才配置适配器和组件。

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>在 IoT 设备或网关上构建和运行网桥

| 平台 | 支持 |
| :-----------: | :-----------: |
| Windows |  是 |
| Linux | 是 |

### <a name="prerequisites"></a>必备条件

若要完成本节，需在本地计算机上安装以下软件：

- 支持编译 [Visual Studio（社区版、专业版或企业版）](https://visualstudio.microsoft.com/downloads/)等 C++ 的开发环境 - 安装 Visual Studio 时，请确保包括 NuGet 包管理器组件和“使用 C++ 的桌面开发”工作负载。
- [CMake](https://cmake.org/download/) - 安装 CMake 时，请选择“将 CMake 添加到系统 PATH”选项。
- 如果是在 Windows 上构建，还需要下载 [Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。

### <a name="source-code"></a>源代码

将 [IoT 即插即用网桥](https://github.com/Azure/iot-plug-and-play-bridge)存储库克隆到本地计算机：

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

上一个命令预计需要几分钟才能运行。

> [!NOTE]
> 如果在 Windows 上遇到 `git submodule update` 失败的问题，请尝试使用以下命令来解决问题：`git config --system core.longpaths true`。

### <a name="build-the-bridge-on-windows"></a>在 Windows 上构建网桥

打开 VS 2019 的开发人员命令提示，导航到包含你克隆的存储库的文件夹，然后运行以下命令：

```console
cd pnpbridge\scripts\windows

build.cmd
```

上一个命令预计需要几分钟才能运行。

或者，可在 Visual Studio 中打开生成的 pnpbridge\cmake\pnpbridge_x86\azure_iot_pnp_bridge.sln 解决方案文件，以编辑、重新生成和调试代码。

> [!TIP]
> 此项目使用 CMAKE 来生成项目文件。 如果未更新相应的 CMAKE 文件，则在 Visual Studio 中对项目所做的任何修改都可能会丢失。

### <a name="build-the-bridge-on-linux"></a>在 Linux 上构建网桥

使用 bash shell，导航到包含你克隆的存储库的文件夹，然后运行以下命令：

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>配置通用传感器

在 config.json 文件中的 `pnp_bridge_connection_parameters` 节点下修改以下参数，该文件位于 iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console 文件夹（对于 Windows）或 iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_linux\src/pnpbridge/samples/console 文件夹（对于 Linux）中  ：

如果要使用连接字符串连接到 IoT 中心：

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
> `symmetric_key` 值必须与连接字符串中的 SAS 密钥匹配。

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

查看配置文件的其余部分，看看在此示例中配置了哪些接口组件和全局参数。

### <a name="start-the-bridge-in-windows"></a>在 Windows 中启动网桥

通过在命令提示符处运行网桥来启动网桥：

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> 若要使用不同位置的配置文件，请在运行网桥时将配置文件路径作为命令行参数传递。
  
> [!TIP]
> 如果有内置相机或已将 USB 相机连接到电脑，则可启动使用相机的应用程序，例如内置的“相机”应用。 当“相机”应用正在运行时，网桥控制台输出会显示监视统计信息，并且帧速率将通过数字孪生接口报告到 IoT 中心。

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>构建网桥并将其作为 IoT Edge 模块运行

| 平台 | 支持 |
| :-----------: | :-----------: |
| Windows |  否 |
| Linux | 是 |

### <a name="prerequisites"></a>必备条件

若要完成本节内容，你需要一个免费层或标准层 Azure IoT 中心。 若要了解如何创建 IoT 中心，请参阅[创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

本节中的步骤假定你在 Windows 10 计算机上具有以下开发环境。 借助这些工具，可构建 IoT Edge 模块并将其部署到 IoT Edge 设备：

- 适用于 Linux 的 Windows 子系统 (WSL) 2（运行 Ubuntu 18.04 LTS）。 有关详细信息，请参阅 [Windows 10 的适用于 Linux 的 Windows 子系统安装指南](/windows/wsl/install-win10)。
- 配置为使用 WSL 2 的适用于 Windows 的 Docker Desktop。 若要了解详细信息，请参阅 [Docker Desktop WSL 2 后端](https://docs.docker.com/docker-for-windows/wsl/)。
- [在 Windows 环境中安装了 Visual Studio Code](https://code.visualstudio.com/docs/setup/windows) 并安装了以下三个扩展：

  - [远程开发扩展包](https://aka.ms/vscode-remote/download/extension) - 使用此扩展，可在 WSL 2 中生成并运行代码。
  - [适用于 Visual Studio Code 的 Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) - 必须在 VS Code 的“WSL: Ubuntu-18.04”环境中启用此扩展。
  - [适用于 Visual Studio Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) - 必须在 VS Code 的“WSL: Ubuntu-18.04”环境中启用此扩展。

- 在 WSL 2 环境中运行以下命令，以安装所需的生成工具：

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- 在 WSL 2 环境中安装了 [Azure CLI](/cli/azure/install-azure-cli-apt)，用于管理 Azure 资源。

  > [!TIP]
  > 如果需要，可以在预安装了 CLI 的 [Azure Cloud Shell](https://shell.azure.com/) 中运行 `az` 命令。

### <a name="create-an-iot-edge-device"></a>创建 IoT Edge 设备

此处的命令将创建一个在 Azure 虚拟机中运行的 IoT Edge 设备。 如果你无权访问真实设备，那么在虚拟机中运行 IoT Edge 设备对于测试部署非常有用。

若要在 IoT 中心创建 IoT Edge 设备注册，请在 WSL 2 环境中运行以下命令。 使用 `az login` 命令登录到 Azure 订阅：

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

若要创建安装了 IoT Edge 运行时的 Azure 虚拟机，请运行以下命令。 用合适的值更新占位符：

```azurecli
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

现在，你已在虚拟机中运行 IoT Edge 运行时。 可使用以下命令来验证 $edgeAgent 和 $edgeHub 是否正在设备上运行 ：

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> 运行虚拟机会产生费用。 请确保在不使用它时将其关闭，并在使用完毕后将其删除。

### <a name="download-the-source-code"></a>下载源代码

在以下步骤中，你将在 WSL 2 环境中生成 Docker 映像。 若要克隆 iot-plug-and-play-bridge 存储库，请在适当文件夹中的 WSL 2 bash shell 中运行以下命令：

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

上一个命令预计需要几分钟才能运行。

### <a name="update-the-dockerfile"></a>更新 Dockerfile

启动 VS Code，打开命令面板，输入“Remote WSL: Open folder in WSL”，然后打开包含克隆的存储库的 iot-plug-and-play-bridge 文件夹 。

打开 pnpbridge\Dockerfile.amd64 文件。 按如下所示编辑环境变量定义：

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> 可使用以下命令检索设备连接字符串：`az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

还有适用于其他体系结构的示例 Dockerfile。

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>生成 IoT 即插即用网桥模块映像

在 VS Code 中，打开命令面板，输入“Registries: Log In to Docker CLI”，然后选择你的 Azure 订阅和容器注册表。 此命令允许 Docker 连接到容器注册表并上传模块映像。

打开 pnpbridge/module.json 文件。 添加 `{your container registry name}.azurecr.io/iotpnpbridge` 作为容器映像存储库，添加 `v1` 作为版本。

在 VS Code 中，右键单击“资源管理器”视图中的 pnpbridge/module.json 文件，选择“生成并推送 IoT Edge 模块映像”，然后选择“amd64”作为平台  。

在 VS Code 的 Docker 视图中，可浏览容器注册表的内容，该注册表现在包括 iotpnpbridge:V1-amd64 模块映像 。

### <a name="create-a-container-registry"></a>创建容器注册表

IoT Edge 设备从容器注册表下载其模块映像。 此示例使用 Azure 容器注册表。

在 bridge-edge-resources 资源组中创建 Azure 容器注册表。 然后启用对容器注册表的管理员访问权限，并获取 IoT Edge 设备下载模块映像所需的凭据：

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>创建部署清单

IoT Edge 部署清单指定要部署到 IoT Edge 设备的模块和配置值。

在 VS Code 中打开 pnpbridge/deployment.template.json 文件：

- 用上一命令中的值更新 `registryCredentials`。 `address` 值类似于 `{your container registry name}.azurecr.io`。
- 更新 `ModulePnpBridge` 的 `image` 值。 模块映像如下所示：`{your container registry}.azurecr.io/iotpnpbridge:v1-amd64`。
- 用以下 JSON 替换 `PnPBridgeConfig`，以配置 CO2 检测适配器：

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

在 VS Code 中，右键单击“资源管理器”视图中的 pnpbridge/deployment.template.json 文件，选择“生成 IoT Edge 部署清单” 。 此命令将生成 pnpbridge/config/deployment.amd64.json 部署清单。

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>将网桥部署到 IoT Edge 设备

在 VS Code 中，打开命令面板，输入“Azure IoT Hub: Select IoT Hub”，然后选择订阅和 IoT 中心。

在 VS Code 中，右键单击“资源管理器”视图中的 pnpbridge/config/deployment.amd64.json 文件，选择“为单个设备创建部署”，然后选择“bridge-edge-device”  。

若要查看设备上的模块的状态，请运行以下命令：

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

正在运行的模块列表现在包括配置为使用 CO2 检测适配器的 ModulePnpBridge 模块。

### <a name="tidy-up"></a>整理

若要从 Azure 订阅中删除虚拟机和容器注册表，请运行以下命令：

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>文件夹结构

pnpbridge\deps\azure-iot-sdk-c-pnp：包含适用于 C SDK 的 Azure IoT 设备 SDK 的 Git 子模块。

pnpbridge\scripts：生成脚本。

pnpbridge\src：IoT 即插即用网桥核心的源代码。

pnpbridge\src\adapters：各种 IoT 即插即用网桥适配器的源代码。

## <a name="next-steps"></a>后续步骤

若要详细了解 IoT 即插即用网桥，请访问 [IoT 即插即用网桥](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存储库。