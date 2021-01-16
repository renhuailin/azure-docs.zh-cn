---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 56fc24966fa60c3a5e91f92b57332ae2f6a525ff
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256538"
---
必须先将客户端配置为通过 Azure 资源管理器通过 Azure PowerShell 连接到设备，然后才能在 Azure Stack Edge 设备上部署 Vm。 有关详细步骤，请参阅 [连接到 Azure Stack Edge 设备上的 Azure 资源管理器](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)。


请确保以下步骤可用于从客户端访问设备 (在连接到 Azure 资源管理器时已完成此配置，只是验证配置是否成功。 ) ： 

1. 验证 Azure 资源管理器通信是否正常工作。 类型：     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. 调用本地设备 Api 进行身份验证。 类型： 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    提供用户名 *EdgeARMuser* 和密码，以便通过 Azure 资源管理器进行连接。

1. 如果已为 Kubernetes 配置 **计算** ，则可以跳过此步骤。 继续操作以确保已为计算启用了网络接口。 在本地 UI 中，请参阅 **计算** 设置。 选择将用于创建虚拟交换机的网络接口。 你创建的 Vm 将附加到连接到此端口和关联网络的虚拟交换机。 请确保选择与要用于 VM 的 IP 地址匹配的网络。  

    ![启用计算设置1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    在网络接口上启用计算。 Azure Stack 边缘将创建和管理对应于该网络接口的虚拟交换机。 目前不要为 Kubernetes 输入特定的 Ip。 启用计算可能需要几分钟时间。

    > [!NOTE]
    > 如果创建 GPU Vm，请选择连接到 Internet 的网络接口。 这允许您在设备上安装 GPU 扩展。


