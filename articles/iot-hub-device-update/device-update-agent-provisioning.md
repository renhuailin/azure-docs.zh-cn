---
title: 预配 Device Update for Azure IoT Hub 代理 | Microsoft Docs
description: 预配 Device Update for Azure IoT Hub 代理
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e65dc85dff7692f82118871ab93c628efe6cecf8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661671"
---
# <a name="device-update-agent-provisioning"></a>Device Update 代理预配

Device Update 模块代理可以与其他系统进程，以及连接到 IoT 中心作为同一逻辑设备的一部分的 [IoT Edge 模块](../iot-edge/iot-edge-modules.md)一起运行。 本部分介绍如何将 Device Update 代理预配为模块标识。 


## <a name="module-identity-vs-device-identity"></a>模块标识与设备标识

在 IoT 中心的每个设备标识下，最多可以创建 50 个模块标识。 每个模块标识隐式生成模块孪生。 在设备端，可以使用 IoT 中心设备 SDK 创建模块，其中每个模块与 IoT 中心建立独立连接。 模块标识和模块孪生提供的功能与设备标识和设备孪生相似，但前者的粒度更细。 [详细了解 IoT 中心中的模块标识](../iot-hub/iot-hub-devguide-module-twins.md)

如果要从设备级别代理迁移到将代理添加为设备上的模块标识，请删除通过设备孪生通信的较旧代理。 将 Device Update 代理预配为模块标识时，设备和 Device Update 服务之间的所有通信都通过模块孪生进行。因此，请记住：在创建[组](device-update-groups.md)时标记设备的模块孪生；所有[通信](device-update-plug-and-play.md)都必须通过模块孪生进行。

## <a name="support-for-device-update"></a>对 Device Update 的支持

Device Update 当前支持以下 IoT 设备类型：

* Linux 设备（IoT Edge和非 IoT Edge 设备）：
    * 映像 A/B 更新：
        - Yocto - ARM64（引用映像），可通过开源扩展，根据需要为其他体系结构[生成自己的映像](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent)。
        - Ubuntu 18.04 模拟器
       
    * 以下平台/体系结构支持的包代理生成：
        - Ubuntu 服务器 18.04 x64 包代理 
        - Debian 9 
        
* 受约束的设备：
    * AzureRTOS Device Update 代理示例：[Azure 实时操作系统的 Device Update for Azure IoT Hub 教程](device-update-azure-real-time-operating-system.md)

