---
title: Azure DDoS 保护模拟测试
description: 了解如何通过模拟测试
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 55692122461ef1b22b43b0def43e826ac7aeae30
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813779"
---
# <a name="test-through-simulations"></a>通过模拟进行测试

良好的做法是通过执行定期模拟，测试服务对攻击做出响应的方式的假设。 在测试期间，验证服务或应用程序是否持续按预期运行，并且不会干扰用户体验。 从技术和流程角度识别现实与模拟中的差距，并将其融入 DDoS 响应策略。 我们建议在过渡环境或者在非高峰期执行此类测试，以便最大程度地降低对生产环境的影响。

我们已与 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)（自助服务流量生成器）进行了合作，以构建一个接口，Azure 客户可在其中针对模拟启用 DDoS 防护的公共终结点生成流量。 可以使用模拟来执行以下操作：

- 验证 Azure DDoS 防护如何帮助防范 Azure 资源受到 DDoS 攻击。
- 受到 DDoS 攻击时优化事件响应过程。
- 阐述 DDoS 符合性。
- 培训网络安全团队。

## <a name="prerequisites"></a>先决条件

- 在完成本教程中的步骤之前，必须先创建具有受保护的公共 IP 地址的 [Azure DDoS 标准保护计划](manage-ddos-protection.md) 。
- 必须首先创建一个具有 [BreakingPoint 云](http://breakingpoint.cloud/)的帐户。 

## <a name="configure-a-ddos-test-attack"></a>配置 DDoS 测试攻击

1. 输入或选择以下值，然后选择 " **开始测试**"：

    |设置        |值                                              |
    |---------      |---------                                          |
    |目标 IP 地址           | 输入要测试的公共 IP 地址之一。                     |
    |端口号   | 输入 _443_。                       |
    |DDoS 配置文件 | 选择 **TCP SYN 洪水**。|
    |测试大小       | 选择 **功能 pps、100 Mbps 和8个源 ip。**                                  |
    |测试持续时间 | 选择 " **10 分钟**"。|

它现在应如下所示：

![DDoS 攻击模拟示例： BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>监视和验证

1. 登录并发送 https://portal.azure.com 到你的订阅。
1. 选择对其进行了攻击的公共 IP 地址。
1. 在“监视”下，选择“指标”。
1. 对于 " **指标**"，请选择 _"DDoS 攻击"_。

资源受到攻击后，应看到值从 **0** 更改为 **1**，如下图所示：

![DDoS 攻击模拟示例：门户](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint 云 API 脚本

此 [API 脚本](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Breaking%20Point%20SDK) 可用于通过运行一次或使用 cron 来计划常规测试来自动执行 DDoS 测试。 这对于验证日志记录是否已正确配置以及检测和响应过程是否有效非常有用。 脚本需要 (使用 Ubuntu 18.04 LTS) 和 Python 3 进行测试的 Linux 操作系统。 使用所包含的脚本或 [BreakingPoint 云](http://breakingpoint.cloud/) 网站上的文档安装必备组件和 API 客户端。

## <a name="next-steps"></a>后续步骤

- 了解如何 [查看和配置 DDoS 保护遥测](telemetry.md)。
- 了解如何 [查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)。
- 了解如何 [联系 DDoS 快速响应](ddos-rapid-response.md)。
