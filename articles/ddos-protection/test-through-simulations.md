---
title: Azure DDoS 防护模拟测试
description: 了解如何通过模拟进行测试
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 4e6c6b441684d3877a8b85f40b650b257a5159da
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106065"
---
# <a name="test-through-simulations"></a>通过模拟进行测试

良好的做法是通过执行定期模拟，测试服务对攻击做出响应的方式的假设。 在测试期间，验证服务或应用程序是否持续按预期运行，并且不会干扰用户体验。 从技术和流程角度识别现实与模拟中的差距，并将其融入 DDoS 响应策略。 我们建议在过渡环境或者在非高峰期执行此类测试，以便最大程度地降低对生产环境的影响。

我们与 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)（自助式流量生成器）提供商合作开发了一个界面，供 Azure 客户用来针对已启用 DDoS 防护的公共终结点生成流量，以便进行模拟。 可以使用模拟进行以下操作：

- 验证 Azure DDoS 防护如何帮助防范 Azure 资源受到 DDoS 攻击。
- 受到 DDoS 攻击时优化事件响应过程。
- 阐述 DDoS 符合性。
- 培训网络安全团队。

> [!NOTE]
> BreakingPoint Cloud 只对公有云可用。

## <a name="prerequisites"></a>先决条件

- 必须先创建 [Azure DDoS 标准防护计划](manage-ddos-protection.md)（包含受保护的公共 IP 地址），然后才能完成本教程中的步骤。
- 必须先创建 [BreakingPoint Cloud](http://breakingpoint.cloud/) 帐户。 

## <a name="configure-a-ddos-test-attack"></a>配置 DDoS 测试攻击

1. 输入或选择以下值，然后选择“开始测试”：

    |设置        |值                                              |
    |---------      |---------                                          |
    |目标 IP 地址           | 输入要测试的一个公共 IP 地址。                     |
    |端口号   | 输入 _443_。                       |
    |DDoS 配置文件 | 可取值包括 `DNS Flood`、`NTPv2 Flood`、`SSDP Flood`、`TCP SYN Flood`、`UDP 64B Flood`、`UDP 128B Flood`、`UDP 256B Flood`、`UDP 512B Flood`、`UDP 1024B Flood`、`UDP 1514B Flood`、`UDP Fragmentation`、`UDP Memcached`。|
    |测试大小       | 可取值包括 `100K pps, 50 Mbps and 4 source IPs`、`200K pps, 100 Mbps and 8 source IPs`、`400K pps, 200Mbps and 16 source IPs`、`800K pps, 400 Mbps and 32 source IPs`。                                  |
    |测试持续时间 | 可取值包括 `10 Minutes`、`15 Minutes`、`20 Minutes`、`25 Minutes`、`30 Minutes`。|

现在，它应如下所示：

![DDoS 攻击模拟示例：BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>监视和验证

1. 登录 https://portal.azure.com ，然后转到你的订阅。
1. 选择在其上测试了攻击的公共 IP 地址。
1. 在“监视”下，选择“指标”。
1. 对于“指标”，选择“是否受到 DDoS 攻击”。

在资源受到攻击后，应该会看到值从“0”变为“1”，如下图所示：

![DDoS 攻击模拟示例：门户](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud API 脚本

此 [API 脚本](https://aka.ms/ddosbreakingpoint)可以通过运行一次或使用 cron 计划常规测试来自动化 DDoS 测试。 这对于验证日志记录是否配置正确以及检测和响应过程是否有效非常有用。 脚本需要 Linux OS（使用 Ubuntu 18.04 LTS 进行测试）和 Python 3。 使用所包含的脚本或 [BreakingPoint Cloud](http://breakingpoint.cloud/) 网站上的文档来安装必备组件和 API 客户端。

## <a name="next-steps"></a>后续步骤

- 了解如何[查看和配置 DDoS 防护遥测](telemetry.md)。
- 了解如何[查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)。
- 了解如何[使用 DDoS 快速响应](ddos-rapid-response.md)。
