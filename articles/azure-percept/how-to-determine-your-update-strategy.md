---
title: 确定 Azure Percept DK 的更新策略
description: Azure Percept DK OTA 或 USB 数据线更新的优点和缺点。 为不同用户提供的用于选择最佳更新方法的建议。
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: c26de68bc30e8ebfa6de92c8e142e6ca69088b26
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223342"
---
# <a name="determine-your-update-strategy-for-azure-percept-dk"></a>确定 Azure Percept DK 的更新策略

为使 Azure Percept DK 软件保持最新，Microsoft 提供两种更新开发工具包的方法。 通过 USB 数据线更新或无线 (OTA) 更新 。

通过 USB 数据线更新会在开发工具包中进行全新安装。 部署新映像后，将会擦除每个分区中的现有配置和所有用户数据。 为此，需使用一根 Type-C USB 数据线将开发工具包连接到主机系统。 主机系统可以是 Windows/Linux 计算机。  也可以使用此更新方法实现出厂重置。 为此，需将完全相同的版本重新部署到开发工具包。 有关 USB 数据线更新的详细信息，请参阅[通过 USB-C 数据线连接更新 Azure Percept DK](./how-to-update-via-usb.md)。

OTA 更新建立在 [Device Update for IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-resources) Azure 服务的基础之上。 将开发工具包连接到 Azure IoT 中心即可执行这种更新。 完成 OTA 更新后，配置和用户数据将会保留。 有关执行 OTA 更新的详细信息，请参阅[以无线 (OTA) 方式更新 Azure Percept DK](./how-to-update-over-the-air.md)。

查看 USB 数据线更新和 OTA 更新的优点与缺点，然后按照适用于不同方案的 Microsoft 建议进行操作。

## <a name="usb-cable-update"></a>USB 数据线更新

- 优点
  - 无需将开发工具包连接到 Internet/Azure。
  - 无论当前在开发工具包上加载了哪个版本的软件和固件，最新映像始终适用。
- 缺点
  - 会重置设备映像并删除配置和用户数据。
  - 需要重新运行 OOBE 并下载任何未预先加载的容器。
  - 无法远程执行。

## <a name="ota-update"></a>OTA更新

- 优点
  - 保留用户数据、配置和下载的容器。 完成 OTA 更新后，开发工具包会继续像更新前那样正常工作。
  - 可以远程执行更新。
  - 可以同时更新多个类似的设备。 还可以安排更新时间，例如在晚上更新。
- 缺点
  - 可能会有无法跳过的必装版本。 请参阅 [OTA 的必装版本](./software-releases-over-the-air-updates.md#hard-stop-version-of-ota)。
  - 设备需要连接到正确配置了“Device Update for IoT Hub”功能的 IoT 中心。
  - 不是很适合用于实现降级。

> [!IMPORTANT]
> Device Update for IoT Hub 不会阻止部署版本比当前正在运行的 OS 更低的映像。 但是，在开发工具包中进行这种部署会导致数据和功能丢失。

## <a name="microsoft-recommendations"></a>Microsoft 的建议

|类型|方案|Update 方法|
|:---:|---|:---:|
|生产|在开发工具包已运行你的解决方案或已部署到现场的情况下，使开发工具包保持更新以便安装最新的修复和安全修补程序。|OTA|
|生产/开发|取出新的开发工具包并将其更新到最新软件。|USB|
|生产/开发|在已跳过多个每月发行版的情况下想要更新到最新软件版本。|USB|
|生产/开发|对开发工具包进行出厂重置。|USB|
|开发|在解决方案开发期间，想要使开发工具包的 OS 和固件保持最新。|USB/OTA|
|开发|跳到任何特定的（旧）版本进行调查/调试。|USB|

## <a name="next-steps"></a>后续步骤

确定所需的更新方法后，请访问以下页面以做好更新准备：

USB 数据线更新

- [通过 USB-C 数据线连接更新 Azure Percept DK](./how-to-update-via-usb.md)
- [适合通过 USB 数据线进行更新的 Azure Percept DK 软件版本](./software-releases-usb-cable-updates.md)

OTA

- [以无线方式 (OTA) 更新 Azure Percept DK](./how-to-update-over-the-air.md)
- [适合通过 OTA 进行更新的 Azure Percept DK 软件版本](./software-releases-over-the-air-updates.md)
