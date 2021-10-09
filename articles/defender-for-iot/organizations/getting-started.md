---
title: 快速入门：入门
description: 在本快速入门中，你将开始了解 Defender for IoT 部署的基本工作流。
ms.topic: quickstart
ms.date: 06/06/2021
ms.openlocfilehash: 32565bfcf87facb957dc4e8b1fc73f52eb1c7ac2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836409"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>快速入门：Defender for IoT 入门

本文概述了设置 Azure Defender for IoT 的步骤。 此过程要求：

- 在 Azure Defender for IoT 门户注册订阅和传感器。
- 安装传感器和本地管理控制台软件。
- 执行传感器和管理控制台的初始激活。

## <a name="permission-requirements"></a>权限要求

### <a name="for-sensors-and-on-premises-management-consoles"></a>对于传感器和本地管理控制台

某些设置步骤需要特定的用户权限。

激活传感器和管理控制台、上传 SSL/TLS 证书以及生成新密码都需要管理用户权限。
### <a name="for-the-defender-for-iot-portal"></a>对于 Defender for IoT 门户

下表介绍对于 Azure Defender for IoT 门户工具的用户访问权限：

| 权限 | 安全读取者 | 安全管理员 | 订阅参阅者 | 订阅所有者 |
|--|--|--|--|--|
| 查看详细信息并访问软件、激活文件和威胁情报包  | ✓ | ✓ | ✓ | ✓ |
| 加入传感器  |  |  ✓ | ✓ | ✓ |
| 加入订阅并更新提交的设备  |  |  | ✓ | ✓ |
| 恢复密码  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>识别解决方案基础结构

阐明网络设置需求

研究你的：

- 网络体系结构
- 受监视带宽
- 创建证书的要求
- 其他网络详细信息。

有关详细信息，请参阅 [Azure Defender for IoT 网络设置简介](how-to-set-up-your-network.md)。

阐明处理网络负载所需的传感器和管理控制台设备

Azure Defender for IoT 支持物理和虚拟部署。 对于物理部署，你可以购买各种经过认证的设备。 有关详细信息，请参阅[确定所需的设备](how-to-identify-required-appliances.md)。

我们建议你计算要监视的设备的大概数量。 稍后当你向门户注册 Azure 订阅时，系统会要求输入此数量。 每隔 1,000 秒可添加一次数字，例如 1000，2000，3000。 受监视的设备的数量被称作已提交设备数。

## <a name="register-with-azure-defender-for-iot"></a>注册 Azure Defender for IoT

注册包括：

- 将 Azure 订阅加入 Defender for IoT。
- 定义已提交设备。
- 下载本地管理控制台的激活文件。

你还可以使用试用订阅免费监视 1000 台设备 30 天。 有关详细信息，请参阅[加入试用订阅](how-to-manage-subscriptions.md#onboard-a-trial-subscription)。

若要注册，请执行以下操作：

1. 转到 Azure Defender for IoT 门户。

1. 选择“加入订阅”。

1. 在“定价”页面，选择一个订阅或创建新的订阅，然后添加已提交设备的数量。

1. 选择“下载本地管理控制台”选项卡，并保存已下载的激活文件。 此文件包含你定义的聚合提交设备。 首次登录后，文件将上传到管理控制台。

要详细了解如何登出订阅，请参阅[登出订阅](how-to-manage-subscriptions.md#offboard-a-subscription)。



## <a name="install-and-set-up-the-on-premises-management-console"></a>安装并设置本地管理控制台

获取本地管理控制台设备后：

- 从 Azure Defender for IoT 门户下载 ISO 包。
- 安装软件。
- 激活并执行初始管理控制台设置。

若要进行安装和设置，请执行以下操作：

1. 在 Defender for IoT 门户中选择“开始使用”。

1. 选择“本地管理控制台”选项卡。

1. 选择一个版本，然后选择“下载”。

1. 安装本地管理控制台软件。 有关详细信息，请参阅 [Defender for IoT 安装](how-to-install-software.md)。

1. 激活并设置管理控制台。 有关详细信息，请参阅[激活并设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)。

## <a name="onboard-a-sensor"></a>加入传感器 ##

通过在 Azure Defender for IoT 注册传感器并下载传感器激活文件，加入传感器：

1. 定义传感器名称并将其与订阅关联。

1. 选择传感器连接模式：

   - 云连接的传感器：传感器检测到的信息会在传感器控制台中显示。 此外，警告信息会通过 IoT 中心传递，并且可以与 Azure Sentinel 等其他 Azure 服务共享。  还可以选择自动将威胁情报包从 Azure Defender for IoT 门户推送到传感器。 有关详细信息，请参阅[威胁情报研究和包](how-to-work-with-threat-intelligence-packages.md)。

   - 本地管理的传感器：传感器检测到的信息会在传感器控制台中显示。 如果使用气隙网络，并且想要统一查看由多个本地托管的传感器检测到的所有信息，请使用本地管理控制台。

1. 在 IoT 中心内选择要将传感器关联到的站点。 IoT 中心将作为此传感器与 Azure Defender for IoT 之间的网关。 定义显示名称和区域。 还可以添加说明性标记。 显示名称、区域和标记是[“站点和传感器”页](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors)上的说明性条目。

1. 选择“注册”。

1. 选择“下载激活文件”。

有关加入的详细信息，请参阅[在 Defender for IoT 门户中加入和管理传感器](how-to-manage-sensors-on-the-cloud.md)。

## <a name="install-and-set-up-the-sensor"></a>安装并设置传感器

从 Azure Defender for IoT 门户下载 ISO 包，安装软件，并设置传感器。

1. 在 Defender for IoT 门户中选择“开始使用”。

1. 选择“设置传感器”。

1. 选择一个版本，然后选择“下载”。

1. 安装传感器软件。 有关详细信息，请参阅 [Defender for IoT 安装](how-to-install-software.md)。

1. 激活并设置传感器。 有关详细信息，请参阅[登录并激活传感器](how-to-activate-and-set-up-your-sensor.md)。

## <a name="connect-sensors-to-an-on-premises-management-console"></a>将传感器连接到本地管理控制台

将传感器连接到管理控制台，以确保：

- 传感器会将警报和设备清单信息发送到本地管理控制台。

- 本地管理控制台可以执行传感器备份，管理传感器检测到的警报，调查传感器断开连接，并且在连接的传感器上执行其他活动。

建议将监视相同网络的多个传感器分组到一个区域中。 执行此操作会联合多个传感器收集的信息。

有关详细信息，请参阅[将传感器连接到本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)。

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>用警报信息填充 Azure Sentinel（可选）

通过配置 Azure Sentinel 将警报信息发送到 Azure Sentinel。 请参阅[将数据从 Defender for IoT 连接到 Azure Sentinel](how-to-configure-with-sentinel.md)。  

## <a name="next-steps"></a>后续步骤 ##

[欢迎使用 Azure Defender for IoT](overview.md)

[Azure Defender for IoT 体系结构](architecture.md)
