---
title: 配置表单识别器容器
titleSuffix: Azure Applied AI Services
description: 了解如何将表单识别器容器配置为分析表单和表数据。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: a7a42f9a2078a3384949704a6319c8acbedcb17d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326328"
---
# <a name="configure-form-recognizer-containers"></a>配置表单识别器容器

> [!IMPORTANT]
>
> 表单识别器容器为受限预览版。 若要使用它们，必须提交[在线请求](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)并获得批准。 有关详细信息，请参阅下面的[请求批准运行容器](form-recognizer-container-install-run.md#request-approval-to-run-the-container)。

使用 Azure 表单识别器容器，你可以构建经过优化的应用程序体系结构，以利用强大的云功能和边缘位置。 容器提供了一个最简单的虚拟隔离环境，可以轻松部署在本地和云中。 在本文中，你将了解如何使用 `docker compose` 命令参数配置表单识别器容器运行时环境。 以下六个表单识别器功能容器支持表单识别器功能：布局、名片、ID 文档、回执、发票、自定义     。 这些容器有多个必需设置和一些可选设置。 有关示例，请参阅[示例 docker-compose.yml 文件](#example-docker-composeyml-file)部分。

## <a name="configuration-settings"></a>配置设置

每个容器都有以下配置设置：

|必须|设置|目的|
|--|--|--|
|是|[ApiKey](#apikey-and-billing-configuration-setting)|跟踪账单信息。|
|是|[Billing](#apikey-and-billing-configuration-setting)|指定 Azure 上服务资源的终结点 URI。 若要详细了解如何进行获取，请参阅[计费]](form-recognizer-container-install-run.md#billing)。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../../../cognitive-services/cognitive-services-custom-subdomains.md)。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可条款。|
|否|[ApplicationInsights](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](/azure/application-insights) 遥测支持。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|HTTP 代理|配置 HTTP 代理以发出出站请求。|
|否|[日志记录](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |

> [!IMPORTANT]
> [`ApiKey`](#apikey-and-billing-configuration-setting)、[`Billing`](#apikey-and-billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。 必须为所有三项设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](form-recognizer-container-install-run.md#billing)。

## <a name="apikey-and-billing-configuration-setting"></a>ApiKey 和 Billing 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 ApiKey 的值必须是在“Billing 配置设置”部分为 `Billing` 指定的资源的有效密钥。

`Billing` 设置指定 Azure 上用于计量容器的计费信息的资源的终结点 URI。 此配置设置的值必须是 Azure 上某个资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

 可以在 Azure 门户中的“密钥和终结点”页上找到这些设置。

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户的“密钥和终结点”页面。":::

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="volume-settings"></a>卷设置

使用[卷](https://docs.docker.com/storage/volumes/)从容器读取数据以及将数据写入容器。 卷是用于保存 Docker 容器生成和使用的数据的首选方式。 你可以通过包括 `volumes` 选项并指定 `type` (bind)、`source`（文件夹路径）和 `target`（文件路径参数）来指定输入装载或输出装载。

表单识别器容器需要一个输入卷和一个输出卷。 输入卷可以是只读的 (`ro`)，需要使用它来访问用于训练和评分的数据。 输出卷必须是可写的，用于存储模型和临时数据。

主机卷位置的确切语法因主机操作系统而异。 另外，由于 Docker 服务帐户权限与主机装载位置权限之间有冲突，因此可能无法访问[主机](form-recognizer-container-install-run.md#host-computer-requirements)的卷位置。

## <a name="example-docker-composeyml-file"></a>示例 docker-compose.yml 文件

docker compose 方法包括三个步骤：

 1. 创建 Dockerfile。
 1. 在 docker-compose.yml 中定义服务，以便它们可以在隔离的环境中一起运行。
 1. 运行 `docker-compose up` 来启动并运行你的服务。

### <a name="single-container-example"></a>单容器示例

在此示例中，输入你的布局容器实例的 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {FORM_RECOGNIZER_API_KEY} 值。

#### <a name="layout-container"></a>**布局容器**

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}

    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

### <a name="multiple-containers-example"></a>多容器示例

#### <a name="receipt-and-ocr-read-containers"></a>**收据和 OCR 读取容器**

在此示例中，输入你的收据容器的 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {FORM_RECOGNIZER_API_KEY} 值，并输入你的计算机视觉读取容器的 {COMPUTER_VISION_ENDPOINT_URI} 和 {COMPUTER_VISION_API_KEY} 值。

```yml
version: "3"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: cognitiveservicespreview.azurecr.io/microsoft/cognitive-services-form-recognizer-receipt:2.1
    environment:
      - EULA=accept 
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept 
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解如何运行多个容器和 docker compose 命令](form-recognizer-container-install-run.md)
