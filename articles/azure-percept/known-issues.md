---
title: Azure Percept 已知问题
description: 详细了解 Azure Percept 已知问题及其解决方法
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a04e53c8444a01bc42f3ce71393fc842f3419e74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193402"
---
# <a name="known-issues"></a>已知问题

如果遇到这些问题，不一定需要开立 bug 票证。 如果在使用任何解决方法时遇到问题，请提交问题票证。

|区域|问题描述|解决方法|
|-------|---------|---------|
| 载入体验 | 如果设备未配置 Wi-Fi，则无法完成载入体验（Azure 登录失败）。 | 1.通过 SSH 连接到设备访问点 (10.1.1.1) <br> 2.识别并复制设备以太网 IP 地址 <br> 3.使用复制的基于 IP 的以太网 URL 连接到点播体验 |
| 载入体验 | 有时在载入期间单击 EULA 中的链接不会打开新网页。 | 复制链接并在单独的浏览器窗口中打开。 |
| 载入体验 | 当连接到移动 Wi-Fi 热点时，无法完成载入。 | 将设备直接连接到 SoftAP、Wi-Fi 网络，或通过以太网连接到网络。 |
| Wi-Fi/SoftAP | SoftAP 有时可能会断开连接或消失。 | 我们正在调查此问题。  重启设备通常可恢复。 |
| Wi-Fi | 用于启用和关闭 Wi-Fi SoftAP 的硬件按钮有时不起作用。 | 继续尝试按下按钮或重新启动设备。 |
| Wi-Fi | 用户在连接到 Wi-Fi 后可能会看到一条消息：“此 Wi-Fi 网络使用的是旧安全标准。” | Devkit 的热点/SoftAP 使用 WEP 加密算法。 |
| Wi-Fi | 无法从 Windows 10 计算机连接到 SoftAP，并显示以下错误消息： <br> “无法连接到此网络” | 重新启动 devkit 和计算机。 |
| 设备更新 | OTA 更新后容器不运行。 | 通过 SSH 连接到设备，并通过此命令 `systemctl restart iotedge` 重新启动 IoT Edge 容器。 这会重新启动所有容器。 |
| 设备更新 | 用户可能会收到一条消息，指示更新失败，即使更新已成功。 | 在 IoT 中心导航到设备的“设备孪生”，确认设备已更新。 该问题在第一次更新后已解决。 |
| 设备更新 | 用户在首次更新后可能会丢失其 Wi-Fi 连接设置。 | 请在更新后通过运行和完成载入体验来设置 Wi-Fi 连接。 该问题在第一次更新后已解决。 |
| 设备更新 | 执行 OTA 更新后，用户无法再使用以前创建的用户帐户通过 SSH 登录，并且无法通过载入体验来创建新的 SSH 用户。 此问题会影响从以下预安装的映像版本执行 OTA 更新的系统：2020.110.114.105 和 2020.109.101.105。 | 若要恢复用户配置文件，请在 OTA 更新后执行以下步骤： <br> 使用“root”作为用户名，[通过SSH 连接到 devkit](./how-to-ssh-into-percept-dk.md)。 如果通过载入体验禁用了 SSH“root”用户登录，需要重新启用它。 成功连接后，运行以下命令： <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> 若要恢复以前的用户主页数据，请运行以下命令： <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| 设备更新 | 进行 OTA 更新后，更新组丢失。 | 按照[这些说明](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air#create-a-device-update-group)更新设备的标记。 |
| 开发工具包安装程序 | 如果 Docker 没有在系统上正常运行，则可选的 Caffe 安装可能会失败。 | 请确保已安装并运行 Docker，然后重试 Caffe 安装。 |
| 开发工具包安装程序 | 可选的 CUDA 安装在不兼容的系统上失败。 | 在运行安装程序之前，验证系统与 CUDA 的兼容性。 |
| Docker, Network, IoT Edge | 如果内部网络使用 172.x.x.x，Docker 容器将无法连接到 Edge。 | 在 /etc/docker/daemon.json 文件中添加一个类似以下的特殊 bip 部分：`{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Azure Percept Studio 中的“查看流”链接不会打开显示设备 Web 流的新窗口。 | 1.打开 [Azure 门户](https://portal.azure.com)并选择“IoT 中心”。 <br> 2.单击设备连接到的 IoT 中心。 <br> 3.在“IoT 中心”页面的“自动设备管理”下选择“IoT Edge” 。 <br> 4.从列表中选择自己的设备。 <br> 5.“选择设备页面”顶部的“设置模块”。 <br> 6.单击 HostIpModule 旁边的垃圾桶图标以删除模块。 <br> 7.若要确认操作，请单击“查看 + 创建”，然后单击“创建” 。 <br> 8.打开 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)，然后单击左侧菜单面板上的“设备”。 <br> 9.从列表中选择自己的设备。 <br> 10.在“视觉”选项卡上，单击“查看设备流” 。 设备将下载新版本的 HostIpModule，并打开包含设备 Web 流的浏览器选项卡。 |