---
title: Azure IoT Edge 流分析
description: 在 Azure 流分析中创建 edge 作业，并将其部署到运行 Azure IoT Edge 的设备。
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012608"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 流分析
 
Azure IoT Edge 流分析使开发人员能够将近乎实时的分析情报部署到 IoT 设备更近的时间，从而使他们能够解锁设备生成数据的全部价值。 Azure 流分析专为实现低延迟、复原能力、有效使用带宽和合规性而设计。 企业可以将控制逻辑部署到接近工业运营的程度，并补充在云中完成的大数据分析。

IoT Edge 上的 Azure 流分析在 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 框架中运行。 在流分析中创建作业后，可以使用 IoT 中心来部署和管理作业。

## <a name="common-scenarios"></a>常见方案

本部分介绍 IoT Edge 上的流分析的常见方案。 下图显示 IoT 设备与 Azure 云之间的数据流。

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge 的高级关系图":::

### <a name="low-latency-command-and-control"></a>低延迟命令和控制

生产安全系统必须响应超高延迟的操作数据。 通过 IoT Edge 上的流分析，可以近乎实时地分析传感器数据，并在检测到异常以停止计算机或触发警报时发出命令。

### <a name="limited-connectivity-to-the-cloud"></a>限制到云的连接

任务关键型系统（如远程采矿设备、连接的船舶或海上钻井）需要分析数据并对数据做出反应，即使云连接是间歇性的也是如此。 通过流分析，你的流逻辑独立于网络连接运行，你可以选择要发送到云以进行进一步处理或存储的内容。

### <a name="limited-bandwidth"></a>有限带宽

由喷气引擎或联网汽车生成的数据量可能非常大，因此，在将数据发送到云之前必须对数据进行筛选或预处理。 使用流分析，可以筛选或聚合需要发送到云的数据。

### <a name="compliance"></a>合规性

监管符合性可能需要在将一些数据发送到云之前对其进行本地匿名或聚合处理。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure 流分析作业中的 Edge 作业

流分析边缘作业在部署到 [Azure IoT Edge 设备](../iot-edge/about-iot-edge.md)的容器中运行。 边缘作业由两部分组成：

* 负责作业定义的云部件：用户在云中定义输入、输出、查询和其他设置，如不按序事件。

* 在 IoT 设备上运行的模块。 模块包含流分析引擎，并从云接收作业定义。 

流分析使用 IoT 中心将边缘作业部署到设备 () 。 有关详细信息，请参阅 [IoT Edge 部署](../iot-edge/module-deployment-monitoring.md)。

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure 流分析 Edge 作业":::

## <a name="edge-job-limitations"></a>边缘作业限制

目标是在 IoT Edge 作业和云作业之间进行平衡。 大多数 SQL 查询语言功能都支持边缘和云。 但边缘作业不支持以下功能：
* JavaScript 中的用户定义函数 (UDF)。 [用于 IoT Edge 作业的 C#](./stream-analytics-edge-csharp-udf.md)（预览版）中提供了 UDF。
* 用户定义聚合 (UDA)。
* Azure ML 函数。
* 用于输入/输出的 AVRO 格式。 目前仅支持 CSV 和 JSON。
* 以下 SQL 运算符：
    * PARTITION BY
    * GetMetadataPropertyValue
* 延迟到达策略

### <a name="runtime-and-hardware-requirements"></a>运行时和硬件要求
若要在 IoT Edge 上运行流分析，则需要可 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)运行的设备。 

流分析和 Azure IoT Edge 使用 **Docker** 容器提供可移植解决方案，该解决方案在 (Windows、Linux) 的多个主机操作系统上运行。

IoT Edge 上的流分析作为 Windows 和 Linux 映像提供，同时在 x86 64 或 ARM (高级 RISC 计算机) 体系结构上运行。 


## <a name="input-and-output"></a>输入和输出

流分析边缘作业可从 IoT Edge 设备上运行的其他模块获取输入和输出。 要与特定模块实现相互连接，你可以在部署时设置路由配置。 有关详细信息，请参阅 [IoT Edge 模块组成文档](../iot-edge/module-composition.md)。

输入和输出均支持 CSV 和 JSON 格式。

对于在流分析作业中创建的每个输入和输出流，会在已部署的模块上创建相应的终结点。 这些终结点可以用于部署的路由。

支持的流输入类型为：
* Edge 中心
* 事件中心
* IoT 中心

支持的流输出类型为：
* Edge 中心
* SQL 数据库
* 事件中心
* Blob 存储/ADLS Gen2

参考输入支持参考文件类型。 可以使用下游的云作业访问其他输出。 例如，在 Edge 中托管的流分析作业会将输出发送到 Edge 中心，然后后者可以将输出发送到 IoT 中心。 可以使用另一个云托管的 Azure 流分析作业，其中包含 IoT 中心提供的输入和输出到 Power BI 或其他输出类型。

## <a name="license-and-third-party-notices"></a>许可证和第三方通知
* [IoT Edge 上的 Azure 流分析许可证](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [IoT Edge 上的 Azure 流分析的第三方通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="azure-stream-analytics-module-image-information"></a>Azure 流分析模块映像信息 

此版本信息上次更新时间：2020-09-21：

- 映像：`mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - 基础映像：mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - 平台：
      - 体系结构：amd64
      - os：linux
 
- 映像：`mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - 基础映像：mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - 平台：
      - 体系结构：arm
      - os：linux
 
- 映像：`mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - 基础映像：mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - 平台：
      - 体系结构：arm64
      - os：linux
      
      
## <a name="get-help"></a>获取帮助
若要获得进一步的帮助，可前往 [Azure 流分析的 Microsoft 问答页](/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>后续步骤

* [有关 Azure IoT Edge 的详细信息](../iot-edge/about-iot-edge.md)
* [IoT Edge 教程上的流分析](../iot-edge/tutorial-deploy-stream-analytics.md)
* [使用 Visual Studio 工具开发流分析 Edge 作业](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 API 实现流分析的 CI/CD](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
