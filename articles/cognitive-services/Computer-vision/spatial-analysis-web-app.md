---
title: 部署空间分析 Web 应用
titleSuffix: Azure Cognitive Services
description: 了解如何在 Web 应用程序中使用空间分析。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: ecccbb4e2741cc7f413e9b2076bd3199b00dd9a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752084"
---
# <a name="how-to-deploy-a-spatial-analysis-web-application"></a>如何：部署空间分析 Web 应用程序

使用本文了解如何部署 Web 应用，该应用将从 IotHub 收集空间分析数据并将其可视化。 这可为各种方案和行业提供有用的应用程序。 例如，如果公司想要优化其经营场所空间的使用，他们可以快速创建包含不同方案的解决方案。 

在本教程中，将了解如何：

* 部署空间分析容器
* 配置操作和相机
* 在 Web 应用程序中配置 IoT 中心连接
* 部署并测试 Web 应用程序

此应用将展示以下方案：

* 进出空间/商店的人数
* 进出结帐区域的人数和结帐排队花费的时间（停留时间）
* 戴口罩的人数 
* 违反社交距离准则的人数

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/cognitive-services/)
* 基本了解 Azure IoT Edge 部署配置和 [Azure IoT 中心](../../iot-hub/index.yml)
* 已配置的[主计算机](spatial-analysis-container.md)。

## <a name="deploy-the-spatial-analysis-container"></a>部署空间分析容器

按照[主计算机设置](./spatial-analysis-container.md)来配置主计算机并将 IoT Edge 设备连接到 Azure IoT 中心。 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>在订阅中部署 Azure IoT 中心服务

首先，使用标准定价层 (S1) 或免费层 (S0) 创建 Azure IoT 中心服务实例。 按照这些说明使用 Azure CLI 创建此实例。

填写必需的参数：
* Subscription：Azure 订阅的名称或 ID
* Resource group：为资源组创建一个名称
* Iot Hub Name：为 IoT 中心创建一个名称
* IoTHub Name：所创建的 IoT 中心的名称 
* Edge Device Name：为边缘设备创建一个名称

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>在主计算机上的 Azure IoT Edge 上部署容器

下一步是使用 Azure CLI 将空间分析容器部署为主计算机上的 IoT 模块。 部署过程需要部署清单文件，其中概述了部署所需的容器、变量和配置。 示例部署清单位于 [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json)，其中包含所有方案的预生成配置。  

### <a name="set-environment-variables"></a>设置环境变量

IoT Edge 模块的大部分环境变量已在上述链接的示例 DeploymentManifest.json 文件中进行设置。 在文件中，搜索 `ENDPOINT` 和 `APIKEY` 环境变量，如下所示。 将这些值替换为前面创建的终结点 URI 和 API 密钥。 确保 EULA 值设置为“接受”。 

```json
"EULA": { 
    "value": "accept"
},
"BILLING":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"APIKEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>配置操作参数

如果使用的示例 [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) 已包含所有必需配置（操作、录制的视频文件 URL 和区域等），则可以跳到“执行部署”部分。

完成空间分析容器的初始配置后，下一步是配置操作参数，并将其添加到部署中。 

第一步是更新示例 [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) 并配置所需的操作。 例如，cognitiveservices.vision.spatialanalysis-personcount 的配置如下所示：

```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

更新部署清单后，请按照相机制造商的说明安装相机、配置相机 URL，并配置用户名和密码。 

接下来，将 `VIDEO_URL` 设置为相机的 RTSP URL，并设置用于连接相机的凭据。

如果边缘设备具有多个 GPU，请选择要对其运行此操作的 GPU。 请确保对操作进行负载均衡，即一次在单个 GPU 上运行的操作不超过 8 个。  

接下来，配置要对其进行人员计数的区域。 若要配置区域多边形，请首先按照制造商的说明从相机检索帧。 若要确定多边形的每个顶点，请在帧上选择一个点，取该点相对于帧左上角的 x,y 像素坐标，然后再除以相应的帧尺寸。 将结果设置为顶点的 x,y 坐标。 可以在 `SPACEANALYTICS_CONFIG` 字段中设置区域多边形配置。

