---
title: 在 Microsoft Azure Stack Edge Pro 设备上从 Azure 市场部署 GPU 模块 | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上部署启用了 GPU 的 IoT 模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568396"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上从 Azure 市场署启用了 GPU 的 IoT 模块

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文介绍如何在 Azure Stack Edge Pro 设备上从 Azure 市场部署启用了图形处理单元 (GPU) 的 IoT Edge 模块。 

在本文中，学习如何：
  - 准备 Azure Stack Edge Pro 以运行 GPU 模块。
  - 从 Azure 市场下载并部署启用了 GPU 的 IoT 模块。
  - 监视模块输出。

## <a name="about-sample-module"></a>关于示例模块

本文中的 GPU 示例模块包括 PyTorch 和 TensorFlow 基准测试示例代码，用于比较 CPU 与 GPU。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

- 你已访问启用了 GPU 的单节点 Azure Stack Edge Pro 设备。 此设备已使用 Azure 中的资源激活。 
- 已在此设备上配置计算。 按照[教程：在 Azure Stack Edge 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)中的步骤操作。
- Windows 客户端上的以下开发资源：
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。   


## <a name="get-module-from-azure-marketplace"></a>从 Azure 市场获取模块

1. 浏览 [Azure 市场中的所有应用](https://azuremarketplace.microsoft.com/marketplace/apps)。

    ![浏览 Azure 市场中的应用](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. 搜索“开始使用 GPU”。

    ![搜索 GPU 示例模块](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. 选择“立即获取”  。

    ![获取示例模块](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. 选择“继续”以确认提供商的使用条款和隐私政策。 

    ![获取示例模块 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. 选择用于部署 Azure Stack Edge Pro 设备的订阅。

    ![选择订阅](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. 输入在配置 Azure Stack Edge Pro 设备时创建的 IoT 中心服务的名称。 若要查找此 IoT 中心服务名称，请在 Azure 门户中转到与设备关联的 Azure Stack Edge 资源。 

    1. 在左窗格菜单选项中，转到“Edge 服务”>“IoT Edge”。 

        ![查看计算配置](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. 转到“属性”。 

        1. 记下在 Azure Stack Edge Pro 设备上配置计算时创建的 IoT 中心服务。
        2. 记下在配置计算时创建的 IoT Edge 设备的名称。 将在后续步骤中使用此名称。

        ![Edge 计算配置](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. 选择“部署到设备”。

11. 输入 IoT Edge 设备的名称，或选择“查找设备”以浏览注册到中心的设备。

    ![查找设备](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. 选择“创建”以继续配置部署清单的标准过程，包括根据需要添加其他模块。 新模块的详细信息（例如映像 URI、创建选项以及所需的属性）已预定义，但可以更改。

    ![选择“创建”](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. 验证该模块是否已部署在 Azure 门户的 IoT 中心。 选择设备，选择”设置模块“，该模块应列在“IoT Edge 模块”部分中 。

    ![选择“创建 2”](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>监视模块  

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 在这种情况下，请选择用于部署 Azure Stack Edge Pro 设备的订阅，并选择为 Azure Stack Edge Pro 设备创建的 IoT Edge 设备。 在前面的步骤中通过 Azure 门户配置计算时，会发生这种情况。

3. 在 VS Code 资源管理器中，展开“Azure IoT 中心”部分。 在“设备”下，应会看到与 Azure Stack Edge Pro 设备对应的 IoT Edge 设备。 

    1. 选择该设备，右键单击，然后选择“开始监视内置事件终结点”。
  
        ![开始监视](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. 转到“设备”>“模块”，应会看到“GPU 模块”正在运行 。

    3. VS Code 终端还应显示 IoT 中心事件，这些事件显示为 Azure Stack Edge Pro 设备的监视输出。

        ![监视输出](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        你可看到 GPU 执行同一组操作（5000 次形状转换迭代）所用时间比 CPU 所用时间要少得多。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[配置 GPU 以使用模块](./azure-stack-edge-gpu-configure-gpu-modules.md)。