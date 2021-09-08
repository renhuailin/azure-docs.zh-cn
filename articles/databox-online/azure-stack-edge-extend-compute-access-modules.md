---
title: 管理 Azure Stack Edge Pro FPGA 计算网络以访问模块
description: 介绍如何在 Azure Stack Edge Pro FPGA 上扩展计算网络，以通过外部 IP 访问模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 2b4b509e359976fcf8ff0c47270c814a203fcbdd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123108328"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>在 Azure Stack Edge Pro 上启用计算网络

本文介绍在 Azure Stack Edge Pro 上运行的模块如何访问设备上启用的计算网络。

若要配置网络，请执行以下步骤：

- 在 Azure Stack Edge Pro 设备上启用用于计算的网络接口
- 在 Azure Stack Edge Pro 上添加用于访问计算网络的模块
- 验证该模块是否可以访问已启用的网络接口

本教程将使用一个 webserver 应用模块来演示该方案。

## <a name="prerequisites"></a>先决条件

在开始之前，需要：

- 一个已完成设备设置的 Azure Stack Edge Pro 设备。
- 你已按照[教程：使用 Azure Stack Edge Pro 转换数据](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute)在设备上完成了“配置计算”步骤。 设备应具备关联的 IoT 中心资源、IoT 设备和 IoT Edge 设备。

## <a name="enable-network-interface-for-compute"></a>为计算启用网络接口

若要通过外部网络访问设备上运行的模块，需要为设备上的网络接口分配一个 IP 地址。 可以从本地 Web UI 管理这些计算设置。

在本地 Web UI 上执行下述步骤，以便配置计算设置。

1. 在本地 Web UI 中，转到“配置”>“计算设置”。  

2. 使你要使用的网络接口能够连接到将要在设备上运行的计算模块。

    - 如果使用静态 IP 地址，请为网络接口输入一个 IP 地址。
    - 如果使用 DHCP，则会自动分配 IP 地址。 本示例使用 DHCP。

    ![启用计算设置 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 选择“应用”以应用设置。 如果使用 DHCP，请记下分配给网络接口的 IP 地址。

    ![启用计算设置](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>添加 webserver 应用模块

执行以下步骤，将 webserver 应用模块添加到 Azure Stack Edge Pro 设备。

1. 转到与 Azure Stack Edge Pro 设备关联的 IoT 中心资源，然后选择“IoT Edge 设备”。
2. 选择与 Azure Stack Edge Pro 设备关联的 IoT Edge 设备。 在“设备详细信息”中，选择“设置模块” 。 在“添加模块”上，依次选择“+ 添加”、“IoT Edge 模块”。
3. 在“IoT Edge 自定义模块”边栏选项卡中：

    1. 为要部署的 webserver 应用模块指定一个 **名称**。
    2. 为模块映像提供“映像 URI”。 将检索与所提供的名称和标记相匹配的模块。 在这种情况下，`mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine` 将从公共 `mcr.microsoft.com` 注册表中拉取 nginx 映像（标记为 1.15.5-alpine）。
    3. 在“容器创建选项”中粘贴以下示例代码：  

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

        此配置可让你使用基于 *http* 的计算网络 IP 在 TCP 端口 8080（默认的 webserver 端口为 80）上访问该模块。

        ![在 IoT Edge 自定义模块边栏选项卡中指定端口信息](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. 选择“保存”。

## <a name="verify-module-access"></a>验证模块访问

1. 验证模块是否已成功部署并正在运行。 在“设备详细信息”页的“模块”选项卡上，模块的运行时状态应为“正在运行”。  
2. 连接到 webserver 应用模块。 打开浏览器窗口并键入：

    `http://<compute-network-IP-address>:8080`

    应会看到 webserver 应用正在运行。

    ![验证通过指定的端口连接到模块](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](azure-stack-edge-manage-users.md)。
