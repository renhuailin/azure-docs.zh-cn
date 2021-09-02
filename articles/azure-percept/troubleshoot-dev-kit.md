---
title: Azure Percept DK 设备故障排除
description: 获取 Azure Percept DK 和 IoT Edge 中某些较常见问题的故障排除技巧
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e644732a90652b1672dc77bfc6db86d7a6b7295a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221087"
---
# <a name="troubleshoot-the-azure-percept-dk-device"></a>Azure Percept DK 设备故障排除

此故障排除文章的目的是帮助 Azure Percept DK 用户快速解决其开发工具包的常见问题。 它还提供了有关如何在需要额外支持时收集日志的指导。

## <a name="log-collection"></a>日志收集
本部分将针对要收集哪些日志以及如何收集它们提供指导。

### <a name="how-to-collect-logs"></a>如何收集日志
1. [通过 SSH](./how-to-ssh-into-percept-dk.md) 连接到开发工具包。
1. 在 SSH 终端窗口中运行所需的命令。 有关日志收集命令的列表，请参阅下一部分。
1. 若要将任何输出重定向到 .txt 文件以做进一步分析，请使用以下语法：
    ```console
    sudo [command] > [file name].txt
    ```
1. 更改 .txt 文件的权限，以便可以复制它：
    ```console
    sudo chmod 666 [file name].txt
    ```
1. 通过 SCP 将文件复制到主机电脑：
    ```console
    scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
    ```

    ```[local host file path]``` 是指主机电脑上要将 .txt 文件复制到的位置。 ```[remote username]``` 是指在[安装体验](./quickstart-percept-dk-set-up.md)过程中选择的 SSH 用户名。

### <a name="log-types-and-commands"></a>日志类型和命令

|日志用途      |何时收集它         |Command                     |
|-----------------|---------------------------|----------------------------|
|支持包 - 提供大多数客户支持请求所需的一组日志。|在请求支持时收集。|```sudo iotedge support-bundle --since 1h``` <br><br>“--since 1h”可以更改为任何时间跨度，例如“6h”（6 小时）、“6d”（6 天）或“6m”（6 分钟）|
|OOBE 日志 - 记录关于安装体验的详细信息。|在安装体验过程中发现问题时收集。|```sudo journalctl -u oobe -b```|
|edgeAgent 日志 - 记录设备上运行的所有模块的版本号。|当一个或多个模块不工作时收集。|```sudo iotedge logs edgeAgent```|
|模块容器日志 - 记录有关特定 IoT Edge 模块容器的详细信息|发现模块出现问题时收集|```sudo iotedge logs [container name]```|
|网络日志 - 一组涵盖 Wi-Fi 服务和网络堆栈的日志。|在发现 Wi-Fi 或网络问题时收集。|```sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd > network_log.txt```<br><br>```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version && rpm -q ztpd > system_ver.txt```<br><br>同时运行这两个命令。 每个命令收集多个日志，并将这些日志置于单个输出中。|

## <a name="troubleshooting-commands"></a>命令疑难解答
下面是一组命令，可用于排查开发工具包中可能发现的问题。 若要运行这些命令，必须先[通过 SSH](./how-to-ssh-into-percept-dk.md) 连接到开发工具包。 

有关 Azure IoT Edge 命令的详细信息，请参阅 [Azure IoT Edge 设备故障排除文档](../iot-edge/troubleshoot.md)。 

|函数         |何时使用                    |Command                 |
|------------------|----------------------------|---------------------------|
|检查开发工具包上的软件版本。|在需要确认开发工具包上的软件版本时使用。|```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version```|
|检查开发工具包的温度|在你认为开发工具包可能过热的情况下使用。|```cat /sys/class/thermal/thermal_zone0/temp```|
|检查开发工具包的遥测 ID|在需要了解开发工具包的唯一遥测标识符的情况下使用。|```sudo azure-device-health-id```|
|检查 IoT Edge 的状态|在连接到云的 IoT Edge 模块出现问题时使用。|```sudo iotedge check```|
|重启 Azure IoT Edge 安全守护程序|在 IoT Edge 无响应或无法正常工作时使用。|```sudo systemctl restart iotedge``` |
|列出已部署的 Azure IoT Edge 模块|在需要查看开发工具包上部署的所有模块时使用|```sudo iotedge list``` |
|显示指定文件系统中的可用空间/总空间|在需要了解开发工具包上的可用存储时使用。|```df [option] [file]```|
|显示开发工具包的 IP 和接口信息|在需要了解开发工具包的 IP 地址时使用。|`ip route get 1.1.1.1`        | 
|仅显示开发工具包的 IP 地址|在仅需要开发工具包的 IP 地址而不需要其他接口信息时使用。|<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |

## <a name="usb-update-errors"></a>USB 更新错误

|错误：                                    |解决方案：                                               |
|------------------------------------------|--------------------------------------------------------|
|通过 UUU 更新 USB 闪存期间发生 LIBUSB_ERROR_XXX |此错误是由于 UUU 更新期间 USB 连接失败而导致的。 如果 USB 电缆未正确连接到电脑或 Percept DK 承载板上的 USB 端口，则会出现这种形式的错误。 尝试拔下再重新连接 USB 数据线的两端，然后轻轻摇晃数据线以确保连接稳固。|

## <a name="clearing-hard-drive-space-on-the-azure-percept-dk"></a>清理 Azure Percept DK 上的硬盘空间
有两个组件占用了 Azure Percept DK 上的硬盘空间：Docker 容器日志和 Docker 容器本身。 为了确保容器日志不会占用全部硬盘空间，Azure Percept DK 内置了日志轮换功能，可以在生成新日志时轮换掉任何旧日志。

对于 Docker 容器数量导致硬盘空间问题的情况，可以按照以下步骤删除未使用的容器：
1. [通过 SSH 连接到开发工具包](./how-to-ssh-into-percept-dk.md)
1. 运行以下命令：`docker system prune`

这将删除所有未使用的容器、网络、映像和可选卷。 有关更多详细信息，请[转到此页](https://docs.docker.com/engine/reference/commandline/system_prune/)。

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK 承载板 LED 状态

承载板外壳的顶部有三个小型 LED。 LED 1 的旁边印刷了云图标，LED 2 的旁边印刷了 Wi-Fi 图标，LED 3 的旁边印刷了感叹号图标。 有关每种 LED 状态的信息，请参阅下表。

|LED             |状态      |说明                      |
|----------------|-----------|---------------------------------|
|LED 1（IoT 中心） |点亮（纯色） |设备已连接到 IoT 中心。 |
|LED 2 (Wi-Fi)   |缓慢闪烁 |设备已准备好由 Wi-Fi Easy Connect 进行配置，并在向配置器通告其存在性。 |
|LED 2 (Wi-Fi)   |快速闪烁 |身份验证成功，设备关联正在进行。 |
|LED 2 (Wi-Fi)   |点亮（纯色） |身份验证和关联成功；设备已连接到 Wi-Fi 网络。 |
|LED 3           |NA         |LED 未使用。 |