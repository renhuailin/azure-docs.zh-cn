---
title: 面向设备制造商的 Azure Defender for IoT 常见问题解答
description: 查找有关 Azure Defender for IoT 代理的最常见问题的解答。
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 3c2e099ca0a6c7cde2fa8a33ed766ba1d4b91fd1
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434311"
---
# <a name="azure-defender-for-iot-for-device-builders-frequently-asked-questions"></a>面向设备制造商的 Azure Defender for IoT 常见问题解答

本文提供了有关 Defender for IoT 代理的常见问题和解答的列表。

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>是否必须安装嵌入式安全代理？

不是必须在 IoT 设备上安装代理才能启用 Defender for IoT。 可以在以下两个选项之间进行选择 - 有四种不同级别的安全监视和管理功能，这些功能可提供不同级别的保护：

- 在进行修改或不进行修改的情况下安装 Defender for IoT 嵌入式安全代理。 此选项提供有关设备行为和访问的最高级别增强安全见解。

- 不在 IoT 设备上安装安全代理。 此选项可使用更少的安全监视和管理功能实现 IoT 中心通信监视。

## <a name="what-does-the-defender-for-iot-agent-do"></a>Defender for IoT 代理有什么作用？

Defender for IoT 代理涵盖的设备级别威胁涉及设备配置、行为和访问（通过扫描配置来确定）、进程和连接性。 Defender for IoT 安全代理不扫描与业务相关的数据或活动。

Defender for IoT 安全代理是开放源代码的，在 GitHub 上提供 32 位和 64 位 Windows 与 Linux 版本： https://github.com/Azure/Azure-IoT-Security 。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理的依赖项和先决条件是什么？

Defender for IoT 支持各种平台。 请参阅[受支持的设备平台](how-to-deploy-agent.md)以验证对特定设备的支持。

## <a name="which-data-is-collected-by-the-agent"></a>代理收集哪些数据？

代理会收集连接、访问、防火墙配置、进程列表和 OS 基线。

## <a name="how-much-data-will-the-agent-generate"></a>代理将生成多少数据？

代理数据生成由设备、应用程序、连接类型和客户代理配置驱动。 由于设备与 IoT 解决方案之间的差异很大，因此建议首先在实验室或测试设置中部署代理，以观察、了解并设置符合需求的特定配置，同时度量所生成的数据量。 启动服务之后，Defender for IoT 代理可提供操作建议来优化代理吞吐量，以帮助你完成配置和自定义过程。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理消息是否使用 IoT 中心的配额？

是的。 代理传输的数据会计入 IoT 中心配额。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下来要做什么？ 我安装了一个代理，但没有看到任何活动或日志...

1. 检查[代理类型是否适合设备的指定 OS 平台](how-to-deploy-agent.md)

1. 确认[代理正在设备上运行](how-to-agent-configuration.md)。

1. 检查在 IoT 中心内[服务是否已成功启用](quickstart-onboard-iot-hub.md)为“安全性”。

1. 检查是否[在 IoT 中心内使用 Defender for IoT 模块配置](quickstart-create-security-twin.md)了设备。

如果活动或日志仍不可用，请联系 Defender for IoT 合作伙伴以获取更多帮助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，传感器和代理便会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新连接时，安全消息会恢复发送。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理是否可以影响设备的性能或其他已安装的软件？

代理如同任何其他应用程序/进程一样使用计算机资源，不应中断正常的设备活动。 运行代理的设备上的资源消耗与其设置和配置相关联。 建议在生产环境中尝试部署之前，在包含的环境中测试代理配置，以及与其他 IoT 应用程序和功能的互操作性。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我要在设备上进行一些维护。 是否可以关闭代理？

无法关闭代理。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>是否有方法可以测试代理是否在正常工作？

如果代理停止通信或无法发送安全消息，则会生成“设备无提示”警报。

## <a name="can-i-create-my-own-alerts"></a>我是否可以创建自己的警报？

是的，你可以基于多个参数（包括 IP/MAC 地址、协议类型、类、服务、函数、命令等）以及有效负载中包含的自定义标记的值创建自定义警报。 请参阅[创建自定义警报](quickstart-create-custom-alerts.md)，详细了解自定义警报以及如何创建它们。

## <a name="next-steps"></a>后续步骤

若要详细了解如何开始使用 Defender for IoT，请参阅以下文章：

- 阅读 Defender for IoT [概述](overview.md)
- 了解 [Defender for IoT 安全警报](concept-security-alerts.md)
