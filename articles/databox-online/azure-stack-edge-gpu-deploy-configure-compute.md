---
title: 教程：通过 Azure Stack Edge Pro GPU 的计算力筛选和分析数据 | Microsoft Docs
description: 了解如何在 Azure Stack Edge Pro GPU 上配置计算角色，并在将数据发送到 Azure 之前，使用该角色转换数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c884ad6850b8f94baa7c658d685651c3241be33f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935630"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>教程：在 Azure Stack Edge Pro GPU 设备上配置计算

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教程介绍如何在 Azure Stack Edge Pro 设备上配置计算角色以及创建 Kubernetes 群集。 

此过程可能需要大约 20 到 30 分钟才能完成。


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 获取 Kubernetes 终结点

 
## <a name="prerequisites"></a>先决条件

在 Azure Stack Edge Pro 设备上设置计算角色之前，请确保：

- 已按照[激活 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中所述的内容激活 Azure Stack Edge Pro 设备。
- 确保已按照[启用计算网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)中的说明进行操作，并且：
    - 已启用计算网络接口。
    - 已分配 Kubernetes 节点 IP 和 Kubernetes 外部服务 IP。

## <a name="configure-compute"></a>配置计算

若要在 Azure Stack Edge Pro 上配置计算，必须通过 Azure 门户创建一个 IoT 中心资源。

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“概述”，然后选择“IoT Edge” 。

   ![开始使用计算](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-1.png)

2. 在“启用 IoT Edge 服务”中，选择“添加” 。

   ![配置计算](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-2.png)

3. 在“配置 Edge 计算”边栏选项卡上输入以下信息：
   
   |字段  |值  |
   |---------|---------|
   |IoT 中心     | 选择“新建”或“现有”。  <br> 默认会使用标准层 (S1) 来创建 IoT 资源。 若要使用免费层 IoT 资源，请创建一个资源，然后选择现有的资源。 <br> 在每种情况下，IoT 中心资源都会使用 Azure Stack Edge 资源所用的同一订阅和资源组。     |
   |名称     |输入 IoT 中心资源的名称。         |

   ![开始使用计算 2](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-3.png)

4. 完成设置后，选择“查看 + 创建”。 查看 IoT 中心资源的设置，然后选择“创建”。

   IoT 中心资源的资源创建需要几分钟时间。 创建资源后，“概述”会指示 IoT Edge 服务现在正在运行。

   ![开始使用计算 3](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-4.png)

5. 若要确认是否已配置 Edge 计算角色，请选择“属性”。

   ![开始使用计算 4](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-5.png)

   如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。 某个 IoT Edge 运行时也在此 IoT Edge 设备上运行。 目前，只有 Linux 平台适用于你的 IoT Edge 设备。

由于要在后台创建虚拟机和 Kubernetes 群集，因此可能需要 20-30 分钟来配置计算。

在 Azure 门户中成功配置计算后，会有一个 Kubernetes 群集和一名与 IoT 命名空间（由 Azure Stack Edge Pro 控制的系统命名空间）关联的默认用户。

## <a name="get-kubernetes-endpoints"></a>获取 Kubernetes 终结点

若要配置客户端以访问 Kubernetes 群集，你需要获取 Kubernetes 终结点。 请按照以下步骤从 Azure Stack Edge Pro 设备的本地 UI 获取 Kubernetes API 终结点。

1. 在设备的本地 Web UI 中，转到“设备”页。
2. 在“设备终结点”下，复制“Kubernetes API 服务”终结点。 此终结点是采用以下格式的字符串：`https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`。 

    ![本地 UI 中的“设备”页](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. 保存该终结点字符串。 稍后配置客户端以通过 kubectl 访问 Kubernetes 群集时，将使用该终结点字符串。

4. 在本地 Web UI 中，你可以：

    - 转到 Kubernetes API，选择“高级设置”，然后下载 Kubernetes 的高级配置文件。 

        ![本地 UI 中的“设备”页 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        如果从 Microsoft 获得了密钥（特选用户可能有此密钥），则可使用此配置文件。

        ![本地 UI 中的“设备”页 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - 还可以转到“Kubernetes 仪表板”终结点并下载 `aseuser` 配置文件。 
    
        ![本地 UI 中的“设备”页 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        可以使用此配置文件登录到 Kubernetes 仪表板或调试 Kubernetes 群集中的任何问题。 有关详细信息，请参阅[访问 Kubernetes 仪表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)。 


## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 获取 Kubernetes 终结点


若要了解如何管理 Azure Stack Edge Pro 设备，请参阅：

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
