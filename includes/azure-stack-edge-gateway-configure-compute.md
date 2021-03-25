---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524802"
---
若要在 Azure Stack Edge Pro 上配置计算，必须通过 Azure 门户创建一个 IoT 中心资源。

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“概述”，然后选择“IoT Edge” 。

   ![开始使用计算](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. 在“启用 IoT Edge 服务”中，选择“添加” 。

   ![配置计算](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. 在“配置 Edge 计算”边栏选项卡上输入以下信息：
   
    |字段  |值  |
    |---------|---------|
    |订阅     |为 IoT 中心资源选择订阅。 可以使用 Azure Stack Edge 资源使用的相同订阅。         |
    |资源组     |为 IoT 中心资源选择资源组。 可以使用 Azure Stack Edge 资源使用的相同资源组。         |
    |IoT 中心     | 选择“新建”或“现有”。  <br> 默认会使用标准层 (S1) 来创建 IoT 资源。 若要使用免费层 IoT 资源，请创建一个资源，然后选择现有的资源。 <br> 在每种情况下，IoT 中心资源都会使用 Azure Stack Edge 资源所用的同一订阅和资源组。     |
    |名称     |接受默认名称或输入 IoT 中心资源的名称。         |

   ![开始使用计算 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. 完成设置后，选择“查看 + 创建”。 查看 IoT 中心资源的设置，然后选择“创建”。

   IoT 中心资源的资源创建需要几分钟时间。 创建资源后，“概述”会指示 IoT Edge 服务现在正在运行。

   ![开始使用计算 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. 若要确认是否已配置 Edge 计算角色，请转到“IoT Edge”>“属性”。

   ![开始使用计算 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。 某个 IoT Edge 运行时也在此 IoT Edge 设备上运行。 目前，只有 Linux 平台适用于你的 IoT Edge 设备。

由于要在后台创建虚拟机和 Kubernetes 群集，因此可能需要 20-30 分钟来配置计算。

在 Azure 门户中成功配置计算后，会有一个 Kubernetes 群集和一名与 IoT 命名空间（由 Azure Stack Edge Pro 控制的系统命名空间）关联的默认用户。
