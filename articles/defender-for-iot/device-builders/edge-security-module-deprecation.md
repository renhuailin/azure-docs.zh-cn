---
title: 功能支持和停用
description: 在 2022 年 3 月 1 日之前，Defender for IoT 将继续支持 C、C# 和 Edge。
ms.date: 07/18/2021
ms.topic: how-to
ms.openlocfilehash: 37848fea276b7925e4e8bd711dd1abe4c0bd338e
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400879"
---
# <a name="feature-support-and-retirement"></a>功能支持和停用

本文介绍 Azure Defender for IoT 功能，并支持 Defender for IoT 中的不同功能。

## <a name="defender-for-iot-c-c-and-edge-defender-iot-micro-agent-deprecation"></a>Defender for IoT C、C# 和 Edge Defender-IoT-micro-agent 弃用

新的微代理将取代当前的 C、C# 和 Edge Defender-IoT-micro-agent。  

新的微代理开发基于从经典安全模块开发中获取的知识和经验、客户和合作伙伴反馈，有四个重要改进：

- 深度安全值：新代理将在主机级别运行，这将提供对设备基础操作的更多可见性，并允许更好地进行安全覆盖。

- 提高了设备性能并降低了占用量：这通过小型 RAM 和 ROM 内存占用量以及低 CPU 消耗量实现。  

- 即插即用：新的微代理不再具有内核级别的依赖项，它的所有软件依赖项都作为包的一部分提供。 微代理支持常见的 CPU 体系结构。

- 易于部署：微代理通过源代码和二进制包支持不同的分发模型。 

### <a name="timeline"></a>时间线 

在 2022 年 3 月 1 日之前，Defender for IoT 将继续支持 C、C# 和 Edge。 

## <a name="micro-agent-preview-support"></a>微代理预览支持

在预览期间，微代理可能会遇到中断性变更，恕不另行通知。

## <a name="next-steps"></a>后续步骤

查看 [Azure Defender for IoT 代理常见问题](resources-agent-frequently-asked-questions.md)。