* 断开连接的设备： 
    * [了解对离线设备更新的支持](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>先决条件  

如果要为[基于包的更新](./understand-device-update.md#support-for-a-wide-range-of-update-artifacts)设置 IoT 设备/IoT Edge 设备，请通过执行以下步骤将 packages.microsoft.com 添加到计算机的存储库：

1. 登录到要安装 Device Update 代理的虚拟机或 IoT 设备。

1. 打开终端窗口。

1. 安装与设备操作系统匹配的存储库配置。

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. 将生成的列表复制到 sources.list.d 目录。

    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. 安装 Microsoft GPG 公钥。

    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>如何将 Device Update 代理预配为模块标识

本部分介绍如何将 Device Update 代理预配为 
* 启用了 IoT Edge 的设备，或 
* 非 Edge IoT 设备，或
* 其他 IoT 设备上的模块标识。 
 
根据所管理的 IoT 设备的类型，按照以下全部或任一部分来添加 Device Update 代理。 

### <a name="on-iot-edge-enabled-devices"></a>在启用了 IoT Edge 的设备上

按照这些说明在[启用了 IoT Edge 的设备](../iot-edge/index.yml)上预配 Device Update 代理。

1. 遵循有关[安装和预配 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge.md?preserve-view=true&view=iotedge-2020-11)的说明。

1. 安装 Device Update 映像更新代理。

    我们在[项目](https://github.com/Azure/iot-hub-device-update/releases)存储库中提供示例图像。 swUpdate 文件是可刷写到 Raspberry Pi B3+ 板上的基础映像。 .gz 文件是要通过 Device Update for IoT Hub 导入的更新。 有关示例，请参阅[如何将映像刷写到 Azure IoT 中心设备](./device-update-raspberry-pi.md#flash-sd-card-with-image)。  

1. 安装 Device Update 包更新代理。

    - 如需从 packages.miscrosoft.com 获取最新代理版本：更新设备上的包列表，然后使用以下方法安装 Device Update 代理包及其依赖项：   

        ```shell
        sudo apt-get update
        ```
    
        ```shell
        sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
        ```
    
    - 对于任何 'rc'（即从[项目](https://github.com/Azure/iot-hub-device-update/releases)发布候选代理版本）：将 .dep 文件下载到要安装 Device Update 代理的计算机上，然后：
   
        ```shell
        sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
        ```
    
1. 你现在已准备好在 IoT Edge 设备上启动 Device Update 代理。 

### <a name="on-non-edge-iot-linux-devices"></a>在非 Edge IoT Linux 设备上

按照这些说明在 IoT Linux 设备上预配 Device Update 代理。

1. 安装 IoT 标识服务，并将最新版本添加到 IoT 设备。 
    1. 登录到计算机或 IoT 设备。
    1. 打开终端窗口。
    1.  使用此命令在 IoT 设备上安装最新的 [IoT 标识服务](https://github.com/Azure/iot-identity-service/blob/main/docs-dev/packaging.md#installing-and-configuring-the-package)：
    > [!Note]
    > IoT 标识服务当前使用对称密钥向 IoT 中心注册模块标识。

    ```shell
    sudo apt-get install aziot-identity-service
    ```
        
1. 预配 IoT 标识服务以获取 IoT 设备信息。

    创建配置模板的自定义副本，以便可以添加预配信息。 在终端中输入以下命令：
      
    ```shell
    sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
    ```
   
1. 接下来，编辑配置文件以包含要充当此设备或计算机的预配程序的设备的连接字符串。 在终端中，输入以下命令。

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. 应看到类似于以下示例的消息：

    :::image type="content" source="media/understand-device-update/config.png" alt-text="IoT 标识服务配置文件关系图。" lightbox="media/understand-device-update/config.png":::

    1. 在同一 Nano 窗口中，找到包含“使用连接字符串手动预配”的块。
    1. 在窗口中，删除“预配”前面的“#”符号
    1. 在窗口中，删除“源”前面的“#”符号 
    1. 在窗口中，删除“connection_string”前面的“#”符号
    1. 在窗口中，删除“connection_string”右侧的引号内的字符串，然后将连接字符串添加到其中 
    1. 使用“Ctrl+X”保存对文件所做的更改，然后使用“Y”并点击“enter”键保存更改。 
    
1. 现在，使用以下命令应用并重新启动 IoT 标识服务。 现在应看到“Done!” 输出，表示已成功配置 IoT 标识服务。

    > [!Note]
    > IoT 标识服务当前使用对称密钥向 IoT 中心注册模块标识。
    
    ```shell
    sudo aziotctl config apply
    ```
    
1. 最终安装 Device Update 代理。 我们在[项目](https://github.com/Azure/iot-hub-device-update/releases)中提供示例映像，swUpdate 文件是可刷写到 Raspberry Pi B3+ 板上的基础映像，.gz 文件是要通过 Device Update for IoT Hub 导入的更新。 请参阅有关[如何将映像刷写到 IoT 中心设备](./device-update-raspberry-pi.md#flash-sd-card-with-image)的这一示例。

1. 你现在已准备好在 IoT 设备上启动 Device Update 代理。 

### <a name="other-iot-devices"></a>其他 IoT 设备

无需 IoT 标识服务即可在受约束设备上配置 Device Update 代理以进行测试。 按照以下步骤使用连接字符串预配 Device Update 代理（从模块或设备）。

1. 我们在[项目](https://github.com/Azure/iot-hub-device-update/releases)存储库中提供示例图像。 swUpdate 文件是可刷写到 Raspberry Pi B3+ 板上的基础映像。 .gz 文件是要通过 Device Update for IoT Hub 导入的更新。 有关示例，请参阅[如何将映像刷写到 Azure IoT 中心设备](./device-update-raspberry-pi.md#flash-sd-card-with-image)。

1. 登录到计算机或 IoT Edge 设备/IoT 设备。
    
1. 打开终端窗口。

1. 将连接字符串添加到[设备更新配置文件](device-update-configuration-file.md)：

    1. 在终端窗口中输入以下内容：
   
        - [对于包更新](device-update-ubuntu-agent.md)，请使用：sudo nano /etc/adu/adu-conf.txt
        - [对于映像更新](device-update-raspberry-pi.md)，请使用：sudo nano /adu/adu-conf.txt
       
    1. 应会看到一个窗口打开，其中包含一些文本。 首次在 IoT 设备上预配 Device Update 代理时，删除“connection_String=”后的整个字符串。 它只是占位符文本。
    
    1. 在终端中，将 \<your-connection-string\> 替换为 Device Update 代理实例的设备的连接字符串。 选择“输入”并“保存”。 应如此示例所示：
    
        ```text
        connection_string=<ADD CONNECTION STRING HERE>
        ```   
        
    > [!Important]
    > 请勿在连接字符串周围添加引号。
    
1. 你现在已准备好在 IoT 设备上启动 Device Update 代理。 


## <a name="how-to-start-the-device-update-agent"></a>如何启动 Device Update 代理

本部分介绍如何启动 Device Update 代理并验证它是否作为模块标识在 IoT 设备上成功运行。

1. 登录到安装了 Device Update 代理的计算机或设备。

1. 打开终端窗口，然后输入以下命令。

    ```shell
    sudo systemctl restart adu-agent
    ```
    
1. 可以使用以下命令检查代理的状态。 如果遇到任何问题，请参阅此[故障排除指南](troubleshoot-device-update.md)。
    
    ```shell
    sudo systemctl status adu-agent
    ```
    
    应会看到状态“正常”。

1. 在 IoT 中心门户中，转到 IoT 设备或 IoT Edge 设备以查找使用 Device Update 代理配置的设备。 你将看到作为模块运行的 Device Update 代理。 例如：

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="设备更新模块名称关系图。" lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>如何生成和运行设备更新代理

还可以生成和修改自己的客户 Device Update 代理。

按照说明通过源[生成](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) Device Update 代理。

成功生成代理后，请[运行](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)该代理。

现在，请进行必要的更改，将代理合并到映像中。  查看如何[修改](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) Device Update 代理以获取指导。


## <a name="troubleshooting-guide"></a>故障排除指南

如果遇到问题，请查看 Device Update for IoT Hub [故障排除指南](troubleshoot-device-update.md)，以帮助解决所有可能的问题并收集要提供给 Microsoft 的必要信息。


## <a name="next-steps"></a>后续步骤

可以使用以下预生成的映像和二进制文件轻松演示 Device Update for IoT Hub：

- [映像更新：Raspberry Pi 3 B+ Reference Yocto Image 入门](device-update-raspberry-pi.md)，可通过开源扩展，根据需要为其他体系结构生成自己的映像。

- [开始使用 Ubuntu (18.04 x64) 模拟器引用代理](device-update-simulator.md)

- [包更新：开始使用 Ubuntu Server 18.04 x64 包代理](device-update-ubuntu-agent.md)

- [Azure 实时操作系统的 Device Update for Azure IoT Hub 教程](device-update-azure-real-time-operating-system.md)
