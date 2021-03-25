---
title: 排查 Azure Percept DK 和 IoT Edge 的一般性问题
description: 获取在加入体验过程中发现的一些较常见问题的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a6d099e8d267c9fe03e0bb676276e7a4ab8157ab
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521520"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Azure Percept DK（开发工具包）故障排除

参阅下面有关一般性 Azure Percept DK 故障排除提示的指导。

## <a name="general-troubleshooting-commands"></a>用于一般性故障排除的命令

若要运行这些命令： 
1. 请连接到[开发工具包的 Wi-Fi AP](./quickstart-percept-dk-set-up.md)
1. [通过 SSH 连接到开发工具包](./how-to-ssh-into-percept-dk.md)
1. 在 SSH 终端中输入命令

若要将任何输出重定向到 .txt 文件以做进一步分析，请使用以下语法：

```console
[command] > [file name].txt
```

将输出重定向到 .txt 文件后，通过 SCP 将该文件复制到主机电脑：

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` 是指主机电脑上要将 .txt 文件复制到的位置。 ```[remote username]``` 是指在[安装体验](./quickstart-percept-dk-set-up.md)过程中选择的 SSH 用户名。 如果在 OOBE 期间未设置 SSH 登录，远程用户名为 ```root```。

有关 Azure IoT Edge 命令的更多信息，请参阅 [Azure IoT Edge 设备故障排除文档](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

|类别：         |命令：                    |函数：                  |
|------------------|----------------------------|---------------------------|
|OS                |```cat /etc/os-release```         |检查 Mariner 映像的版本 |
|OS                |```cat /etc/os-subrelease```      |检查衍生映像的版本 |
|OS                |```cat /etc/adu-version```        |检查 ADU 版本 |
|温度       |```cat /sys/class/thermal/thermal_zone0/temp``` |检查开发工具包的温度 |
|WLAN             |```journalctl -u hostapd.service``` |检查 SoftAP 日志|
|WLAN             |```journalctl -u wpa_supplicant.service``` |检查 Wi-Fi 服务日志 |
|WLAN             |```journalctl -u ztpd.service```  |检查 Wi-Fi 零接触预配服务日志 |
|WLAN             |```journalctl -u systemd-networkd``` |检查 Mariner 网络堆栈日志 |
|WLAN             |```/data/misc/wifi/hostapd_virtual.conf``` |检查 Wi-Fi 接入点配置详细信息 |
|OOBE              |```journalctl -u oobe -b```       |检查 OOBE 日志 |
|遥测         |```azure-device-health-id```      |查找唯一的遥测 HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |针对常见问题运行配置和连接检查 |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |检查容器（例如语音和视觉模块）的日志 |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |收集模块日志、Azure IoT Edge 安全管理器日志、容器引擎日志、```iotedge check``` JSON 输出，以及过去一小时的其他有用调试信息 |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |查看 Azure IoT Edge 安全管理器的日志 |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |重启 Azure IoT Edge 安全守护程序 |
|Azure IoT Edge          |```sudo iotedge list```           |列出已部署的 Azure IoT Edge 模块 |
|其他             |```df [option] [file]```          |显示有关指定文件系统中可用空间/总空间的信息 |
|其他             |```ip route get 1.1.1.1```        |显示设备 IP 和接口信息 |
|其他             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |仅显示设备 IP 地址 |


可将 ```journalctl``` Wi-Fi 命令合并成以下单个命令：

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>用于 Docker 排除故障的命令

|命令：                        |函数：                  |
|--------------------------------|---------------------------|
|```docker ps``` |[显示哪些容器正在运行](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[显示设备上有哪些映像](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[从设备中删除映像](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[提取指定模块的容器日志](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[删除所有无标记的映像](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |检查 Docker 容器下载状态 |

## <a name="usb-updating"></a>USB 更新

|错误：                                    |解决方案：                                               |
|------------------------------------------|--------------------------------------------------------|
|通过 UUU 更新 USB 闪存期间发生 LIBUSB_ERROR_XXX |此错误是由于 UUU 更新期间 USB 连接失败而导致的。 如果 USB 数据线未正确连接到电脑或 PE-10X 上的 USB 端口，则会出现这种形式的错误。 尝试拔下再重新插入 USB 数据线的两端，然后轻轻摇晃数据线以确保连接稳固。 此方法几乎总能解决问题。 |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK 承载板 LED 状态

承载板外壳的顶部有三个小型 LED。 LED 1 的旁边印刷了云图标，LED 2 的旁边印刷了 Wi-Fi 图标，LED 3 的旁边印刷了感叹号图标。 有关每种 LED 状态的信息，请参阅下表。

|LED             |状态      |说明                      |
|----------------|-----------|---------------------------------|
|LED 1（IoT 中心） |点亮（纯色） |设备已连接到 IoT 中心。 |
|LED 2 (Wi-Fi)   |缓慢闪烁 |设备已准备好由 Wi-Fi Easy Connect 进行配置，并在向配置器通告其存在性。 |
|LED 2 (Wi-Fi)   |快速闪烁 |身份验证成功，设备关联正在进行。 |
|LED 2 (Wi-Fi)   |点亮（纯色） |身份验证和关联成功；设备已连接到 Wi-Fi 网络。 |
|LED 3           |NA         |LED 未使用。 |


