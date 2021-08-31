---
title: 从实时视频分析迁移到 Azure 视频分析器
description: 了解如何从实时视频分析迁移到 Azure 视频分析器。
ms.topic: how-to
ms.date: 08/16/2021
ms.openlocfilehash: a2225e25685bb9177851379fd86b59c986ff28f0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326314"
---
# <a name="how-to-migrate-from-live-video-analytics-to-azure-video-analyzer"></a>如何从实时视频分析迁移到 Azure 视频分析器

如果你已将实时视频分析边缘模块部署到 IoT Edge 设备，并且当前正在运行媒体图以处理来自实时流式处理协议 (RTSP) 摄像头的源，则应使用本文。

如果你使用的不是 RTSP 摄像头，并在使用某篇[实时视频分析快速入门或教程](../live-video-analytics-edge/overview.md)，则只需切换到相应的 [Azure 视频分析器快速入门或教程](../../azure-video-analyzer/video-analyzer-docs/overview.md)即可。

以下说明适用于将单个 IoT Edge 设备的实时视频分析迁移到 Azure 视频分析器。 需要对每个 IoT Edge 设备重复这些步骤。

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅。

   > [!NOTE]
   > 需要一个 Azure 订阅，在该订阅中，你可以访问资源组的 [参与者](../../role-based-access-control/built-in-roles.md)角色和 [用户访问管理员](../../role-based-access-control/built-in-roles.md)角色，你将在该资源组下创建新资源（用户分配的托管标识、存储帐户、视频分析器帐户）。 如果你没有适当的权限，请联系帐户管理员，让其授予这些权限。 

* 需要对安装了实时视频分析的 IoT Edge 设备拥有管理特权。

## <a name="create-a-backup-of-current-settings"></a>创建当前设置的备份

在开始迁移之前，强烈建议保存边缘部署清单和实时视频分析媒体图配置。

> [!NOTE]
> 下面列出的步骤无法检索摄像头 RTSP 凭据和流 URL 详细信息。  需要直接从摄像头的管理应用程序检索这些信息。

### <a name="save-the-current-deployment-manifest"></a>保存当前部署清单

1. 登录到 Azure 门户。

1. 转到“IoT 中心”，在“自动设备管理”下选择“IoT Edge”  。

1. 在“IoT Edge”下，选择要迁移到 Azure 视频分析器的 IoT Edge 设备。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="该屏幕截图显示 IoT Edge 设备。":::

1. 依次选择“设置模块”、“查看 + 创建” 

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="该屏幕截图显示部署清单。":::

1. 复制窗口的“部署”部分中的值，并将其保存到安全的位置。

1. 选择门户右上角的“X”。  

    > [!NOTE]
    > 不要选择“创建”。  查看部署清单，在 `modules` 下找到 `lvaEdge` 节点。  如果 `lvaEdge` 节点下有一个 `env` 节点，请记下 LOCAL_EDGE_USER 和 lOCAL_GROUP_ID 的值，因为稍后在文档中会用到这些值。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="该屏幕截图显示本地边缘用户和组的部署清单以及 env 节。":::

### <a name="save-the-live-video-analytics-topologies"></a>保存实时视频分析拓扑

1. 若要保存实时视频分析媒体图，请在 IoT Edge 设备下选择实时视频分析模块。  在以下示例中，容器名称为 lvaEdge。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="该屏幕截图显示实时视频分析 IoT Edge 模块容器名称。":::

1. 选择“直接方法”。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="该屏幕截图显示实时视频分析的直接方法调用。":::

1. 在“方法名称”字段中输入“GraphTopologyList”，在有效负载中输入以下代码：

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="该屏幕截图显示 IoT Edge 直接方法调用。":::

1. 选择“调用方法”。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="该屏幕截图显示调用方法。":::

1. 返回的结果将显示在“结果”部分。  这是实时视频分析图拓扑。  复制该 JSON 并将其保存到安全的位置。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="该屏幕截图显示 GraphTopologyList 方法的结果。":::

1. 重复该过程（但请将 `GraphTopologyList` 替换为 `GraphInstanceList`），并保存“结果”部分中的值。  这是图实例的列表。 请记下所有活动实例的名称。

1.  创建实时视频分析设置的备份副本后，可以通过在“方法名称”中输入“GraphInstanceDeactivate”并输入以下代码，来停用所有活动媒体图 ：

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > 请将 Graph_Name 替换为新结果中的活动实例名称。  重复此过程，直到停用了所有活动图。 

