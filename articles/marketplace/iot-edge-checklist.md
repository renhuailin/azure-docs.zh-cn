---
title: Azure Marketplace 中的 IoT Edge 模块提供的预认证清单
description: 了解有关在 Azure Marketplace 中发布 IoT Edge 模块产品/服务的特定认证要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 4782e6c0ed866c467051fc1811a60df0010a3db1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744372"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>IoT Edge 模块的预认证清单

> [!NOTE]
> 我们强烈建议发布者查看此核对清单并验证模块功能，然后再提交证明。 这将减少更改和 resubmissions 的需求，从而加速你的认证过程。

## <a name="validation-of-image"></a>验证映像

边缘模块映像准备好进行提交后，请执行以下步骤，以确保映像按 Microsoft 期望的方式工作。

### <a name="steps-to-perform-in-the-azure-portal"></a>要在 Azure 门户中执行的步骤

1. 打开 [Azure 门户](https://partner.microsoft.com/)。
1. 创建资源组。
1. 创建 IoT 中心。
1. 创建 IoT Edge 设备。
1. 复制连接字符串，并将其保存在记事本中。
1. 选择 "设置 **边缘设备上的模块**"。
1. 添加最新版本的映像所在的 ACR 详细信息。
1. 选择 " **添加 IoT Edge 模块** ，并提供：
    - 模块设置中的图像 URI
    - 环境变量 (与合作伙伴中心添加的环境变量) 
    - 容器创建选项 (与合作伙伴中心添加的选项相同) 
    - 模块克隆设置 (与合作伙伴中心添加的内容相同) 
1. 添加路由 (与合作伙伴中心) 中添加的内容相同。
1. 选择“查看 + 创建”。

边缘模块部署在 Azure 上创建的边缘设备上。

### <a name="steps-to-perform-on-the-device"></a>要在设备上执行的步骤

#### <a name="device-details"></a>设备详细信息

认证团队使用以下硬件在不同体系结构上验证映像：

- 对于 X64 映像，其配置大小为运行 Ubuntu Server 18.04/Ubuntu Server 16.04 的标准 D2s v3 的 Azure VM。
- 对于 Azure 资源管理器32映像，Raspberry Pi 3 模型 B。
- 对于 Azure 资源管理器64映像，NVIDIA Jetson Nano 4Gb。

#### <a name="steps"></a>步骤

1. 确保可通过 Putty 访问创建的设备/VM。
1. 将 [IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge) 下载到设备上。
1. 将步骤5中复制的连接字符串更新为 yaml 文件。
1. 重新启动边缘模块 `sudo systemctl restart iotedge` 。
1. 检查是否已将模块部署到设备上， `sudo iotedge list` 它应处于 "正在运行" 状态。
1. 确保使用部署的模块的日志 `sudo iotedge logs “Module Name“ -f` 没有任何错误。 如果有已知错误，请在提交产品/服务之前，在合作伙伴中心 **说明中对** 此进行说明。

## <a name="metadata-validation"></a>元数据验证

检查下列各项：

- 合作伙伴中心和 Azure 容器注册表中均列出了最新标记。
- 产品/服务描述中添加了最低硬件要求。
- Azure 容器注册表用户名和密码会更新并添加到合作伙伴中心。
- 所需的每个 **属性** 的准确性（ *如果适用）*。
- 所需的 **环境变量** 的准确性（ *如果适用）*。
- 所需的 **创建选项** 的准确性（ *如果适用）*。
- 存在潜在客户管理连接字符串。
- 存在隐私策略
- 使用条款存在

## <a name="next-steps"></a>后续步骤

- [从商业应用商店部署模块](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [在合作伙伴中心发布边缘模块](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [部署 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
