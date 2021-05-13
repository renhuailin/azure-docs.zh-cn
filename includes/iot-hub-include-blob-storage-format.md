---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b566fc5c8d490d9fbad8ba64217d9ab3110cd709
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313455"
---
> [!NOTE]
> 可将数据以 [Apache Avro](https://avro.apache.org/) 格式（默认）或 JSON 格式写入 Blob 存储。 
>    
> 编码格式只能在配置 Blob 存储终结点时设置。 不能更改已设置的终结点的格式。 使用 JSON 编码时，必须在消息系统属性中将 contentType 设置为 JSON，将 contentEncoding 设置为 UTF-8。 
>
> 若要更详细地了解如何使用 Blob 存储终结点，请参阅[有关如何路由到存储的指南](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)。
>