## <a name="azure-video-analyzer-device-preparation"></a>Azure 视频分析器：设备准备 

若要开始迁移，需要将 IoT Edge 设备配置为运行 Azure 视频分析器模块。 应使用非特权本地用户帐户在 IoT Edge 设备上运行 Azure 视频分析器模块。 该模块需要创建某些本地文件夹来存储应用程序配置数据。 RTSP 摄像头模拟器模块需要通过视频文件来合成实时视频源。 

1. 通过 SSH 登录到 IoT Edge 设备。

    > [!Note]
    > 根据 IoT Edge 设备清单文件的检查结果（“创建当前设置的备份”部分中的步骤 6），需要在 IoT Edge 设备上创建新的用户/组，或重复使用已在实时视频分析下创建的用户/组。

    如果 IoT Edge 设备上已经为实时视频分析配置了本地用户和组 ID（在部署清单中可以找到），则可以跳过步骤 1，但请记下用户和组的名称与值，因为稍后在本文档需要用到。  如果你还为实时视频分析部署使用了唯一的文件夹结构（在部署清单中可以找到），请将 videoanalyzer 替换为这些值，并省略步骤 2 中的前两行。  

    以下示例演示了实时视频分析部署的唯一文件夹结构。  请注意，applicationDataDirectory 是 /var/lib/videofiles。 对于此示例，已在 IoT Edge 设备上为实时视频分析创建了 localedgeuser 和 localedgegroup 。

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    然后运行以下代码：

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. 运行以下代码，在 IoT Edge 设备上创建本地用户帐户：

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. 使用以下代码创建 avaedge 文件夹结构：

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## <a name="create-azure-resources"></a>创建 Azure 资源

下一步是创建所需的 Azure 资源。 以下说明已在[“创建 Azure 资源”部分](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md)中阐述，但为了方便起见，下面再重复一遍。 

现在请创建所需的 Azure 资源（视频分析器帐户、存储帐户和用户分配的托管标识）。 

创建 Azure 视频分析器帐户时，必须将一个 Azure 存储帐户与其关联。 如果使用视频分析器录制某个摄像机上的实时视频，则该数据将以 Blob 形式存储在存储帐户的容器中。 必须使用托管标识向视频分析器帐户授予对存储帐户的适当访问权限。 

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>在 Azure 门户中创建视频分析器帐户

