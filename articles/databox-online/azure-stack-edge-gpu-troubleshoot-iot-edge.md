---
title: 排查 Azure Stack Edge Pro GPU 设备上的 IoT Edge 问题 | Microsoft Docs
description: 介绍如何排查 Azure Stack Edge Pro GPU 设备上的 IoT Edge 错误。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 405a9c62a551a011eb6d7b00025c6ae0a563e858
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987794"
---
# <a name="troubleshoot-iot-edge-issues-on-your-azure-stack-edge-pro-gpu-device"></a>排查 Azure Stack Edge Pro GPU 设备上的 IoT Edge 问题 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何通过查看 IoT Edge 代理的运行时响应和设备上安装的 IoT Edge 服务的错误，排查 Azure Stack Edge Pro GPU 设备上与计算相关的错误。

## <a name="review-iot-edge-runtime-responses"></a>查看 IoT Edge 运行时响应

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="troubleshoot-iot-edge-service-errors"></a>排查 IoT Edge 服务错误

以下错误与 Azure Stack Edge Pro GPU 设备上的 IoT Edge 服务相关。

[!INCLUDE [Troubleshoot IoT Edge errors](../../includes/azure-stack-edge-iot-troubleshoot-compute-error-detail.md)]


## <a name="next-steps"></a>后续步骤

- [调试与 IoT Edge 相关的 Kubernetes 问题](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)。
- [排查设备问题](azure-stack-edge-gpu-troubleshoot.md)。