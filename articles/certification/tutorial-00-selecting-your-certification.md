---
title: Azure 认证设备计划 - 教程 - 选择认证计划
description: 有关为设备选择适当认证计划的分步指南
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975427"
---
# <a name="tutorial-select-your-certification-program"></a>教程：选择认证计划

祝贺你选择了 Azure 认证设备计划！ 我们很高兴你能够加入我们的认证设备生态系统。 若要开始，必须先确定哪些认证计划最适合你的设备功能。

在本教程中，学习：

> [!div class="checklist"]
> * 选择最适合你的设备的认证计划

## <a name="selecting-a-certification-program-for-your-device"></a>为设备选择认证计划

所有设备必须符合 [**Azure 认证设备**](./program-requirements-azure-certified-device.md)计划所述的基线要求。 其他三种认证徽章是基于此计划颁发的，它们提供不同的客户价值。 你可以选择三种增量徽章（IoT 即插即用、Edge Managed 和 Edge Secured Core *预览版）中的一种或多种。

1. 请查看下表中每个认证计划的要求。 可以在标题中选择每个计划的详细要求。

    |要求|[IoT 即插即用](./program-requirements-edge-secured-core.md)|[Edge Managed](./program-requirements-edge-managed.md)|[Edge Secured Core](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | 处理器 | 任意|MPU/CPU|MPU/CPU|
    | (OS) | 任意|[第 1 层 OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|[第 1 层 OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | IoT Edge 运行时 | 不支持 |必需|必需|
    | Defender for IoT | 不支持|必需|必需|
    | ADU/Windows 更新 | 不支持|必需|必需|

1. 在你觉得自己知道了设备所要满足的要求后，接下来请查看计划的技术要求。 这可能是 Azure 认证设备认证，也可能是基线认证与三种增量徽章之一的组合。 

## <a name="next-steps"></a>后续步骤

你现在已准备好开始认证设备了！ 请继续学习下一篇文章开始处理自己的项目。
> [!div class="nextstepaction"]
>[教程：创建项目](tutorial-01-creating-your-project.md)
