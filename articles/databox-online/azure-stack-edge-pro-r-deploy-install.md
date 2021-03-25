---
title: 教程：安装 Azure Stack Edge Pro R 物理设备 | Microsoft Docs
description: 有关 Azure Stack Edge Pro R 安装的第二篇教程涉及到如何排布物理设备的电源线和网线。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96463533"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>教程：安装 Azure Stack Edge Pro R

本教程介绍如何安装 Azure Stack Edge Pro R 物理设备。 安装过程涉及到设备布线。

安装可能需要大约 30 分钟才能完成。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 检查设备
> * 为设备布线

## <a name="prerequisites"></a>先决条件

下面是安装物理设备所要满足的先决条件：

### <a name="for-the-azure-stack-edge-resource"></a>对于 Azure Stack Edge 资源

在开始之前，请确保：

* 已完成[准备部署 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md) 中的所有步骤。
    * 已创建用于部署设备的 Azure Stack Edge 资源。
    * 已生成用于通过 Azure Stack Edge 资源激活设备的激活密钥。

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>对于 Azure Stack Edge Pro R 物理设备

在部署设备之前：

- 请确保将设备安全地放置在平坦、稳定的水平工作台面上。
- 检查进行安装的场地是否具有：
    - 从独立线路引出的标准交流电源

        \- 或 -
    - 机架式配电装置 (PDU)。 设备带有一个不间断电源 (UPS)
    

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络

开始之前：

- 查看部署 Azure Stack Edge Pro R 的网络要求，并根据要求配置数据中心网络。 有关详细信息，请参阅 [Azure Stack Edge Pro R 网络要求](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements)。

- 请确保最小 Internet 带宽为 20 Mbps，以便实现设备的理想运行。


## <a name="inspect-the-device"></a>检查设备

此设备单独打包装运。 按以下步骤拆开设备包装。

1. 将箱子放在平坦的水平表面上。
2. 检查设备运送箱是否有任何损坏。 打开运送箱检查设备。 如果运送箱或设备看起来有损伤，请联系 Microsoft 支持部门来帮助评估该设备是否处于良好的工作状态。
3. 打开运送箱后，确保里面有：
    - 一台单机箱 Azure Stack Edge Pro 设备
    - 一个不间断电源 (UPS)
    - 两根用于将设备连接到 UPS 的短电源线
    - 一根用于将 UPS 连接到电源的电源线

如果上述任一部件缺失，请联系 Azure Stack Edge Pro R 支持部门。 接下来是对设备进行布线。


## <a name="cable-the-device"></a>为设备布线

以下过程说明如何为 Azure Stack Edge Pro R 设备排布电源线和网线。

在开始为设备布线之前，需要符合以下先决条件：

- Azure Stack Edge Pro R 物理设备在安装位置。
- 一根电源线。
- 至少一根用于连接管理接口的 1-GbE RJ-45 网线。 设备上有两个 1-GbE 网络接口：一个用于管理，一个用于数据。
- 一根 10/25-GbE SFP+ 铜缆用于要配置的每个数据网络接口。 至少需要将一个数据网络接口（来自端口 3 或端口 4）连接到 Internet（在连接到 Azure 的情况下）。  
- 可访问一个配电装置（推荐）。

> [!NOTE]
> - 如果仅连接一个数据网络接口，则建议使用 25/10-GbE 网络接口（例如端口 3 或端口 4）将数据发送到 Azure。 
> - 为获得最佳性能并处理大量数据，请考虑连接所有数据端口。
> - Azure Stack Edge Pro R 设备应连接到数据中心网络，这样才能从数据源服务器引入数据。

在 Azure Stack Edge Pro R 设备上：

- 前面板有磁盘驱动器和电源按钮。

    - 设备正面有 8 个磁盘插槽。
    - 设备里面还有 2 个 M.2 SATA 磁盘，它们充当操作系统磁盘。 

- 背板带有冗余电源单元 (PSU)。
- 背板有 4 个网络接口：

    - 两个 1-Gbps 接口。
    - 2 个 25-Gbps 接口，它们还可用作 10-Gbps 接口。
    - 主板管理控制器 (BMC)。

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
执行以下步骤，为设备排布电源线和网线。

1. 识别设备背板上的各个端口。

    ![已布线设备的背板](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. 在设备正面找到磁盘插槽和电源按钮。

    ![设备的前面板](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. 将电源线的一端连接到 UPS。 将电源线的另一端连接到机架式配电装置 (PDU)。 
5. 按下电源按钮打开设备。
6. 将 1-GbE 网络接口的端口 1 连接到用于配置物理设备的计算机。 端口 1 是专用的管理接口。
7. 将一个或多个端口 2、端口 3 或端口 4 连接到数据中心网络/Internet。

    - 如果连接端口 2，请使用 RJ-45 网线。
    - 对于 10/25-GbE 网络接口，请使用 SFP+ 铜缆。

## <a name="next-steps"></a>后续步骤

本教程介绍了 Azure Stack Edge Pro R 主题，例如如何：

> [!div class="checklist"]
> * 拆开设备包装
> * 为设备布线

请继续学习下一教程，了解如何连接到设备。

> [!div class="nextstepaction"]
> [连接到 Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
