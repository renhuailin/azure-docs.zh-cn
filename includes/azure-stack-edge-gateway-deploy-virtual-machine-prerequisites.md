---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730606"
---
必须先将客户端配置为通过 Azure 资源管理器通过 Azure PowerShell 连接到设备，然后才能在 Azure Stack Edge 设备上部署 Vm。 有关详细说明，请参阅 [连接到 Azure Stack Edge 设备上的 Azure 资源管理器](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)。

请确保可以使用以下步骤从客户端访问设备。 已连接到 Azure 资源管理器时已完成此配置，现在正在验证配置是否成功。 

1. 运行以下命令，验证 Azure 资源管理器通信是否正常运行：     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. 若要调用本地设备 Api 进行身份验证，请输入： 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    若要通过 Azure 资源管理器进行连接，请提供用户名 *EdgeARMuser* 和密码。

1. 如果已为 Kubernetes 配置计算，则可以跳过此步骤。 否则，请执行以下操作，以确保已为计算启用了网络接口： 

   a. 在本地用户界面上，请参阅 **计算** 设置。  
   b. 选择要用于创建虚拟交换机的网络接口。 你创建的 Vm 将附加到连接到此端口和关联网络的虚拟交换机。 请确保选择与要用于 VM 的 IP 地址匹配的网络。  

    !["计算配置网络设置" 窗格的屏幕截图。](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. 在 "对网络接口 **启用计算** " 下，选择 **"是"**。 Azure Stack 边缘将创建和管理对应于该网络接口的虚拟交换机。 请不要在此时间输入 Kubernetes 的特定 Ip。 启用计算可能需要几分钟时间。

    > [!NOTE]
    > 如果要创建 GPU Vm，请选择连接到 internet 的网络接口。 这样做使你能够在设备上安装 GPU 扩展。


