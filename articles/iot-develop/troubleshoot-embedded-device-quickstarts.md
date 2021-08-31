---
title: Azure RTOS 嵌入式设备快速入门故障排除
description: 这些步骤旨在帮助你排查使用 Azure RTOS 嵌入式设备快速入门时的常见问题
author: JimacoMS4
ms.author: v-jbrannian
ms.service: iot-develop
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.openlocfilehash: 26353064f929c382559216c28651757c7b4ee498
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124687"
---
# <a name="troubleshooting-the-azure-rtos-embedded-device-quickstarts"></a>Azure RTOS 嵌入式设备快速入门故障排除

按照[嵌入式设备开发快速入门](quickstart-devkit-mxchip-az3166.md)操作时，可能会遇到一些常见问题。 通常，以下任一源中可能会出现问题：

* 你的环境。 计算机、软件或网络设置和连接。
* 你的 Azure IoT 资源。 你创建的用于连接到 Azure IoT 的 IoT 中心和设备。
* 你的设备。 物理板及其配置。

本文针对完成快速入门时可能会出现的最常见问题提供建议的解决方法。

## <a name="prerequisites"></a>先决条件

所有故障排除步骤都需要完成你正在处理的快速入门的以下先决条件：

* 已安装或获取了快速入门的所有必备组件和软件工具。
* 创建了 Azure IoT 中心或 Azure IoT Central 应用程序，并按照快速入门中的指示注册了设备。
* 已按照快速入门中的指示为设备生成了映像。

## <a name="issue-the-source-directory-doesnt-contain-cmakeliststxt-file"></a>问题：源目录不包含 CMakeLists.txt 文件
### <a name="description"></a>说明
尝试生成项目时，可能会出现此问题。 这是错误地从 GitHub 克隆项目的结果。 该项目包含多个默认情况下不会克隆的子模块，除非使用“--recursive” 标志。

### <a name="resolution"></a>解决方案
* 使用 Git 克隆存储库时，请确认存在“--recursive”选项。

## <a name="issue-the-build-fails"></a>问题：生成失败

### <a name="description"></a>说明

发生此问题的原因是，对象文件的路径超出了 Windows 中的默认最大路径长度。 检查生成输出中的消息，如下所示：

```output
-- Configuring done
CMake Warning in C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/core/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeLists.txt:
  The object file directory

    C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/NXP/MIMXRT1060-EVK/build/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeFiles/asc_security_core.dir/./

  has 208 characters.  The maximum full path to an object file is 250
  characters (see CMAKE_OBJECT_PATH_MAX).  Object file

    src/serializer/extensions/custom_builder_allocator.c.obj

  cannot be safely placed under this directory.  The build may not work
  correctly.


-- Generating done
```

### <a name="resolution"></a>解决方案

可以尝试使用以下选项之一来解决此错误：
* 将存储库克隆到路径较短的目录，然后重试。
* 按照[最大路径长度限制](/windows/win32/fileio/maximum-file-path-limitation)中的说明，在 Windows 10 版本 1607 及更高版本中启用长路径。

## <a name="issue-device-cant-connect-to-iot-hub"></a>问题：设备无法连接到 IoT 中心

### <a name="description"></a>说明

创建 Azure 资源并刷写设备后，可能会出现此问题。 尝试将新刷写的设备连接到 Azure IoT 时，会看到如下所示的控制台消息：

```output
Unable to resolve DNS for MQTT Server
```

### <a name="resolution"></a>解决方案

* 检查在 azure_config.h 文件中为 IoT 配置输入的配置值的拼写和大小写。 某些 IoT 资源属性的值（如 `deviceID` 和 `primaryKey`）区分大小写。

## <a name="issue--wi-fi-is-unable-to-connect"></a>问题：Wi-Fi 无法连接

### <a name="description"></a>说明

刷写使用 Wi-Fi 连接的设备并尝试连接到 Wi-Fi 网络后，你收到一条错误消息，指示 Wi-Fi 无法连接。

### <a name="resolution"></a>解决方案

* 检查 Wi-Fi 频率和设置。 嵌入式设备快速入门中使用的设备都使用 2.4 GHz。 确认 Wi-Fi 路由器是否配置为支持 2.4 GHz 网络。
* 检查 Wi-Fi 模式。 确认在 azure_config.h 文件中对 WIFI_MODE 常量使用了哪些设置。 检查 Wi-Fi 网络安全或身份验证设置，以确认 Wi-Fi 安全模式是否与配置文件中的内容匹配。

## <a name="issue-flashing-the-board-fails"></a>问题：刷写面板失败

### <a name="description"></a>说明

无法完成设备刷写过程。 如果遇到以下任何症状，就会知道遇到了该问题：

* 你构建的 *.bin 映像文件不会复制到设备。
* 用于刷写设备的实用工具发出警告或显示错误。
* 用于刷写设备的实用工具未指示编程已成功完成。

### <a name="resolution"></a>解决方案

* 确保已连接到设备上正确的 USB 端口。 某些设备具有多个端口。
* 请尝试使用不同的 Micro USB 线缆。 某些设备和线缆不兼容。
* 尝试连接到计算机上的其他 USB 端口。 USB 端口可能在内部断开连接、在软件中禁用或暂时处于不可用状态。
* 重新启动计算机。

## <a name="issue-device-fails-to-connect-to-port"></a>问题：设备无法连接到端口

### <a name="description"></a>说明

刷写设备并连接到计算机后，终端软件中显示如下消息：

```output
Failed to initialize the port.
Please verify the COM port settings.
```

### <a name="resolution"></a>解决方案

* 在终端软件的设置中，检查“端口”设置，确认选择了正确的端口。 如果显示多个端口，可以打开 Windows 设备管理器并选择“端口”节点，为连接的设备查找正确端口。

## <a name="issue-terminal-output-shows-garbled-text"></a>问题：终端输出显示已乱码文本

### <a name="description"></a>说明

成功刷写设备并连接到计算机后，终端软件中显示乱码文本输出。

### <a name="resolution"></a>解决方案

* 在终端软件的设置中，确认“波特率”设置为”115,200”。

## <a name="issue-terminal-output-shows-no-text"></a>问题：终端输出未显示文本

### <a name="description"></a>说明

成功刷写设备并连接到计算机后，终端软件中未显示任何输出。

### <a name="resolution"></a>解决方案

* 确认终端软件中的设置与快速入门中的设置匹配。
* 重启终端软件。
* 按设备上的“重置”按钮。
* 确认 USB 线缆已正确连接。

## <a name="issue-communication-between-device-and-iot-hub-fails"></a>问题：设备和 IoT 中心之间的通信失败

### <a name="description"></a>说明

刷写设备并连接到计算机后，终端软件中显示重复消息，如下所示：

```output
Failed to publish temperature
```

### <a name="resolution"></a>解决方案

* 确认定价层和缩放层是免费层或标准层之一。 不支持基本层，因为它不支持云到设备和设备孪生通信。

## <a name="next-steps"></a>后续步骤

如果查看本文中的问题后，仍无法监视终端中的设备或连接到 Azure IoT，则设备的硬件或物理配置可能出现问题。 请参阅设备的制造商页面，查找文档和支持选项。

* [STMicroelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)
* [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)
* [微芯片 ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)