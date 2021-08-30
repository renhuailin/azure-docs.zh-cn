---
title: 在 Azure Stack Edge Pro GPU 设备上安装更新 | Microsoft Docs
description: 介绍如何使用 Azure 门户和本地 Web UI 为 Azure Stack Edge Pro GPU 设备和设备的 Kubernetes 群集应用更新
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/12/2021
ms.author: alkohli
ms.openlocfilehash: 9f78e3021df56ea750f2c902555aca796691476a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448943"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>更新 Azure Stack Edge Pro GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍通过本地 Web UI 和 Azure 门户在 Azure Stack Edge Pro GPU 上安装更新所需的步骤。 应用软件更新或修补程序，使 Azure Stack Edge Pro 设备和设备上关联的 Kubernetes 群集保持最新。

本文中所述的过程是使用不同版本的软件执行的，但对于当前软件版本，该过程保持不变。

> [!IMPORTANT]
> - 更新 2106 是当前更新且对应于：
>   - 设备软件版本 - 2.2.1636.3457
>   - Kubernetes 服务器版本 - v1.20.2
>   - IoT Edge 版本：0.1.0-beta14
>   - GPU 驱动程序版本：460.32.03
>   - CUDA 版本：11.2
>    
>    有关此更新中新增功能的信息，请转到[发行说明](azure-stack-edge-gpu-2105-release-notes.md)。
> - 若要应用 2105 更新，设备必须正在运行 2010。 如果未运行支持的最低版本，则会看到此错误：“无法安装更新包，因为不满足其依赖项”。
> - 此更新要求按顺序应用两个更新。 首先应用设备软件更新，然后应用 Kubernetes 更新。
> - 请记住，安装更新或修补程序会重新启动设备。 此更新包含设备软件更新和 Kubernetes 更新。 如果 Azure Stack Edge Pro GPU 是一个单节点设备，则任何正在进行的 I/O 都会被中断，并且你的设备将经历长达 1.5 小时的更新停机时间。

若要在设备上安装更新，首先需要配置更新服务器的位置。 配置更新服务器后，可以通过 Azure 门户 UI 或本地 Web UI 应用更新。

后续部分将介绍其中的每个步骤。

## <a name="configure-update-server"></a>配置更新服务器

