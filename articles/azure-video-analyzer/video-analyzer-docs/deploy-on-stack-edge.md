---
title: 在 Azure Stack Edge 上部署 Azure 视频分析器
description: 本文列出了可帮助你在 Azure Stack Edge 上部署 Azure 视频分析器的步骤。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: da14368846cd87d5d4e231933cec0068a4e558f9
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546600"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>在 Azure Stack Edge 上部署 Azure 视频分析器

本文列出了可帮助你在 Azure Stack Edge 上部署视频分析器的步骤。 设置并激活设备后，该设备便可用于视频分析器部署。 

对于视频分析器，我们将通过 IoT 中心进行部署，但 Azure Stack Edge 资源将公开 Kubernetes API，该 API 允许客户部署可与视频分析器通信的其他非 IoT 中心感知解决方案。 

> [!TIP]
> 使用 Kubernetes (K8s) API 进行自定义部署是一种高级案例。 建议客户创建 Edge 模块，并通过 IoT 中心将其部署到每个 Azure Stack Edge 资源，而非使用 Kubernetes API。 本文将介绍使用 IoT 中心部署视频分析器模块的步骤。

## <a name="prerequisites"></a>先决条件

* 视频分析器帐户

    此[云服务](./overview.md)用于注册视频分析器边缘模块，以及播放录制的视频和视频分析
* 托管标识

    这是用户分配的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，用于管理对上述存储帐户的访问。
* [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) 资源
* [IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)
* 存储帐户

    建议使用常规用途 v2 (GPv2) 存储帐户。  
    详细了解[常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)。
* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 确保开发计算机连接到的网络允许基于端口 5671 的高级消息队列协议。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。

## <a name="configuring-azure-stack-edge-for-using-video-analyzer"></a>配置 Azure Stack Edge 以使用视频分析器

Azure Stack Edge 是一个硬件即服务解决方案，也是一台具有网络数据传输功能的支持 AI 的边缘计算设备。 详细了解 [Azure Stack Edge 和详细设置说明](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)。 若要开始，请按照以下链接中的说明进行操作：

