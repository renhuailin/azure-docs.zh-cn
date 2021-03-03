---
title: 排查 Azure Percept 深色和 IoT Edge 的一般问题
description: 获取有关在快速体验过程中发现的一些更常见问题的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: c8027b62c0c463e134817f589ba3e1957cea5b39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679116"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Azure Percept 深色 (开发工具包) 故障排除

请参阅下面的指南，了解 Azure Percept 深色的一般故障排除提示。

## <a name="general-troubleshooting-commands"></a>常规疑难解答命令

若要运行这些命令， 
1. 连接到 [开发工具包的 Wi-Fi AP](./quickstart-percept-dk-set-up.md)
1. [通过 SSH 连接开发工具包](./how-to-ssh-into-percept-dk.md)
1. 在 SSH 终端中输入命令

若要将任何输出重定向到 .txt 文件以进行进一步分析，请使用以下语法：

```console
[command] > [file name].txt
```

将输出重定向到 .txt 文件后，请通过 SCP 将文件复制到主机 PC：

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` 指你要将 .txt 文件复制到的主机 PC 上的位置。 ```[remote username]``` 是在 [安装](./quickstart-percept-dk-set-up.md)过程中选择的 SSH 用户名。 如果未在 OOBE 期间设置 SSH 登录，则远程用户名为 ```root``` 。

有关 Azure IoT Edge 命令的其他信息，请参阅 [Azure IoT Edge 设备故障排除文档](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

|类别：         |命令：                    |函数：                  |
|------------------|----------------------------|---------------------------|
|(OS)                |```cat /etc/os-release```         |检查 Mariner 映像版本 |
|(OS)                |```cat /etc/os-subrelease```      |检查衍生映像版本 |
|(OS)                |```cat /etc/adu-version```        |检查 ADU 版本 |
|温度       |```cat /sys/class/thermal/thermal_zone0/temp``` |检查 devkit 的温度 |
|Wi-Fi             |```journalctl -u hostapd.service``` |检查 SoftAP 日志|
|Wi-Fi             |```journalctl -u wpa_supplicant.service``` |检查 Wi-Fi 服务日志 |
|Wi-Fi             |```journalctl -u ztpd.service```  |检查 Wi-Fi 零触控预配服务日志 |
|Wi-Fi             |```journalctl -u systemd-networkd``` |检查 Mariner 网络堆栈日志 |
|Wi-Fi             |```/data/misc/wifi/hostapd_virtual.conf``` |检查 wifi 访问点配置详细信息 |
|OOBE              |```journalctl -u oobe -b```       |检查 OOBE 日志 |
|遥测         |```azure-device-health-id```      |查找唯一遥测 HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |针对常见问题运行配置和连接检查 |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |检查容器日志，如语音和视觉模块 |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |收集来自过去一小时的模块日志、Azure IoT Edge 安全管理器日志、容器引擎日志、 ```iotedge check``` JSON 输出和其他有用的调试信息 |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |查看 Azure IoT Edge 安全管理员的日志 |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |重新启动 Azure IoT Edge 安全守护程序 |
|Azure IoT Edge          |```sudo iotedge list```           |列出已部署的 Azure IoT Edge 模块 |
|其他             |```df [option] [file]```          |显示有关指定文件系统 (s 中可用/总空间的信息)  |
|其他             |```ip route get 1.1.1.1```        |显示设备 IP 和接口信息 |
|其他             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |仅显示设备 IP 地址 |


```journalctl```可以将 Wi-Fi 命令合并为以下单个命令：

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker 故障排除命令

|命令：                        |函数：                  |
|--------------------------------|---------------------------|
|```docker ps``` |[显示正在运行的容器](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[显示设备上的映像](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[从设备中删除映像](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[获取指定模块的容器日志](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[删除所有无关联图像](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |检查 docker 容器下载状态 |

## <a name="usb-updating"></a>USB 更新

|错误：                                    |解决方案：                                               |
|------------------------------------------|--------------------------------------------------------|
|通过 UUU 在 USB 闪存期间 LIBUSB_ERROR_XXX |此错误是 UUU 更新过程中 USB 连接失败的结果。 如果 USB 电缆未正确地连接到电脑上的 USB 端口或 PE 10 倍，则会出现此窗体的错误。 尝试拔出并 replugging USB 电缆的两端，并 jiggling 电缆来确保安全连接。 这几乎总是能解决问题。 |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept 深色运营板 LED 状态

承运商基板顶部有三个小的 Led。 云图标将打印在 "LED 1" 旁边，在 "LED 2" 旁打印一个 Wi-Fi 图标，并在 "LED 3" 旁打印感叹号图标。 有关每个 LED 状态的信息，请参阅下表。

|LED             |状态      |说明                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT 中心)  | (纯色)  |设备已连接到 IoT 中心。 |
|LED 2 (Wi-fi)    |缓慢闪烁 |正在进行设备身份验证。 |
|LED 2 (Wi-fi)    |快速闪烁 |身份验证成功，设备关联正在进行。 |
|LED 2 (Wi-fi)    | (纯色)  |身份验证和关联成功;设备已连接到 Wi-Fi 网络。 |
|LED 3           |NA         |未使用 LED。 |


