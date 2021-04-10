---
title: Azure 市场中 IoT Edge 模块产品/服务的预认证清单
description: 了解在 Azure 市场中发布 IoT Edge 模块产品/服务的特定认证要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562692"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>IoT Edge 模块的预认证清单

> [!NOTE]
> 我们强烈建议发布者先查看此清单并验证模块功能，然后再提交材料进行认证。 这可减少更改和重新提交的需求，从而加快认证过程。

## <a name="validation-of-image"></a>验证映像

准备好提交 Edge 模块映像后，请执行以下步骤，以确保映像按 Microsoft 期望的方式工作。

### <a name="steps-to-perform-in-the-azure-portal"></a>要在 Azure 门户中执行的步骤

1. 打开 [Azure 门户](https://partner.microsoft.com/)。
1. 创建资源组。
1. 创建 IoT 中心。
1. 创建 IoT Edge 设备。
1. 复制连接字符串并将其保存在记事本中。
1. 选择设置“创建的 Edge 设备上的模块”。
1. 添加最新版本映像所在的 ACR 的详细信息。
1. 选择“添加 IoT Edge 模块”并提供：
    - 模块设置中的映像 URI
    - 环境变量（与合作伙伴中心添加的环境变量相同）
    - 容器创建选项（与合作伙伴中心添加的此选项相同）
    - 模块孪生设置（与合作伙伴中心添加的此设置相同）
1. 添加路由（与合作伙伴中心添加的路由相同）。
1. 选择“查看 + 创建”。

Edge 模块部署在 Azure 中创建的 Edge 设备上。

### <a name="steps-to-perform-on-the-device"></a>要在设备上执行的步骤

#### <a name="device-details"></a>设备详细信息

认证团队使用以下硬件在不同体系结构上验证映像：

- 对于 X64 映像，配置大小与运行 Ubuntu Server 18.04/Ubuntu Server 16.04 的标准 D2s v3 相同的 Azure VM。
- 对于 Azure 资源管理器 32 映像，Raspberry Pi 3 模型 B。
- 对于 Azure 资源管理器 64 映像，NVIDIA Jetson Nano 4Gb。

#### <a name="steps"></a>步骤

1. 确保可通过 Putty 访问创建的设备/VM。
1. 将 [IoT Edge 运行时](../iot-edge/how-to-install-iot-edge.md)下载到设备上。
1. 将在步骤 5 中复制的连接字符串更新到 yaml 文件。
1. 重启 Edge 模块 `sudo systemctl restart iotedge`。
1. 使用 `sudo iotedge list` 检查是否已在设备上部署模块，该模块应处于“正在运行”状态。
1. 确保使用 `sudo iotedge logs “Module Name“ -f` 部署的模块的日志没有任何错误。 如果有已知错误，请在合作伙伴中心“审阅者注意事项”中进行描述，然后再提交产品/服务。

## <a name="metadata-validation"></a>元数据验证

检查下列各项：

- 合作伙伴中心和 Azure 容器注册表中均列出了最新标记。
- 产品/服务描述中添加了最低硬件要求。
- Azure 容器注册表用户名和密码已更新，并已添加到合作伙伴中心。
- 所需“孪生属性”的准确度（如果适用）。
- 所需“环境变量”的准确度（如果适用）。
- 所需“创建选项”的准确度（如果适用）。
- 存在“潜在顾客管理”连接字符串。
- 存在隐私策略
- 存在使用条款
- 从 [Azure IoT 设备目录](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible)添加支持的 IoT Edge 设备链接 

## <a name="next-steps"></a>后续步骤

- [从商业市场部署模块](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [在合作伙伴中心发布 Edge 模块](./partner-center-portal/azure-iot-edge-module-creation.md)
- [部署 IoT Edge 模块](../iot-edge/quickstart-linux.md)