* [Azure Stack Edge/Data Box Gateway 资源创建](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)
* [安装和设置](../../databox-online/azure-stack-edge-gpu-deploy-install.md)
* 连接和激活

    1. [“连接”](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
    2. [配置网络](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    3. [配置设备](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
    4. [配置证书](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
    5. [激活](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
* [将 IoT 中心附加到 Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>在 Azure Stack Edge 本地 UI 上启用计算先决条件

在开始之前，请确保：

* 已激活 Azure Stack Edge 资源。
* 可访问运行 PowerShell 5.0 或更高版本的 Windows 客户端系统，以便访问 Azure Stack Edge 资源。
* 若要部署 Kubernetes 群集，需要通过[本地 Web UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) 配置 Azure Stack Edge 资源。 

    * 连接和配置：
    
        1. [“连接”](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
        2. [配置网络](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
        3. [配置设备](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
        4. [配置证书](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
        5. [激活](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
    * 若要启用计算，请在设备的本地 Web UI 中，转到“计算”页面。
    
        * 然后选择要为计算启用的网络接口。 选择“启用”。 启用计算会导致在设备的该网络接口上创建虚拟交换机。
        * 将 Kubernetes 测试节点 IP 和 Kubernetes 外部服务 IP 留空。
        * 选择应用 - 此操作需要大约 2 分钟。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text=" Azure Stack Edge 本地 UI 上的计算先决条件":::

        * 如果没有为 Kubernetes API 和 Azure Stack Edge 资源配置 DNS，可以更新 Windows 主机文件。
        
            * 以管理员身份打开文本编辑器
            * 打开 C:\Windows\System32\drivers\etc\hosts 下的文件
            * 将 Kubernetes API 设备名称的 IPv4 和主机名添加到该文件中。 （可以在 Azure Stack Edge 门户中在“设备”部分找到此信息。）
            * 保存并关闭

### <a name="deploy-video-analyzer-edge-modules-using-azure-portal"></a>使用 Azure 门户部署视频分析器边缘模块

Azure 门户引导你创建部署清单并将部署推送到 IoT Edge 设备。  
#### <a name="select-your-device-and-set-modules"></a>选择设备并设置模块

1. 登录 [Azure 门户](https://ms.portal.azure.com/)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”  。
1. 在设备列表中单击目标设备的 ID。
1. 选择“设置模块”  。

#### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供了一个引导你完成创建部署清单的向导。 该向导将三个步骤整理成多个选项卡：“模块”、“路由”和“查看 + 创建”    。

#### <a name="add-modules"></a>添加模块

1. 在页面的“IoT Edge 模块”部分，单击“添加”下拉列表并选择“IoT Edge 模块”，显示“添加 IoT Edge 模块”页面     。
1. 在“模块设置”选项卡上，提供模块的名称，然后指定容器映像 URI  ：   
    示例:
    
    * IoT Edge 模块名称：avaedge
    * 映像 URI：mcr.microsoft.com/media/video-analyzer:1    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="该屏幕截图显示“模块设置”选项卡":::
    
    > [!TIP]
    > 如本过程所述，在“模块设置”、“容器创建选项”和“模块孪生设置”选项卡上指定值之前，请勿选择“添加”   。
    
    > [!WARNING]
    > 当你调用模块时，Azure IoT Edge 会区分大小写。 请记下用作模块名称的确切字符串。

1. 打开“环境变量”选项卡。
   
   在显示的输入框中添加以下值

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="环境变量":::

1. 打开“容器创建选项”选项卡。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="容器创建选项":::
 
    将以下 JSON 复制并粘贴到框中，从而限制模块生成的日志文件的大小。
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   JSON 中的 "Binds" 部分有 2 个条目：
   1. "/var/lib/videoanalyzer:/var/lib/videoanalyzer"：用于从容器绑定永久性应用程序配置数据并将其存储在边缘设备上。
   1. "/var/media:/var/media"：这将绑定边缘设备和容器之间的媒体文件夹。 用于在运行支持在边缘设备上存储视频剪辑的 pipelineTopology 时存储视频录制内容。
   
1. 在“模块孪生设置”选项卡上，复制以下 JSON 并将其粘贴到框中。
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="孪生设置":::

    Azure 视频分析器需要[模块孪生配置架构](module-twin-configuration-schema.md)中列出的一组必需孪生属性才能运行。  

    需要输入到模块孪生设置编辑框中的 JSON 将如下所示：    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
    下面是一些可添加到 JSON 的其他建议属性，将有助于监视模块。 有关详细信息，请参阅[监视和日志记录](monitor-log-edge.md)：
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. 选择“添加”  

添加 RTSP 模拟器 Edge 模块

1. 在页面的“IoT Edge 模块”部分，单击“添加”下拉列表并选择“IoT Edge 模块”，显示“添加 IoT Edge 模块”页面     。
1. 在“模块设置”选项卡上，提供模块的名称，然后指定容器映像 URI  ：   
    示例:
    
    * IoT Edge 模块名：rtspsim
    * 映像 URI：mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2  


1. 打开“容器创建选项”选项卡。
 
    将以下 JSON 内容复制并粘贴到框中
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. 选择“添加”  

1. 在完成时选择“下一步:路由”继续转到路由部分。 指定路由。

    在“名称”下输入“AVAToHub”，在“值”下输入“FROM /messages/modules/avaedge/outputs/* INTO $upstream” 
1. 选择“下一步: 查看 + 创建”转到“查看”部分。
1. 查看部署信息，然后选择“创建”以部署该模块。

    > [!TIP]
    > 按照以下步骤生成预配令牌：
1. 打开 Azure 门户并转到“视频分析器”
1. 在左侧导航窗格中，单击“边缘模块”。
1. 选择边缘模块，然后单击“生成令牌”按钮：

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="生成令牌" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. 复制预配令牌：

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="复制令牌":::



#### <a name="optional-setup-docker-volume-mounts"></a>（可选）设置 Docker 卷装载

若要查看工作目录中的数据，请在部署之前按照以下步骤设置 Docker 卷装载。 

这些步骤包括创建网关用户并设置文件共享，以查看视频分析器工作目录和视频分析器媒体文件夹的内容。

> [!NOTE]
> 支持绑定装载，但卷装载允许查看数据，如果需要，还可以远程复制数据。 可以同时使用绑定和卷装载，但两者不能指向相同的容器路径。

1. 打开 Azure 门户并转到 Azure Stack Edge 资源。
1. 创建可访问共享的“网关用户”。
    
    1. 在左侧导航窗格中，单击“云存储网关”。
    1. 单击左侧导航窗格中的“用户”。
    1. 单击“+ 添加用户”图标以设置用户名和密码。 （建议：`avauser`）。
    1. 单击“添加”。

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="添加用户":::
1. 创建一个本地共享以持久保存视频分析器。

    1. 单击“云存储网关”->“共享”。
    1. 单击“+ 添加共享”。
    1. 设置共享名称。 （建议：`ava`）。
    1. 将共享类型保留为 SMB。
    1. 请确保选中“将该共享用于 Edge 计算”。
    1. 确保选中“配置为 Edge 本地共享”。
    1. 在“用户详细信息”中，向最近创建的用户授予对共享的访问权限。
    1. 单击“创建”。
            
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="本地共享":::  
    
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
    1. 将存储类型设置为块 Blob。
    1. 设置容器名称。
    1. 在“用户详细信息”中，向最近创建的用户授予对共享的访问权限。
    1. 单击“创建”。    
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="远程共享":::
1. 更新 RTSP 模拟器模块的容器创建选项以使用卷装载：
    1. 单击“设置模块”按钮：

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="设置模块" lightbox="./media/deploy-on-stack-edge/set-modules.png":::
    1. 单击“rtspsim”模块：

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="选择模块":::
    1. 选择“容器创建选项”选项卡并按如下所示添加装载：
    
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="更新模块":::

        ```json
            "createOptions": 
            {
                "HostConfig": 
                {
                    "Mounts": 
                    [
                        {
                            "Target": "/live/mediaServer/media",
                            "Source": "media",
                            "Type": "volume"
                        }
                    ],
                    "PortBindings": {
                        "554/tcp": [
                            {
                            "HostPort": "554"
                            }
                        ]
                    }
                }
            }
        ```
    1. 单击“更新”按钮
    1. 单击“查看并创建”按钮，最后单击“创建”按钮以更新模块 。
    
### <a name="verify-that-the-module-is-running"></a>验证模块是否正在运行

最后一步是确保模块按预期连接并运行。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

若要验证模块是否正在运行，请执行以下操作：

1. 在 Azure 门户中，返回到 Azure Stack Edge 资源
1. 选择“模块”磁贴。 随后会转到“模块”边栏选项卡。  在模块列表中，找到已部署的模块。 所添加模块的运行时状态应为“正在运行”。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="自定义模块" lightbox="./media/deploy-on-stack-edge/running-module.png":::

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

    * 按照文档配置计算机以[访问 Kubernetes 群集](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md)。
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

    如果容器尝试将卷装载到现有目录和非空目录，模块将无法启动。
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

    使用 K8s 部署通过 gRPC 与视频分析器通信的自定义推理解决方案时，需要确保将 pod 部署在与视频分析器模块相同的节点上。

    * 选项 1 - 使用节点相关性和内置节点标签进行归置。

    当前 NodeSelector 自定义配置似乎不是选项，因为用户无权在节点上设置标签。 但根据客户的拓扑和命名约定，他们可能能够使用[内置节点标签](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)。 可将通过视频分析器引用 Azure Stack Edge 资源的 nodeAffinity 节添加到推理 Pod 清单中，以实现共置。
    * 选项 2 - 使用 Pod 相关性进行归置（建议）。

        Kubernetes 支持可在同一节点上计划 Pod 的 [Pod 相关性](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)。 可将引用视频分析器模块的 podAffinity 节添加到推理 Pod 清单以实现共置。

         ```json   
        // Example Video Analyzer module deployment match labels
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

[检测运动并发出事件](detect-motion-emit-events-quickstart.md)
