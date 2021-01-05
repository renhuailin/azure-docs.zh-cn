---
title: 入门
description: 开始了解用于 IoT 的基本工作流部署。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: ed6c88826b41df0bdfef8cbbcb2569b3cea8f868
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832380"
---
# <a name="getting-started-with-defender-for-iot"></a>用于 IoT 的 Defender 入门

本文概述了设置用于 IoT 的 Azure Defender 所需的步骤。 此过程要求您：

- 在 Azure Defender for IoT 门户中注册你的订阅和传感器。
- 安装传感器和本地管理控制台软件。
- 执行传感器和管理控制台的初始激活。

## <a name="permission-requirements"></a>权限要求

某些安装步骤需要特定的用户权限。

需要具有管理用户权限才能激活传感器和管理控制台、上传 SSL/TLS 证书，并生成新密码。

下表介绍了对 Azure Defender for IoT 门户工具的用户访问权限：

| 权限 | 安全读取者 | 安全管理员 | 订阅参与者 | 订阅所有者 |
|--|--|--|--|--|
| 查看 IoT 屏幕和数据的所有 Defender | ✓ | ✓ | ✓ | ✓ |
| 加入传感器  |  |  ✓ | ✓ | ✓ |
| 更新定价  |  |  ✓ | ✓ | ✓ |
| 恢复密码  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. 识别解决方案基础结构

**阐明网络设置需求**

研究您的网络体系结构、监视的带宽和其他网络详细信息。 有关详细信息，请参阅 [关于用于 IoT 的 Azure Defender 网络设置](how-to-set-up-your-network.md)。

**阐明处理网络负载所需的传感器和管理控制台设备**

Azure Defender for IoT 同时支持物理部署和虚拟部署。 对于物理部署，可以购买各种经过认证的设备。 有关详细信息，请参阅 [识别所需的设备](how-to-identify-required-appliances.md)。

建议你计算将监视的设备的近似数目。 稍后，当你将 Azure 订阅注册到门户时，系统会要求你输入此编号。 可按1000秒的间隔添加数字。 被监视设备的数量称为 "已 *提交设备*"。

## <a name="2-register-with-azure-defender-for-iot"></a>2. 向 Azure Defender 注册 IoT

注册包括：

- 将 Azure 订阅载入到 IoT 的 Defender。
- 定义已提交的设备。
- 下载本地管理控制台的激活文件。

若要注册，请执行以下操作：

1. 请参阅 Azure Defender for IoT 门户。
1. 选择 " **板载订阅**"。
1. 在 " **定价** " 页上，选择一个订阅或创建一个新订阅，并添加已提交设备的数量。
1. 选择 " **下载本地管理控制台** " 选项卡，并保存下载的激活文件。 此文件包含您定义的已提交的聚合设备。 首次登录后，该文件将上载到管理控制台。

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. 安装并设置本地管理控制台

获取本地管理控制台设备后：

- 从 Azure Defender for IoT 门户下载 ISO 包。
- 安装软件。
- 激活并执行初始管理控制台设置。

安装和设置：

1. 从 "IoT 门户" 入门中选择 "  。
1. 选择 " **本地管理控制台** " 选项卡。
1. 选择一个版本，然后选择 " **下载**"。
1. 安装本地管理控制台软件。 有关详细信息，请参阅 [用于 IoT 安装的 Defender](how-to-install-software.md)。
1. 激活并设置管理控制台。 有关详细信息，请参阅 [激活和设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)。

## <a name="4-onboard-a-sensor"></a>4. 板载传感器

通过将传感器注册到 Azure Defender 用于 IoT 并下载传感器激活文件，将传感器注册到其中：

1. 定义传感器名称并将其与订阅关联。
1. 选择传感器管理模式：

   - **云连接传感器**：传感器检测的信息将显示在传感器控制台中。 此外，警报信息通过 IoT 中心传送，可与其他 Azure 服务（如 Azure Sentinel）共享。

   - **本地托管传感器**：传感器检测的信息将显示在传感器控制台中。 如果使用的是气流网络，并且想要统一查看多个本地托管传感器检测到的所有信息，请使用本地管理控制台。 

1. 下载传感器激活文件。

有关详细信息，请参阅 [在 IoT 门户中板载和管理传感器](how-to-manage-sensors-on-the-cloud.md)。

## <a name="5-install-and-set-up-the-sensor"></a>5. 安装并设置传感器

从 Azure Defender for IoT 门户下载 ISO 包，安装软件，并设置传感器。

1. 从 "IoT 门户" 入门中选择 "  。
1. 选择 " **设置传感器**"。
1. 选择一个版本，然后选择 " **下载**"。
1. 安装传感器软件。 有关详细信息，请参阅 [用于 IoT 安装的 Defender](how-to-install-software.md)。
1. 激活并设置传感器。 有关详细信息，请参阅 [登录和激活传感器](how-to-activate-and-set-up-your-sensor.md)。

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. 将传感器连接到本地管理控制台

将传感器连接到管理控制台，以确保：

- 传感器将警报和设备清单信息发送到本地管理控制台。

- 本地管理控制台可以执行传感器备份，管理传感器检测的警报，调查传感器断开连接，并在连接的传感器上执行其他活动。

建议将监视同一网络的多个传感器组合到一个区域中。 这样做会合并多个传感器收集的信息。

有关详细信息，请参阅 [将传感器连接到本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)。

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. 用警报信息填充 Azure Sentinel (可选) 

通过配置 Azure Sentinel 将警报信息发送到 Azure Sentinel。 请参阅 [将数据从用于 IoT 的 Defender 连接到 Azure Sentinel](how-to-configure-with-sentinel.md)。

## <a name="see-also"></a>另请参阅

- [欢迎使用 Azure Defender for IoT](overview.md)

- [Azure Defender for IoT 体系结构](architecture.md)
