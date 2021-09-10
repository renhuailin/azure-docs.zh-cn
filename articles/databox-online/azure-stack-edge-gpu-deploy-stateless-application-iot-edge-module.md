---
title: 通过 IoT Edge 模块在 Azure Stack Edge Pro GPU 上部署 Kubernetes 无状态应用 | Microsoft Docs
description: 介绍如何使用可通过外部 IP 访问的 IoT Edge 模块，在 Azure Stack Edge Pro GPU 设备上部署 Kubernetes 无状态应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 233afd9b103a250633c5e49776fc0dfda55a0389
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112243"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>使用 IoT Edge 模块在 Azure Stack Edge Pro GPU 设备上运行 Kubernetes 无状态应用程序

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何使用 IoT Edge 模块在 Azure Stack Edge Pro 设备上部署无状态应用程序。

若要部署无状态应用程序，需要执行以下步骤：

- 在部署 IoT Edge 模块之前，请确保满足先决条件。
- 添加一个 IoT Edge 模块用于访问 Azure Stack Edge Pro 上的计算网络。
- 验证该模块是否可以访问已启用的网络接口。

在本操作指南文章中，你将使用一个 webserver 应用模块来演示该方案。

## <a name="prerequisites"></a>先决条件

在开始之前，需要：

- 一台 Azure Stack Edge Pro 设备。 请确保：

    - 已在设备上配置了计算网络设置。
    - 已根据[教程：激活设备](azure-stack-edge-gpu-deploy-activate.md)中的步骤激活了设备。
- 已根据[教程：在 Azure Stack Edge Pro 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)中所述在设备上完成了“配置计算”步骤。 设备应具有关联的 IoT 中心资源、IoT 设备和 IoT Edge 设备。


## <a name="add-webserver-app-module"></a>添加 webserver 应用模块

执行以下步骤，将 webserver 应用模块添加到 Azure Stack Edge Pro 设备。

1. 在与设备关联的 IoT 中心资源中，转到“自动设备管理”>“IoT Edge”。
1. 选择并单击与 Azure Stack Edge Pro 设备关联的 IoT Edge 设备。 

    ![选择 IoT Edge 设备](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. 选择“设置模块”。 在“在设备上设置模块”中，依次选择“+ 添加”、“IoT Edge 模块”。  

    ![选择 IoT Edge 模块](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. 在“添加 IoT Edge 模块”中：

    1. 为要部署的 webserver 应用模块指定一个 **名称**。
    2. 在“模块设置”选项卡下，提供模块映像的 **映像 URI**。 将检索与所提供的名称和标记相匹配的模块。 在本例中，`mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine` 将从公共 `mcr.microsoft.com` 注册表中拉取 nginx 映像（标记为 1.15.5-alpine）。

        ![添加 IoT Edge 模块](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. 在“容器创建选项”选项卡中粘贴以下示例代码：  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        此配置可让你使用基于 *http* 的计算网络 IP 在 TCP 端口 8080（默认的 webserver 端口为 80）上访问该模块。 选择 **添加** 。

        ![在 IoT Edge 自定义模块边栏选项卡中指定端口信息](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. 选择“查看 + 创建”。 检查模块详细信息，然后选择“创建”。

## <a name="verify-module-access"></a>验证模块访问

1. 验证模块是否已成功部署并正在运行。 在“模块”选项卡上，模块的运行时状态应是“正在运行”。   

    ![验证模块状态是否为“正在运行”](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. 若要获取 webserver 应用的外部终结点，请访问 [Kubernetes 仪表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)。 
1. 在仪表板的左窗格中，按照 **iotedge** 命名空间进行筛选。 转到“发现和负载均衡”>“服务”。 在列出的服务列表下，找到 webserver 应用模块的外部终结点。 

    ![在外部终结点连接到 webserver 应用](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. 选择外部终结点打开新的浏览器窗口。

    应会看到 webserver 应用正在运行。

    ![验证通过指定的端口连接到模块](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何通过 IoT Edge 模块公开有状态应用程序<!--insert link-->.
