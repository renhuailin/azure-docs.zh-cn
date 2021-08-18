---
title: 配置容器 - 文本分析
titleSuffix: Azure Cognitive Services
description: 文本分析为每个容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/21/2021
ms.author: aahi
ms.openlocfilehash: ae3a147b64c1355700398371206c3da39597ad9b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451305"
---
# <a name="configure-text-analytics-docker-containers"></a>配置文本分析 docker 容器

文本分析为每个容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。 还提供了多个[示例 docker 运行命令](how-tos/text-analytics-how-to-install-containers.md#run-the-container-with-docker-run)。

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，并且该值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的 _文本分析_ 资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：“密钥”下的文本分析资源管理

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器的账单信息的文本分析资源的终结点 URI。 必须为此配置设置指定值，并且该值必须是 Azure 上的 _文本分析_ 资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：文本分析概述，标记为 `Endpoint`

|必须| 名称 | 数据类型 | 说明 |
|--|------|-----------|-------------|
|是| `Billing` | 字符串 | 账单终结点 URI。 有关获取账单 URI 的详细信息，请参阅[收集必需参数](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。

文本分析容器不使用输入或输出装载来存储训练或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 此外，由于 docker 服务帐户使用的权限与主机安装位置权限之间的冲突，可能无法访问[主计算机](how-tos/text-analytics-how-to-install-containers.md#host-computer-requirements-and-recommendations)的装载位置。 

|可选| 名称 | 数据类型 | 说明 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | 文本分析容器不使用此项。|
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](how-tos/text-analytics-how-to-install-containers.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