1. 在本地 Web UI 中，转到“配置” > “更新服务器”。
   
    ![配置更新 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. 在“选择更新服务器类型”的下拉列表中，选择“Microsoft 更新服务器(默认)”或“Windows Server Update Services”。  
   
    如果从 Windows Server Update Services 更新，请指定服务器 URI。 该 URI 处的服务器将在连接到此服务器的所有设备上部署更新。

    ![配置更新 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS 服务器用于通过管理控制台管理和分配更新。 WSUS 服务器还可以作为组织内其他 WSUS 服务器的更新源。 充当更新源的 WSUS 服务器称为上游服务器。 在 WSUS 实现中，网络中必须至少有一台 WSUS 服务器能够连接到 Microsoft 更新来获取可用的更新信息。 作为管理员，你可以根据网络安全和配置来决定多少其他 WSUS 服务器直接连接到 Microsoft 更新。
    
    有关详细信息，请转到 [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

建议通过 Azure 门户安装更新。 设备每天自动扫描一次更新。 当更新可用时，你将在门户中看到通知。 然后可以下载并安装更新。

> [!NOTE]
> 请确保在继续安装更新之前设备运行正常，状态显示 为“设备正在正常运行!”。

1. 当更新可用于设备时，你将看到通知。 选择通知，或从顶部命令栏中选择“更新设备”。 这将允许你应用设备软件更新。

    ![更新后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. 在“设备更新”边栏选项卡中，检查你是否已查看了发行说明中与新功能关联的许可条款。

    你可以选择“下载”并安装更新，也可以只“下载”更新。 然后可以选择以后安装这些更新。

    ![更新 2 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    如果要下载并安装更新，请选中在下载完成后自动安装更新的选项。

    ![更新 3 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. 开始下载更新。 你会看到一条通知，指示正在下载。

    ![更新 4 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    通知横幅也会显示在 Azure 门户中。 这指示下载进度。

    ![更新 5 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    你可以选择此通知或选择“更新设备”以查看更新的详细状态。

    ![更新 6 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. 下载完成后，通知横幅会更新以指示完成。 如果选择下载并安装更新，则将自动开始安装。

    如果选择仅下载更新，则选择该通知以打开“设备更新”边栏选项卡。 选择“安装”。
  
5. 你会看到一条通知，指示正在安装。 门户还会显示信息警报，指示正在安装。 设备脱机且处于维护模式。
   
    ![更新 10 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. 因为这是单节点设备，所以设备在安装更新后会重新启动。 重新启动过程中的关键警报指示设备检测信号丢失。

    ![更新 11 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    选择警报以查看相应的设备事件。
    
    ![更新 12 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. 重新启动后，设备软件将完成更新。 更新完成后，可以从本地 Web UI 验证是否已更新设备软件。 Kubernetes 软件版本尚未更新。

    ![更新 13 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. 你将看到一条通知横幅，指出设备更新可用。 选择此横幅，开始更新设备上的 Kubernetes 软件。 

    ![更新 13a 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![更新 14 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    如果从顶部命令栏中选择“更新设备”，则可以查看更新的进度。  

    ![更新 15 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. 安装更新后，设备状态更新为“设备正在正常运行!”。 

    ![更新 16 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    转到本地 Web UI，然后转到“软件更新”页。 验证是否已成功安装 Kubernetes 更新，以及软件版本是否反映了此情况。

    ![更新 17 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


成功安装设备软件和 Kubernetes 更新后，横幅通知就会消失。 你的设备现在具有最新版本的设备软件和 Kubernetes。


## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

* 下载更新或修补程序
* 安装更新或修补程序

以下部分详细介绍了这些步骤。


### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

若要下载更新，请执行以下步骤。 你可以从 Microsoft 提供的位置或从 Microsoft 更新目录下载更新。


执行以下步骤，从 Microsoft 更新目录下载更新。

1. 启动浏览器并导航到 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)。

    ![搜索目录](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. 在 Microsoft 更新目录的搜索框中，输入要下载的更新的修补程序或条款的知识库 (KB) 编号。 例如，输入“Azure Stack Edge”，然后单击“搜索” 。
   
    更新列表显示为“Azure Stack Edge 更新 2105”。
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. 选择“下载”  。 有两个包可供下载： <!--KB 4616970 and KB 4616971--> 一个用于设备软件更新 (SoftwareUpdatePackage.exe)，另一个用于 Kubernetes 更新 (Kubernetes_Package.exe) 。 将包下载到本地系统的文件夹。 也可以将该文件夹复制到可通过设备访问的网络共享。

### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

安装更新或修补程序之前，请确保：

 - 更新或修补程序已本地下载到主机上，或可通过网络共享访问。
 - 你的设备状态为“正常”，如本地 Web UI 的“概述”页中所示。

   ![更新设备](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

此过程大约需要 20 分钟来完成。 执行以下步骤，安装更新或修补程序。

1. 在本地 Web UI 中，转到“维护” > “软件更新”。 记下运行的软件版本。 
   
   ![更新设备 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. 提供更新文件的路径。 也可以浏览到网络共享上的更新安装文件。 选择包含 SoftwareUpdatePackage.exe 后缀的软件更新文件。

   ![更新设备 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. 选择“应用”。

   ![更新设备 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. 出现确认提示时，选择“是”以继续执行下一步操作。 假定设备是单节点设备，应用更新后，设备将重新启动并且会出现停机。 
   
   ![更新设备 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. 更新开始。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。
   
6. 重新启动完成后，会转到“登录”页。 若要验证设备软件是否已更新，请在本地 Web UI 中，转到“维护” > “软件更新”。 对于当前版本，显示的软件版本应为 Azure Stack Edge 2105。 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. 现在，你将更新 Kubernetes 软件版本。 重复上述步骤。 提供包含 Kubernetes_Package.exe 后缀的 Kubernetes 更新文件的路径。  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. 选择“应用更新”。

   ![更新设备 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. 出现确认提示时，选择“是”以继续执行下一步操作。

10. 成功安装 Kubernetes 更新后，“维护” > “软件更新”中没有对显示的软件所做的更改。


## <a name="next-steps"></a>后续步骤

了解有关[管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md) 的详细信息。
