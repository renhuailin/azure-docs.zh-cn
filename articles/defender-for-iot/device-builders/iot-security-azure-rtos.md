---
title: Azure RTOS Defender-IoT-micro-agent 概述
description: 详细了解 Azure RTOS Defender-IoT-micro-agent 支持和实现，该模块是 Azure Defender for IoT 的一部分。
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: cf905eb9cff7840eeaac70e731421ba2252193e6
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303645"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos"></a>概述：Defender for IoT 适用于 Azure RTOS 的 Defender-IoT-micro-agent

Azure Defender for IoT 微模块为使用 Azure RTOS 的设备提供了一个全面的安全解决方案。 它覆盖了实时操作系统 (RTOS) 设备上的常见威胁和潜在恶意活动。 Azure RTOS 现在随附了内置的 Azure IoT Defender-IoT-micro-agent。

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Defender for IoT Azure RTOS 的可视化。":::


Azure RTOS 微模块提供以下功能：

- 恶意网络活动检测
- 基于自定义警报的设备行为基线
- 改进的设备安全机制

## <a name="detect-malicious-network-activities"></a>检测恶意网络活动

每个设备的入站和出站网络活动都受到监视。 支持的协议包括 IPv4 和 IPv6 上的 TCP、UDP 和 ICMP。 Defender for IoT 会针对 Microsoft 威胁情报源检查每一个网络活动。 该源会通过在世界各地收集的数百万个独特的威胁指标来实时更新。

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>基于自定义警报的设备行为基线

通过基线，可将设备聚类到安全组，并定义每个组的预期行为。 由于 IoT 设备通常设计为在定义明确且受限的场景中运行，因此可轻松通过使用一组参数来创建定义其预期行为的基线。 任何偏离基线的情况都会触发警报。

## <a name="improve-your-device-security-hygiene"></a>改进设备安全机制

通过使用 Defender for IoT 提供的建议基础结构，你可以获取关于环境中影响和损害设备安全态势的问题的知识和见解。 薄弱的 IoT 设备安全态势如果保持不变，可能会导致潜在的攻击得逞。 安全性总是由任何组织中最薄弱的环节来衡量。

## <a name="get-started-protecting-azure-rtos-devices"></a>开始保护 Azure RTOS 设备

Azure RTOS Defender-IoT-micro-agent 可在设备上免费下载。 每个 Azure 订阅都通过一个 30 天的试用版提供 Defender for IoT 云服务。 若要开始，请下载 [Azure RTOS Defender-IoT-micro-agent](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/device-builders/iot-security-azure-rtos.md)。 

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure RTOS Defender-IoT-micro-agent。 若要详细了解 Defender-IoT-micro-agent 并开始操作，请参阅以下文章：

- [Azure RTOS IoT Defender-IoT-micro-agent 概念](concept-rtos-security-module.md)
- [快速入门：Azure RTOS IoT Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