这是一个示例视频帧，显示如何为大小为 1920/1080 的帧计算顶点坐标。
![示例视频帧](./media/spatial-analysis/sample-video-frame.jpg)

还可以为何时计入检测到的人员以及何时生成事件选择一个置信度阈值。 如果希望输出所有事件，请将阈值设置为 0。

### <a name="execute-the-deployment"></a>执行部署

部署清单完成后，请在 Azure CLI 中使用此命令，将主计算机上的容器部署为 IoT Edge 模块。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

填写必需的参数：

* IoT Hub Name：Azure IoT 中心名称
* DeploymentManifest.js：部署文件的名称
* IoT Edge device name：主计算机的 IoT Edge 设备名称
* Subscription：你的订阅 ID 或名称

此命令将开始部署，你可以在 Azure 门户的 Azure IoT 中心实例中查看部署状态。 在设备完成下载容器映像并开始运行之前，状态可能显示为“417 – 设备的部署配置未设置”。

### <a name="validate-that-the-deployment-was-successful"></a>验证部署是否成功

在 Azure 门户上 IoT 中心实例的 spatial-analysis 模块的“IoT Edge 模块设置”中，找到“运行时状态”。 “运行时状态”的“所需值”和“报告的值”应为 `Running`。 请参阅下面的内容，了解它在 Azure 门户上的显示状态。

![示例部署验证](./media/spatial-analysis/deployment-verification.png)

此时，空间分析容器正在运行操作。 它为操作发出 AI 见解，并将这些见解作为遥测路由到 Azure IoT 中心实例。 若要配置其他相机，可以更新部署清单文件并再次执行部署。

## <a name="spatial-analysis-web-application"></a>空间分析 Web 应用程序

空间分析 Web 应用程序使开发人员能够快速配置示例 Web 应用、将其托管在 Azure 环境中并使用该应用来验证 E2E 事件。

## <a name="build-docker-image"></a>生成 Docker 映像

按照[指南](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/README.md#docker-image)生成映像，并将映像推送到订阅中的 Azure 容器注册表。

## <a name="setup-steps"></a>设置步骤

若要安装该容器，请创建新的 Azure 应用服务并填写所需的参数。 然后转到“Docker”选项卡，并依次选择“单个容器”和“Azure 容器注册表”。 使用你在其中推送映像的 Azure 容器注册表实例。

![输入映像详细信息](./media/spatial-analysis/solution-app-create-screen.png)

输入以上参数后，单击“查看+创建”并创建应用。

### <a name="configure-the-app"></a>配置应用 

等待安装完成，然后导航到 Azure 门户中的资源。 转到“配置”部分，添加以下两个“应用程序设置”。

* `EventHubConsumerGroup` – Azure IoT 中心中使用者组的字符串名称，可以在 IoT 中心创建新的使用者组，或使用默认组。 
* `IotHubConnectionString` – Azure IoT 中心的连接字符串，可从 Azure IoT 中心资源![配置参数](./media/spatial-analysis/solution-app-config-page.png)的密钥部分检索该字符串

添加这 2 个设置后，单击“保存”。 然后在左侧导航菜单中单击“身份验证/授权”，并将其更新为所需的身份验证级别。 建议更新为 Azure Active Directory (Azure AD) express。 

### <a name="test-the-app"></a>测试应用程序

转到 Azure 服务并验证部署是否成功，以及 Web 应用是否正在运行。 导航到配置的 URL `<yourapp>.azurewebsites.net`，查看正在运行的应用。

![测试部署](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>获取 PersonCount 源代码
若要查看或修改此应用程序的源代码，可[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-spatial-analysis)查找源代码。

## <a name="next-steps"></a>后续步骤

* [配置空间分析操作](./spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [相机放置指南](spatial-analysis-camera-placement.md)
* [区域和线条放置指南](spatial-analysis-zone-line-placement.md)
