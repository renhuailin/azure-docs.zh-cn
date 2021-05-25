---
title: 在 Azure Stack Edge 上部署实时视频分析
description: 本文列出了可帮助你在 Azure Stack Edge 上部署实时视频分析的步骤。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: d49167890009d58b21c3678cb89f608bad665abd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730263"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>在 Azure Stack Edge 上部署实时视频分析

本文列出了可帮助你在 Azure Stack Edge 上部署实时视频分析的步骤。 设置并激活设备后，该设备便可用于实时视频分析部署。 

对于实时视频分析，我们将通过 IoT 中心进行部署，但 Azure Stack Edge 资源将公开 Kubernetes API，该 API 允许客户部署可与实时视频分析通信的其他非 IoT 中心感知解决方案。 

> [!TIP]
> 使用 Kubernetes (K8s) API 进行自定义部署是一种高级案例。 建议客户创建 Edge 模块，并通过 IoT 中心将其部署到每个 Azure Stack Edge 资源，而非使用 Kubernetes API。 本文将介绍使用 IoT 中心部署实时视频分析模块的步骤。

## <a name="prerequisites"></a>先决条件

* 你拥有[所有者特权](../../role-based-access-control/built-in-roles.md#owner)的 Azure 订阅。
* [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) 资源
   
* [IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)
* 实时视频分析模块的[服务主体](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal)。

   使用提供 IoT 中心的以下区域之一：美国东部 2、美国中部、美国中北部、日本东部、美国西部 2、美国中西部、加拿大东部、英国南部、法国中部、法国南部、瑞士北部、瑞士西部和日本西部。
* 存储帐户

    建议使用常规用途 v2 (GPv2) 存储帐户。  
    详细了解[常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)。
* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 确保开发计算机连接到的网络允许基于端口 5671 的高级消息队列协议。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>配置 Azure Stack Edge 以使用实时视频分析

Azure Stack Edge 是一个硬件即服务解决方案，也是一台具有网络数据传输功能的支持 AI 的边缘计算设备。 详细了解 [Azure Stack Edge 和详细设置说明](../../databox-online/azure-stack-edge-deploy-prep.md)。 若要开始，请按照以下链接中的说明进行操作：

* [Azure Stack Edge/Data Box Gateway 资源创建](../../databox-online/azure-stack-edge-deploy-prep.md)
* [安装和设置](../../databox-online/azure-stack-edge-deploy-install.md)
* [连接和激活](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [将 IoT 中心附加到 Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>在 Azure Stack Edge 本地 UI 上启用计算先决条件

在开始之前，请确保：

* 已激活 Azure Stack Edge 资源。
* 可访问运行 PowerShell 5.0 或更高版本的 Windows 客户端系统，以便访问 Azure Stack Edge 资源。
* 若要部署 Kubernetes 群集，需要通过[本地 Web UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) 配置 Azure Stack Edge 资源。 
    
    * 若要启用计算，请在设备的本地 Web UI 中，转到“计算”页面。
    
        * 然后选择要为计算启用的网络接口。 选择“启用”。 启用计算会导致在设备的该网络接口上创建虚拟交换机。
        * 将 Kubernetes 测试节点 IP 和 Kubernetes 外部服务 IP 留空。
        * 选择应用 - 此操作需要大约 2 分钟。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Azure Stack Edge 本地 UI 上的计算先决条件":::

        * 如果没有为 Kubernetes API 和 Azure Stack Edge 资源配置 DNS，可以更新 Windows 主机文件。
        
            * 以管理员身份打开文本编辑器
            * 打开 C:\Windows\System32\drivers\etc\hosts 下的文件
            * 将 Kubernetes API 设备名称的 IPv4 和主机名添加到该文件中。 （可以在 Azure Stack Edge 门户中在“设备”部分找到此信息。）
            * 保存并关闭

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>使用 Azure 门户部署实时视频分析 Edge 模块

为此，我们只需执行[通过 IoT 中心部署实时视频分析](deploy-iot-edge-device.md)的特定步骤。

1. 跳过用户和组创建步骤，转到[部署实时视频分析 Edge](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) 模块。 遵循此处所述的步骤。
1. 在容器创建选项中，无需设置环境变量。 因此，请跳过此步骤。
1. 打开“容器创建选项”选项卡。

   * 将以下 JSON 复制并粘贴到框中，从而限制模块生成的日志文件的大小。
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > 如果使用 gRPC 协议进行共享内存传输，可以使用主机 IPC 模式在实时视频分析和推理解决方案之间提供共享内存访问权限。
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON 中的“Binds”部分有 2 个条目。 上述“Binds”部分中提到的目录将由 LVA 自动创建。  
        可以根据需要随意更新边缘设备绑定，但如果这样做，请确保这些目录存在于设备上。
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices"：这用于从容器绑定永久性应用程序配置数据并将其存储在边缘设备上。
    * "/var/media:/var/media"：这将绑定边缘设备和容器之间的媒体文件夹。 这用于在运行支持在边缘设备上存储视频剪辑的媒体图拓扑时存储视频录制。
        
1. 继续执行文档中的步骤，并填充“模块孪生”设置。
1. 选择“下一步: 路由”继续转到路由部分。 指定路由。

    保留默认路由并选择“下一步:  查看 + 创建”继续转到查看部分。
1. [查看并验证部署](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>（可选）设置 Docker 卷装载

若要查看工作目录中的数据，请在部署之前按照以下步骤设置 Docker 卷装载。 

这些步骤介绍了如何创建网关用户并设置文件共享，以查看实时视频分析工作目录和实时视频分析媒体文件夹的内容。

> [!NOTE]
> 支持绑定装载，但卷装载允许查看数据，如果需要，还可以远程复制数据。 可以同时使用绑定和卷装载，但两者不能指向相同的容器路径。

1. 打开 Azure 门户并转到 Azure Stack Edge 资源。
1. 创建可访问共享的“网关用户”。
    
    1. 在左侧导航窗格中，单击“云存储网关”。
    1. 单击左侧导航窗格中的“用户”。
    1. 单击“+ 添加用户”图标以设置用户名和密码。 （建议：`lvauser`）。
    1. 单击“添加”。
    
1. 创建“本地共享”以实现实时视频分析持久性。

    1. 单击“云存储网关”->“共享”。
    1. 单击“+ 添加共享”。
    1. 设置共享名称。 （建议：`lva`）。
    1. 将共享类型保留为 SMB。
    1. 请确保选中“将该共享用于 Edge 计算”。
    1. 确保选中“配置为 Edge 本地共享”。
    1. 在“用户详细信息”中，向最近创建的用户授予对共享的访问权限。
    1. 单击“创建”。
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="本地共享":::  

    > [!TIP]
    > 使用连接到 Azure Stack Edge 的 Windows 客户端，按照[本文档中所述](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)的步骤连接到 SMB 共享。    

1. 为文件同步存储创建远程共享。

    1. 首先，在同一区域中创建 blob 存储帐户，方法是单击“云存储网关”->“存储帐户”。
    1. 单击“云存储网关”->“共享”。
    1. 单击“+ 添加共享”。
    1. 设置共享名称。 （建议：media）。
    1. 将共享类型保留为 SMB。
    1. 请确保选中“将该共享用于 Edge 计算”。
    1. 请确保未选中“配置为 Edge 本地共享”。
    1. 选择最近创建的存储帐户。
    1. 设置容器名称。
    1. 将存储类型设置为块 Blob。
    1. 在“用户详细信息”中，向最近创建的用户授予对共享的访问权限。
    1. 单击“创建”。    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="远程共享":::
    
    
1. 更新实时视频分析 Edge 模块的容器创建选项（请参阅[添加模块文档](deploy-iot-edge-device.md#add-modules)中的第 4 点）以使用卷装载。

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>验证模块是否正在运行

最后一步是确保模块按预期连接并运行。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

若要验证模块是否正在运行，请执行以下操作：

1. 在 Azure 门户中，返回到 Azure Stack Edge 资源
1. 选择“模块”磁贴。 随后会转到“模块”边栏选项卡。  在模块列表中，找到已部署的模块。 所添加模块的运行时状态应为“正在运行”。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="自定义模块":::

### <a name="configure-the-azure-iot-tools-extension"></a>配置 Azure IoT Tools 扩展

按照以下说明使用 Azure IoT Tools 扩展连接到 IoT 中心。

1. 在 Visual Studio Code 中，选择“视图”>“资源管理器”。 或是选择 Ctrl+Shift+E。
1. 在“资源管理器”选项卡的左下角，选择“Azure IoT 中心”。
1. 选择“更多选项”图标以查看上下文菜单。 然后选择“设置 IoT 中心连接字符串”。
1. 输入框出现时，在其中输入 IoT 中心连接字符串。 

   * 若要获取连接字符串，请转到 Azure 门户中的 IoT 中心，然后单击左侧导航窗格中的“共享访问策略”。
   * 单击 iothubowner 以获取共享访问密钥。
   * 复制连接字符串 - 主密钥，然后粘贴到 VSCode 上的输入框。

   连接字符串将如下所示：<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   如果连接成功，边缘设备列表随即显示。 此时应看到 Azure Stack Edge。 现在你可以管理 IoT Edge 设备，并通过上下文菜单与 Azure IoT 中心进行交互。 若要查看部署在边缘设备上的模块，请在 Azure Stack 设备下，展开“模块”节点。
    
## <a name="troubleshooting"></a>疑难解答

* Kubernetes API 访问权限 (kubectl)

    * 按照文档配置计算机以[访问 Kubernetes 群集](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-create-kubernetes-cluster)。
    * 所有已部署的 IoT Edge 模块都使用 `iotedge` 命名空间。 使用 kubectl 时，请确保包含该命名空间。  

* 模块日志

    `iotedge` 工具无法访问以获取日志。 必须使用 [kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) 来查看日志或通过管道传递到文件。 示例： <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* Pod 和节点指标

    使用 [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) 可查看 pod 和节点指标。
    <br/>`kubectl top pods -n iotedge` 

* 模块网络   
对于 Azure Stack Edge 上的模块发现，模块的主机端口绑定必须在 createOptions 中。 然后，就可以通过 `moduleName:hostport` 对该模块进行寻址。
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* 卷装载

    如果容器尝试将卷装载到现有的和非空目录，模块将无法启动。

* 使用 gRPC 时的共享内存

    任何命名空间中的 pod 之间支持通过主机 IPC 使用 Azure Stack Edge 资源上的共享内存。
    在 Edge 模块上配置共享内存以通过 IoT 中心进行部署。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* （高级）Pod 归置

    使用 K8s 部署通过 gRPC 与实时视频分析通信的自定义推理解决方案时，需要确保将 pod 部署在与实时视频分析模块相同的节点上。

    * 选项 1 - 使用节点相关性和内置节点标签进行归置。

    当前 NodeSelector 自定义配置似乎不是选项，因为用户无权在节点上设置标签。 但根据客户的拓扑和命名约定，他们可能能够使用[内置节点标签](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)。 通过实时视频分析来引用 Azure Stack Edge 资源的 nodeAffinity 部分可以添加到推理 pod 清单中，以实现归置。
    * 选项 2 - 使用 Pod 相关性进行归置（建议）。
Kubernetes 支持 [Pod 相关性](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)，可以在同一节点上计划 Pod。 可以将引用实时视频分析模块的 podAffinity 部分添加到推理 pod 清单以实现归置。

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```
* 使用 `rtspsim` 模块时的 404 错误代码  
该容器只从容器内的一个文件夹读取视频。 如果将外部文件夹映射/绑定到容器映像中已存在的文件夹，Docker 将隐藏容器映像中的文件。  
 
    例如，如果没有绑定，容器可能会包含以下文件：  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    主机可能包含以下文件：
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    但在部署清单文件中添加以下绑定时，Docker 将覆盖 /live/mediaServer/media 的内容以匹配主机上的内容。
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>后续步骤

可以通过调用直接方法来使用该模块分析实时视频流。 在模块上[调用直接方法](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)。