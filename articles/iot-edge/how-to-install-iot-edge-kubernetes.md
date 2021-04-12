---
title: 如何在 Kubernetes 上安装 IoT Edge | Microsoft Docs
description: 了解如何使用本地开发群集环境在 Kubernetes 上安装 IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201675"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在 Kubernetes 上安装 IoT Edge（预览）

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 可与 Kubernetes 集成，将 Kubernetes 用作可复原的且高度可用的基础结构层。 在以下情况下，此支持大致适用于 IoT Edge 解决方案：

![k8s 简介](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>若要了解此集成，可将 Kubernetes 视为除 Linux 和 Windows 之外可运行 IoT Edge 应用程序的另一种操作环境。

## <a name="architecture"></a>体系结构 
在 Kubernetes 上，IoT Edge 提供自定义资源定义 (CRD)，用于部署边缘工作负载。 IoT Edge 代理充当 CRD 控制器，可协调云管理的所需状态与本地群集状态。

模块生存期由 Kubernetes 计划程序管理，该计划程序将维护模块的可用性及选择其位置。 IoT Edge 管理顶层运行的边缘应用程序平台，会持续将 IoT 中心内指定的所需状态与边缘群集上的状态相协调。 该应用程序模型仍是用户熟悉的基于 IoT Edge 模块和路由的模型。 IoT Edge 代理控制器将 IoT Edge 的应用程序模型自动转换为 Pod、部署、服务等 Kubernetes 本机构造。

下面是概要性的体系结构示意图：

![kubernetes 体系结构](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

边缘部署的每个组件划归到特定于设备的 Kubernetes 命名空间，因而可在多个边缘设备及其部署之间共享相同的群集资源。

>[!NOTE]
>Kubernetes 上的 IoT Edge 目前以[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)提供。

## <a name="tutorials-and-references"></a>教程和参考 

请参阅 [Kubernetes 上的 IoT Edge 预览文档微型站点](https://aka.ms/edgek8sdoc)，以深入了解教程和参考等信息。