1. 登录  [Azure 门户](https://portal.azure.com/)。 

1. 在搜索框中，输入“视频分析器” **** 。 

1. 在“服务”下选择“视频分析器”。 ****   **** 

1. 选择“添加” ****。 

1. 在“创建视频分析器帐户”部分，输入以下所需值： ****   

   - 订阅：选择用于创建视频分析器帐户的订阅。 

   - 资源组：选择要在其中创建视频分析器帐户的资源组，或者选择“新建”以创建一个资源组。 ****   

   - 视频分析器帐户名称：输入视频分析器帐户的名称。 该名称必须由全小写字母或数字构成（不含空格），长度为 3 到 24 个字符。 

   - 位置：选择要部署视频分析器帐户的位置（例如“美国西部 2”） **** 。 

   - 存储帐户：创建一个存储帐户。 建议选择 [标准常规用途 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) 存储帐户。 

      > [!NOTE]
      > 如果存储帐户位于要将 Azure 视频分析器帐户部署到的区域，则你可以选择使用该存储帐户，而不是创建新帐户。 

   - 用户标识：创建新的用户分配的托管标识并为其命名。 

1. 在窗体底部，选中以下陈述旁边的框：“我保证，我已获得适用的法律要求的处理媒体内容所需的所有必要权限和同意，并同意遵循所有适用的...” 

1. 在窗体底部选择“查看 + 创建”。 ****   

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="该屏幕截图显示如何在 Azure 门户中创建视频分析器帐户。":::

   > [!NOTE]
   > 这些 Azure 资源将添加到资源组。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="该屏幕截图显示还要将资源添加到 Azure 资源组。":::

## <a name="deploy-the-azure-video-analyzer-edge-module"></a>部署 Azure 视频分析器边缘模块

### <a name="generate-azure-video-analyzer-edge-module-token"></a>生成 Azure 视频分析器边缘模块令牌

1. 转到你的视频分析器帐户。

1. 在“边缘”窗格中选择“边缘模块” 。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="该屏幕截图显示如何创建 Azure 视频分析器帐户边缘模块。":::

1. 选择“添加边缘模块”，输入“avaedge”作为新边缘模块的名称，然后选择“添加”  。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="该屏幕截图显示如何添加边缘模块。":::

1. 右侧将显示“复制预配令牌”窗格。 复制“推荐用于 IoT 模块部署的所需属性”下的代码片段。  在稍后的步骤中需要用到此代码片段。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="该屏幕截图显示预配令牌窗格。":::

### <a name="deploy-the-azure-video-analyzer-edge-module"></a>部署 Azure 视频分析器边缘模块

1.  转到你的 Azure IoT 中心。

1. 在“自动设备管理”下，选择“选择 IoT Edge” 。

1. 选择 IoT Edge 设备的“设备 ID”值。

1. 选择“设置模块”。

1. 选择“添加”，然后从下拉菜单中选择“IoT Edge 模块市场” 。

1. 在搜索字段中输入“Azure 视频分析器”。

1. 选择“Azure 视频分析器”。

1. 在“设置模块”窗格中，选择“AzureVideoAnalyzerEdge” 。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="该屏幕截图显示如何添加 IoT Edge 中的 Azure 视频分析器模块。":::

1. 在“IoT Edge 模块名称”字段中，输入“avaedge”。
1. 选择“环境变量”。 

   > [!NOTE]
   > 如果实时视频分析部署使用了“创建当前设置的备份”部分的步骤 6 中所示的本地用户和组，请在以下步骤中使用这些值，而不要使用 LOCAL_USER_ID 和 LOCAL_GROUP_ID。

1. 在“名称”字段中输入“LOCAL_USER_ID”，在“值”字段中输入“1010” 。  在下一个“名称”字段中输入“LOCAL_GROUP_ID”，在“值”字段中输入“1010” 。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="该屏幕截图显示 Azure 视频分析器环境变量。":::

1. 选择“容器创建选项”并粘贴以下代码：

      ```json
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
                  "/var/media/:/var/media/",
                  "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
         }
      }
      ```

1. 选择“模块孪生设置”，并粘贴前面从视频分析器帐户中的“复制预配令牌”页复制的代码片段。 ****   **** 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>` 是前面在“生成 Azure 视频分析器边缘模块令牌”部分的步骤 4 中捕获的预配令牌的占位符。

1. 选择“更新”。

1. 选择“路由” **** 。
1. 在“名称”下，输入“AVAToHub” ****  **** 。 在“值”下，输入“FROM /messages/modules/avaedge/outputs/* INTO $upstream” ****  ____ 。 
1. 选择 LVAToHub 路由右侧的垃圾桶图标将其删除。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="该屏幕截图显示如何删除实时视频分析 IoT Edge 路由。":::

1. 在“模块”下找到“lvaEdge”模块，然后选择其右侧的垃圾桶图标将其删除。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="该屏幕截图显示如何删除实时视频分析 IoT Edge 模块。":::

1. 选择“查看 + 创建”，然后选择“创建”以部署 avaedge 边缘模块 ****  ****  **** 。 

### <a name="verify-your-deployment"></a>验证部署

在设备详细信息页上，确认 avaedge 模块是否已列为“在部署中指定”和“由设备报告”。 ****    ****   **** 

可能需要花费片刻时间，模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。 状态代码“200 -- 正常”表示  [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)工作正常。 ****    

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="该屏幕截图显示 IoT Edge 运行时报告 200 状态。":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="该屏幕截图显示 IoT Edge 运行时报告所有模块正在运行。":::

## <a name="convert-media-graph-topologies-to-azure-video-analyzer-pipelines"></a>将媒体图拓扑转换为 Azure 视频分析器管道

实时视频分析使用的媒体图拓扑与 Azure 视频分析器使用的管道拓扑之间存在一些差异。  请使用下表来比较实时视频分析拓扑和 Azure 视频分析器拓扑。

| 场景                                                     | 媒体图（实时视频分析）                                            | 管道（Azure 视频分析器）                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 使用多个 AI 模型记录相关对象         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| 视频和音频                                              | [audio-video]()                                               | [audio-video]()                                               |
| 连续视频录制                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| 使用 gRPC 扩展进行连续视频录制和推理 | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| 使用 HTTP 扩展进行连续视频录制和推理 | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| 使用运动检测进行连续视频录制             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| 基于外部 AI 的事件向 [Azure 视频分析器视频/资产] 进行基于事件的视频录制 | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sinkhttps://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| 基于外部 AI 的事件向 [Azure 视频分析器视频/资产] 进行基于事件的视频录制 | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| 基于外部推理引擎检测到的特定对象向 [资产/视频接收器] 进行基于事件的视频录制 | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| 基于通过 IoT Edge 中心发送的消息向文件进行基于事件的视频录制 | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| 基于运动事件向本地文件进行基于事件的视频录制 | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| 基于运动事件向 [资产/视频接收器] 和本地文件进行基于事件的视频录制 | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| 使用 gRPC 扩展分析实时视频，以将图像发送到 Intel 的 OpenVINO(TM) DL Streamer - Edge AI Extension 模块 | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| 使用 HTTP 扩展分析实时视频，以将图像发送到外部推理引擎。 | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| 使用 HTTP 扩展分析实时视频，以将图像发送到 Intel 的 OpenVINO™ Model Server – AI Extension 模块 | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| 使用用于空间分析的计算机视觉分析实时视频 | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> 可以在 GitHub 上的“示例”部分找到[实时视频分析媒体图拓扑](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies)和 [Azure 视频分析器管道拓扑](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies)并对其进行比较。 通过查看这些示例，可将现有的媒体图拓扑更新为 Azure 视频分析器管道拓扑。

### <a name="set-and-activate-the-pipeline-topology"></a>设置并激活管道拓扑

将媒体图拓扑（实时视频分析）转换为管道拓扑（Azure 视频分析器）后，需要在 avaedge 模块上设置管道。

1. 若要设置拓扑，请在 IoT Edge 设备下选择 Azure 视频分析器边缘模块。  在以下示例中，容器名称为 avaedge。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="该屏幕截图显示实时视频分析 IoT Edge 模块。":::

1. 选择“直接方法”。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="该屏幕截图显示实时视频分析的直接方法调用。":::

1. 在“方法名称”中输入“pipelineTopologySet”，在有效负载中输入转换后的管道 JSON 。

1. 选择“调用方法”。

      在返回结果字段中，应会看到“status: 201”后接你的拓扑。  此状态指示已创建了新拓扑。

1. 接下来，我们需要使用该拓扑创建实时管道。  执行步骤 1-3，不过，这一次需要在“方法名称”字段中输入“livePipelineSet”，在有效负载字段中输入以下代码：

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > 请将 `<live pipeline name>`、`<Name of the set topology>`、`<RTSP camera string>`、`<Camera User Password>` 值替换为适用于你环境的值。 

1. 选择“调用方法”。
   
      在返回结果字段中，应会看到“status: 201”后接你的实时管道。  此状态指示已创建新管道。

2. 最后，执行步骤 1-3 激活实时管道，不过，这一次需要在“方法名称”中输入“livePipelineActivate”，在有效负载字段中输入以下代码 ：

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > 请将 `<live pipeline name>` 替换为前面设置的实时管道名称。

4. 选择“调用方法”。
   
   在返回结果字段，应会看到“status: 200”。  此状态指示已激活新管道。

## <a name="direct-methods"></a>直接方法

实时视频分析和 Azure 视频分析器都使用直接方法调用。  在迁移过程中还请注意，直接方法已更新，需要检查它们是否适用于在实时视频分析解决方案中使用它们的任何工作流。  下表显示了[实时视频分析直接方法调用](direct-methods.md)和 [Azure 视频分析器直接方法调用](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md)的差异。

| 实时视频分析 | Azure 视频分析器 |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## <a name="miscellaneous-changes"></a>其他更改 

- Appsettings.json：更新 appsettings.json 文件，使 moduleId 引用你的模块。 也就是说，应将其设置为 avaeEdge。 

- [gRPC 协定](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)发生了细微的更改。 虽然现有的 gRPC 扩展应该仍可正常工作，但我们建议更新原型文件。 可通过 inferencing.proto 中的新行 string sequence_id=14 进行这种更改。

## <a name="next-steps"></a>后续步骤

- 阅读 [有关在检测到运动时将视频录制到云中的快速入门](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md)。 
- 阅读 [有关分析实时视频的快速入门](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md)。 
- 查看 [Azure 视频分析器术语](../../azure-video-analyzer/video-analyzer-docs/terminology.md)。 
- 查看 [Azure 视频分析器管道](../../azure-video-analyzer/video-analyzer-docs/pipeline.md)。 