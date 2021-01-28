---
title: 在 IoT Edge 上运行异常探测器
titleSuffix: Azure Cognitive Services
description: 将异常探测器模块部署到 IoT Edge。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936270"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>将异常探测器模块部署到 IoT Edge

了解如何将认知服务 [异常探测器](../anomaly-detector-container-howto.md) 模块部署到 IoT Edge 设备。 在将其部署到 IoT Edge 后，模块将与其他模块一起在 IoT Edge 中与容器实例一起运行。 它公开了与在标准 docker 容器环境中运行的异常探测器容器实例完全相同的 Api。 

## <a name="prerequisites"></a>必备条件

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。
* 安装 [Azure CLI](/cli/azure/install-azure-cli)。
* [IoT 中心](../../../iot-hub/iot-hub-create-through-portal.md)和[IoT Edge](../../../iot-edge/quickstart-linux.md)设备。

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>将异常情况检测模块部署到边缘

1. 在 Azure 门户中，在 **IoT Edge 上输入异常探测器** ，并打开 Azure Marketplace 结果。
2. 这会转到 Azure 门户的 " [IoT Edge 模块" 的目标设备](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector)。 提供下列必需信息。

    1. 选择订阅。

    1. 选择 IoT 中心。

    1. 选择 " **查找设备** " 并查找 IoT Edge 设备。

3. 选择“创建”按钮。

4. 选择 **AnomalyDetectoronIoTEdge** 模块。

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="带有 AnomalyDetectoronIoTEdge 链接的 IoT Edge 模块用户界面的图像以红色框突出显示，指示这是要选择的项。":::

5. 导航到“环境变量”并提供以下信息。

    1.  对于 **Eula**，保留值 "接受"。

    1. 对于“Billing”，填写你的认知服务终结点。

    1. 对于“ApiKey”，填写你的认知服务 API 密钥。

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="环境变量周围有红色方框，需要为终结点和 API 密钥填写值的区域":::

6. 选择“更新”

7. 选择 " **下一步"：** 用于定义路由的路由。 将来自所有模块的所有消息定义为传递到 Azure IoT 中心。

8. 选择“下一步: 审阅 + 创建”。 可以预览用于定义部署到 IoT Edge 设备的所有模块的 JSON 文件。
    
9. 选择“创建”，启动模块部署。

10. 完成模块部署后，你将返回到 IoT 中心的“IoT Edge”页。 从 IoT Edge 设备列表中选择你的设备，以查看其详细信息。

11. 向下滚动并查看列出的模块。 检查新模块的运行时状态是否为 "正在运行"。 

若要解决 IoT Edge 设备的运行时状态，请参阅 [故障排除指南](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>在 IoT Edge 设备上测试异常探测器

你将对运行 Azure 认知服务容器的 Azure IoT Edge 设备发出 HTTP 调用。 容器提供基于 REST 的终结点 Api。 `http://<your-edge-device-ipaddress>:5000`对于模块 api，请使用主机。

如果边缘设备不允许端口5000上的入站通信，则需要创建新的 **入站端口规则**。 

对于 Azure VM，此设置可在 "**虚拟机** 设置" "网络入站端口规则" "  >    >    >    >  **添加入站端口规则**" 下设置。

可以通过多种方式来验证模块是否正在运行。 找到相关 *外围设备的外部 IP* 地址和公开端口，并打开你喜欢的 web 浏览器。 使用以下各种请求 URL 验证容器是否正在运行。 下面列出的示例请求 URL 是 `http://<your-edge-device-ipaddress:5000`，但是你的特定容器可能会有所不同。 请记住，需要使用边缘设备的 *外部 IP* 地址。

| 请求 URL | 用途 |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | 容器提供主页。 |
| `http://<your-edge-device-ipaddress>:5000/status` | 还请求了 GET，这将验证用于启动容器的 api 密钥是否有效，且不会导致终结点查询。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。 |
| `http://<your-edge-device-ipaddress>:5000/swagger` | 容器为终结点提供一组完整的文档以及“尝试”功能。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |

![容器的主页](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>后续步骤

* 查看 [安装并运行](../anomaly-detector-container-configuration.md) 容器以拉取容器映像并运行容器
* 查看[配置容器](../anomaly-detector-container-configuration.md)了解配置设置
* [详细了解异常探测器 API 服务](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
