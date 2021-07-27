---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: f166413507afb9aff814eaddaade099d2e34ae68
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554944"
---
在 Azure Stack Edge 设备上部署 VM 之前，必须先将客户端配置为使用 Azure PowerShell 通过 Azure 资源管理器连接到设备。 有关详细说明，请参阅[连接到 Azure Stack Edge 设备上的 Azure 资源管理器](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md)。

确保可使用以下步骤从客户端访问设备。 能连接到 Azure 资源管理器即表示已完成此配置，现在是验证配置是否成功。 

1. 运行以下命令，验证 Azure 资源管理器通信是否正常运行：     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. 若要调用本地设备 API 进行身份验证，请输入： 

    `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d`

    若要通过 Azure 资源管理器进行连接，请提供用户名 EdgeArmUser 和密码。

1. 如果已为 Kubernetes 配置计算，则可以跳过此步骤。 否则，请执行以下操作，确保已为网络接口启用计算： 

   a. 在本地用户界面上，转到“计算”设置。  
   b. 选择要用于创建虚拟交换机的网络接口。 创建的 VM 将附加到与此端口及关联网络连接的虚拟交换机。 请确保选择与要用于此 VM 的 IP 地址相匹配的网络。  

    ![计算配置网络设置窗格的屏幕截图。](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. 在网络接口上的“启用计算”下，选择“是”。 Azure Stack Edge 将创建一个与该网络接口相对应的虚拟交换机，并对其进行管理。 请勿在此时输入 Kubernetes 的特定 IP。 启用计算可能需要花费几分钟时间。

    > [!NOTE]
    > 如果是创建 GPU VM，请选择已连接到 Internet 的网络接口。 这样将能在设备上安装 GPU 扩展。