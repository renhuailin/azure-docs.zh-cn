---
title: 在 Azure 容器实例中运行异常检测器容器
titleSuffix: Azure Cognitive Services
description: 向 Azure 容器实例中部署异常检测器容器，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: 3979bb82bfa055cc2a134bf3119097c452ffb855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "94364116"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>向 Azure 容器实例中部署异常检测器容器

了解如何向 Azure [容器实例](../../../container-instances/index.yml)中部署认知服务[异常检测器](../anomaly-detector-container-howto.md)容器。 此过程演示如何创建异常探测器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>后续步骤

* 查看[安装和运行容器](../anomaly-detector-container-configuration.md)以拉取容器映像并运行容器
* 查看[配置容器](../anomaly-detector-container-configuration.md)了解配置设置
* [详细了解异常检测器 API 服务](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)