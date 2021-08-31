---
title: 配置容器 - 翻译器
titleSuffix: Azure Cognitive Services
description: 使用 `docker run` 命令参数配置翻译器容器运行时环境。 同时存在必需设置和可选设置。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f298a4593ef5aa8af9b7f62f48b4e32075429dbf
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539585"
---
# <a name="configure-translator-docker-containers-preview"></a>配置翻译器 Docker 容器（预览版）

认知服务为每个容器提供一个通用配置框架。  你可以轻松配置翻译器容器，并构建针对强大的云功能和边缘位置进行优化的翻译器应用程序体系结构。

使用 `docker run` 命令参数配置翻译器容器运行时环境。 此容器有多个必需设置，以及一些可选设置。 容器专用设置是帐单设置。

## <a name="configuration-settings"></a>配置设置

此容器具有以下配置设置：

|必须|设置|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|跟踪账单信息。|
|否|[ApplicationInsights](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](/azure/application-insights) 遥测支持。|
|是|[Billing](#billing-configuration-setting)|指定 Azure 上服务资源的终结点 URI。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可条款。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|HTTP 代理|配置 HTTP 代理以发出出站请求。|
|否|[日志记录](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |
|是|[Mounts](#mount-settings)|从主计算机读取数据并将其写入到容器，以及从容器读回数据并将其写回到主计算机。|

 > [!IMPORTANT]
> [ApiKey](#apikey-configuration-setting)、[Billing](#billing-configuration-setting) 和 [EULA](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器无法启动。 详细了解如何使用这些配置设置来实例化容器。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，该值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的翻译器资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：翻译器资源管理，位于“密钥”下 

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器账单信息的翻译器资源的终结点 URI。 必须为此配置设置指定一个值，并且该值必须是 Azure 上翻译器资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：翻译器“概述”页，标记为“`Endpoint`”

| 必须 | 名称 | 数据类型 | 说明 |
| -------- | ---- | --------- | ----------- |
| 是 | `Billing` | 字符串 | 账单终结点 URI。 有关获取账单 URI 的详细信息，请参阅[收集必需参数](translator-how-to-install-container.md#required-elements)。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理凭据设置

[!INCLUDE [Container shared HTTP proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解认知服务容器](../../cognitive-services-container-support.md)
