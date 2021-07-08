---
title: 教程：安装 Azure Stack Edge Mini R 物理设备 | Microsoft Docs
description: 有关 Azure Stack Edge Mini R 设备安装的第二篇教程涉及到如何排布物理设备的电源线和网线。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 8bf73374545fa10c1b26ccbc9b932962fcabb6b3
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759862"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>教程：安装 Azure Stack Edge Mini R

本教程介绍如何安装 Azure Stack Edge Mini 物理设备。 安装过程涉及到设备布线。

安装可能需要大约 30 分钟才能完成。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 检查设备
> * 为设备布线

## <a name="prerequisites"></a>先决条件

下面是安装物理设备所要满足的先决条件：

### <a name="for-the-azure-stack-edge-resource"></a>对于 Azure Stack Edge 资源

在开始之前，请确保：

* 已完成[准备部署 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md) 中的所有步骤。
    * 已创建用于部署设备的 Azure Stack Edge 资源。
    * 已生成用于通过 Azure Stack Edge 资源激活设备的激活密钥。

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>对于 Azure Stack Edge Mini R 物理设备

在部署设备之前：

- 请确保将设备安全地放置在平坦、稳定的水平工作台面上。
- 检查进行安装的场地是否具有：
    - 从独立线路引出的标准交流电源   -或-
    - 机架式配电装置 (PDU)。 
    

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络

开始之前：

- 查看部署 Azure Stack Edge Mini R 的网络要求，并根据要求配置数据中心网络。 有关详细信息，请参阅 [Azure Stack Edge 网络要求](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements)。

- 确保 Internet 带宽至少为 20 Mbps，使设备以最佳状态运行。 <!-- engg TBC -->


## <a name="inspect-the-device"></a>检查设备

此设备单独打包装运。 按以下步骤拆开设备包装。

1. 将箱子放在平坦的水平表面上。
2. 检查设备运送箱是否有任何损坏。 打开运送箱检查设备。 如果运送箱或设备看起来有损伤，请联系 Microsoft 支持部门来帮助评估该设备是否处于良好的工作状态。
3. 打开运送箱后，确保里面有：
    - 一台配有侧式缓冲器的便携式 Azure Stack Edge Mini R 设备
    - 一个电池，还有安装到设备上的后盖。 
    - 一根将电池连接到电源的电源线 

如果上述任一部件缺失，请联系 Azure Stack Edge 支持部门。 接下来是对设备进行布线。


## <a name="cable-the-device"></a>为设备布线

以下过程说明如何为 Azure Stack Edge 设备排布电源线和网线。

在开始为设备布线之前，需要符合以下先决条件：

- Azure Stack Edge Mini R 物理设备在安装位置。
- 一根电源线。
- 至少一根用于连接管理接口的 1-GbE RJ-45 网线。 设备上有两个 1-GbE 网络接口：一个用于管理，一个用于数据。
- 一根 10-GbE SFP+ 铜缆用于要配置的每个数据网络接口。 至少需要将一个数据网络接口（来自端口 3 或端口 4）连接到 Internet（在连接到 Azure 的情况下）。

  强烈建议使用性能最佳的铜质 SFP+ (10 Gbps) 收发器。 可使用兼容的光纤收发器，但尚未经过测试。 有关详细信息，请参阅 Azure Stack Edge Mini R 的[收发器和电缆规格](azure-stack-edge-mini-r-technical-specifications-compliance.md#transceivers-cables)。
   
- 可访问一个配电装置（推荐）。

> [!NOTE]
> - 如果仅连接一个数据网络接口，则建议使用 10-GbE 网络接口（例如端口 3 或端口 4）将数据发送到 Azure。 
> - 为获得最佳性能并处理大量数据，请考虑连接所有数据端口。
> - Azure Stack Edge 设备应连接到数据中心网络，这样才能从数据源服务器引入数据。 <!-- engg TBC -->

在 Azure Stack Edge 设备上：

- 前面板上有一个 SSD 托盘。 

    - 设备插槽中有 1 个 SSD 盘。 
    - 设备还有一张 CFx 卡，它用作操作系统磁盘的存储区。
    
- 前面板上有网络接口，可连接 Wi-Fi。 

    - 2 个 1 GbE RJ 45 网络接口（设备本地 UI 上的端口 1 和端口 2）
    - 2 个 10 GbE SFP+ 网络接口（设备本地 UI 上的端口 3 和端口 4） 
    - 1 个 Wi-Fi 端口，上面连接了 1 个 Wi-Fi 收发器。

- 前面板上还有一个电源按钮。 

- 背板上有一个电池，还有一个安装到设备上的外壳。 


执行以下步骤，为设备排布电源线和网线。

1. 识别设备前面板上的各种网络和存储组件。

    ![设备上的网络和存储接口](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. 在设备正面左下角找到电源按钮。 

    ![具有设备电源按钮的设备前面板](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. 电池连接到设备的背板上。 找到第二个在电池上的电源按钮。 

    ![具有电池电源按钮的设备前面板](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    将电源线的一端连接到电池，将另一端连接到电源插座。 

    ![电源线连接](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    仅靠电池运行时（电池不连接到电源上），正面的电源开关和电池上的开关都应切换到“开”位置。 当电池连接到电源时，只应将设备正面的电源按钮切换到“开”位置。 

4. 按前面板上的电源按钮来接通设备电源。 
    
    > [!NOTE]
    > 若要打开或关闭设备电源，必须按下电源按钮顶端的黑色按钮，然后将电源按钮切换到“开”或“关”位置。 

5. 若要在此设备上配置 Wi-Fi，请采用以下布线图：

    ![Wi-Fi 布线](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - 将 1-GbE 网络接口的端口 1 连接到用于配置物理设备的计算机。 端口 1 是专用的管理接口。


    如果对此设备采用有线配置，请采用下图：
     
    ![有线网络布线](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - 将 1-GbE 网络接口的端口 1 连接到用于配置物理设备的计算机。 端口 1 是专用的管理接口。
    - 将一个或多个端口 2、端口 3 或端口 4 连接到数据中心网络/Internet。
    
        - 如果连接端口 2，请使用 RJ-45 网线。
        - 对于 10-GbE 网络接口，请使用 SFP+ 铜缆。

## <a name="next-steps"></a>后续步骤

本教程介绍了 Azure Stack Edge 主题，例如如何执行以下操作：

> [!div class="checklist"]
> * 拆开设备包装
> * 为设备布线

请继续学习下一教程，了解如何连接、设置和激活设备。

> [!div class="nextstepaction"]
> [连接并设置 Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
