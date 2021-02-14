---
title: 使用 IoT Edge 模块在带 GPU 的 Azure Stack Edge Pro 上部署计算工作负载 |Microsoft Docs
description: 了解如何在 Azure Stack Edge Pro GPU 设备上使用预先创建的 IoT Edge 模块运行计算工作负载。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 44265efd4d125bb2701742a490fc883c48f1877b
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550986"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Tutorial:使用 IoT Edge 模块在 Azure Stack Edge Pro GPU 上运行计算工作负载

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教程介绍如何在 Azure Stack Edge Pro GPU 设备上使用 IoT Edge 模块运行计算工作负载。 配置计算后，设备可在将数据发送到 Azure 之前先转换数据。

此过程可能需要大约 10 到 15 分钟才能完成。


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 添加共享
> * 添加计算模块
> * 验证数据转换和传输

 
## <a name="prerequisites"></a>先决条件

在 Azure Stack Edge Pro GPU 设备上设置计算角色之前，请确保：

- 已按照[激活 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中所述的内容激活 Azure Stack Edge Pro 设备。
- 你有一个 IoT Edge 模块并且该模块可以运行你的数据。 在本教程中，我们使用了一个 `filemove2` 模块，该模块将数据从设备上的 Edge 本地共享移动到 Edge 共享，数据将从此处传输到 Azure 存储帐户。


## <a name="configure-compute"></a>配置计算

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>添加共享

对于本教程中的简单部署，需要添加两个共享：一个 Edge 共享，一个 Edge 本地共享。

1. 请执行以下步骤，在设备上添加 Edge 共享：

    1. 在 Azure Stack Edge 资源中，转到“云存储空间网关”>“共享”。
    2. 在命令栏中，选择“+ 添加共享”。
    3. 在“添加共享”边栏选项卡上提供共享名称，然后选择共享类型。
    4. 若要装载 Edge 共享，请选中“将该共享用于 Edge 计算”复选框。
    5. 依次选择“存储帐户”、“存储服务”、某个现有用户、“创建”。  

        ![添加 Edge 共享](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > 若要装载 NFS 共享以进行计算，必须在 NFS 虚拟 IP 地址相同的子网上配置计算网络。 若要详细了解如何配置计算网络，请参阅[在 Azure Stack Edge Pro 上启用计算网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

    现已创建 Edge 共享，并且收到了创建成功的通知。 共享列表可能会更新，但必须等待共享创建完成。

2. 请重复上述所有步骤并选中“配置为 Edge 本地共享”复选框，在设备上添加 Edge 本地共享。 本地共享中的数据将保留在设备上。

    ![添加 Edge 本地共享](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    如果创建了本地 NFS 共享，请使用以下远程同步 (rsync) 命令选项将文件复制到共享：

    `rsync <source file path> < destination file path>`

    有关 `rsync` 命令的详细信息，请参阅 [Rsync 文档](https://www.computerhope.com/unix/rsync.htm)。
 
3. 转到“云存储空间网关”>“共享”，以查看更新的共享列表。

    ![更新的共享列表](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>添加模块

可以添加自定义的或预生成的模块。 设备不附带预建或自定义模块。 若要了解如何创建自定义模块，请访问[为 Azure Stack Edge Pro 设备开发 C# 模块](azure-stack-edge-j-series-create-iot-edge-module.md)。

在此部分中，我们将向[为 Azure Stack Edge Pro 开发 C# 模块](azure-stack-edge-j-series-create-iot-edge-module.md)中创建的 IoT Edge 设备添加一个自定义模块。 此自定义模块从 Edge 设备上的 Edge 本地共享提取文件，并将其移到设备上的 Edge（云）共享。 然后，云共享将文件推送到与该云共享相关联的 Azure 存储帐户。

请执行以下步骤来添加模块：

1. 转到“IoT Edge”>“模块”。 在命令栏中，选择“+ 添加模块”。 

2. 在“添加模块”边栏选项卡中，输入以下值：

    
    |字段  |值  |
    |---------|---------|
    |名称     | 模块的唯一名称。 此模块是一个 Docker 容器，可以部署到与 Azure Stack Edge Pro 关联的 IoT Edge 设备。        |
    |映像 URI     | 模块的对应容器映像的映像 URI。        |
    |需要凭据     | 如果选中此项，则会使用用户名和密码来检索具有匹配 URL 的模块。        |
    |输入共享     | 选择一个输入共享。 在本例中，Edge 本地共享是输入共享。 此处使用的模块将文件从 Edge 本地共享移到 Edge 共享，然后，这些文件将从 Edge 共享上传到云中。        |
    |输出共享     | 选择一个输出共享。 在本例中，Edge 共享是输出共享。        |
    |触发器类型     | 选择“文件”或“计划”。   每当发生文件事件（例如，将文件写入输入共享）时，文件触发器就会激发。 计划的触发器根据定义的计划激发。         |
    |触发器名称     | 触发器的唯一名称。         |
    |环境变量| 可帮助定义运行模块的环境的可选信息。   |

    ![添加和配置模块](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. 选择 **添加** 。 随即会添加该模块。 “IoT Edge”>“模块”页将更新，以指示模块已部署。 所添加模块的运行时状态应为“正在运行”。 

    ![已部署模块](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>验证数据转换和传输

最后一步是确保模块按预期运行并处理数据。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

若要验证模块是否按预期运行和处理数据，请执行以下操作：


1. 在文件资源管理器中，连接到前面创建的 Edge 本地共享和 Edge 共享。 请参阅步骤 

    ![连接到 Edge 本地和 Edge 云共享](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. 将数据添加到本地共享。

    ![已复制到 Edge 本地共享的文件](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   数据将移到云共享。

    ![已移至 Edge 云共享的文件](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   然后，将数据从云共享推送到存储帐户。 可以在门户中使用存储资源管理器或 Azure 存储查看数据。

    ![验证存储帐户中的数据](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
现已完成验证过程。


## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 配置计算
> * 添加共享
> * 添加计算模块
> * 验证数据转换和传输

若要了解如何管理 Azure Stack Edge Pro 设备，请参阅：

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
