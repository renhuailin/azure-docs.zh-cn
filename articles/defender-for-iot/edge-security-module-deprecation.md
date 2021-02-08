---
title: 功能支持和停用
titleSuffix: Azure Defender for IoT
description: '在2022年3月1日之前，适用于 IoT 的 Defender 将继续支持 C、c # 和 Edge。'
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 2779a73d3a5f77e3a3b144309baf6d2788565443
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809734"
---
# <a name="feature-support-and-retirement"></a>功能支持和停用

本文介绍适用于 iot 的 Azure Defender 功能，并支持用于 IoT 的不同功能。

## <a name="defender-for-iot-c-c-and-edge-security-module-deprecation"></a>用于 IoT C、c # 和边缘安全模块弃用的 Defender

新的微代理将替换当前的 C、c # 和边缘安全模块。  

新的微代理基于从现有安全模块开发、客户和合作伙伴获得的经验，并具有四项重要改进： 

- **深度安全值**：新代理将在主机级别运行，这将提供对设备的基础操作的更多可见性，并允许更好地进行安全覆盖。

- **改善了设备性能和降低了占用量**：通过小型 RAM 实现，并降低了 CPU 消耗和 CPU 使用率。  

- **即插即** 用：新的微代理不再具有内核级别的依赖项，其所有软件依赖项都作为其包的一部分提供。 微代理支持常见的 CPU 体系结构。

- **易于部署**：微代理通过源代码和二进制包支持不同的分发模型。 

### <a name="timeline"></a>时间线 

在2022年3月1日之前，适用于 IoT 的 Defender 将继续支持 C、c # 和 Edge。 

## <a name="micro-agent-preview-support"></a>微代理预览版支持

在预览期间，微型代理可能会遇到重大更改，恕不另行通知。

## <a name="next-steps"></a>后续步骤

查看 [IoT 传感器和管理控制台 api 的 Defender](references-work-with-defender-for-iot-apis.md)。