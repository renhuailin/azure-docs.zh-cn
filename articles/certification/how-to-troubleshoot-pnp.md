---
title: IoT 即插即用设备故障排除
description: 适用于认证 IoT 即插即用设备的合作伙伴的建议故障排除步骤指南。
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e87051bb27e097f507435582cc919f05fd78aeb
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403052"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>IoT 即插即用认证项目故障排除

在 IoT 即插即用认证项目的连接和测试阶段期间，你可能会遇到阻止你通过 Azure for IoT 认证服务 (AICS) 测试的某些情形。

## <a name="prerequisites"></a>先决条件

- 应登录 [Azure 认证设备门户](https://certify.azure.com)，并为你的设备创建一个项目。 有关详细信息，请查看[教程](tutorial-01-creating-your-project.md)。

## <a name="when-aics-tests-arent-passing"></a>当 AICS 测试未通过时

AICS 测试可能会由于多种原因而未通过。 请按照以下步骤检查常见问题，并对设备进行故障排除。

1. 仔细检查设备代码是否在 DPS 预配期间设置模型 ID 有效负载。 这是 AICS 验证设备的要求。
1. 可以通过按 `View Logs` 按钮来查看来自以前测试运行的遥测日志，以确定导致测试失败的原因。 测试消息和原始数据都可供评审。  

    ![评审测试数据](./media/images/review-logs.png)

1. 在日志指示 `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` 的某些情形中，尝试重新启动设备，并重启设备预配过程。
1. 如果自动测试继续失败，则可以对结果执行 `request a manual review` 操作以进行替代。 这会触发向 Azure 认证设备团队请求进行手动验证。  

    ![请求手动评审](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>当你看到“通过，但出现警告”时

在运行测试时，如果收到的结果为 `Passed with warnings`，这意味着在测试期间未收到某些遥测。 这可能是由于遥测依赖于较长的时间间隔，或外部触发器不可用。 可以继续提交设备进行评审，在此期间，评审团队会确定将来是否需要手动验证。

## <a name="when-you-need-help-with-the-model-repository"></a>当你需要模型存储库方面的帮助时

对于与模型存储库相关的 IoT 即插即用问题，请参阅[有关设备模型存储库的文档指导](../iot-develop/concepts-model-repository.md)。

## <a name="next-steps"></a>后续步骤

希望本指南可帮助你继续进行 IoT 即插即用认证旅程！ 通过 AICS 后，便可以继续学习我们的教程来提交和发布设备。

- [教程：测试设备](tutorial-03-testing-your-device.md)