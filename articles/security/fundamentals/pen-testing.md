---
title: 渗透测试 | Microsoft Docs
description: 本文概述了渗透测试过程，以及对运行在 Azure 基础结构中的应用进行渗透测试的方式。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550195"
---
# <a name="penetration-testing"></a>渗透测试

使用 Azure 进行应用程序测试和部署的一个优点是可快速创建环境。 不必为请求、获取以及“搭架和堆叠”本地硬件担心。

快速创建环境很好，但仍需确保进行常规安全审慎调查。 你可能想要做的事情之一就是对部署在 Azure 中的应用程序进行渗透测试。

我们不会为你执行应用程序的渗透测试，但我们确实理解你希望并且需要对自己的应用程序执行渗透测试。 这是好事，因为改进自己的应用程序的安全性可以加强整个 Azure 生态系统的安全性。

自 2017 年 6 月 15 日起，对于针对 Azure 资源进行的渗透测试，Microsoft 不再要求执行预批准流程。 本流程仅与 Microsoft Azure 相关，并不适用于任何其他 Microsoft 云服务。

>[!IMPORTANT]
>虽然参加渗透测试时无需再通知 Microsoft，客户仍须遵守 [Microsoft 云统一渗透测试参与规则](https://technet.microsoft.com/mt784683)。

可以执行的标准测试包括：

* 对终结点进行测试，以发现[开放 Web 应用程序安全项目 (OWASP) 的前 10 个漏洞](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* 终结点的[模糊测试](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* 终结点的[端口扫描](https://en.wikipedia.org/wiki/Port_scanner)

你不能执行的一类渗透测试是任何类型的[拒绝服务 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻击。 此测试包括：自行发起 DoS 攻击，或者执行相关的测试，以便确定、演示或模拟任何类型的 DoS 攻击。

>[!Note]
>Mircosoft 已与 BreakingPoint Cloud 合作构建接口，用户可在其中针对已启用 DDoS 保护的公共 IP 地址生成用于模拟的流量。 若要了解有关 BreakingPoint Cloud 模拟的详细信息，请参阅[通过模拟进行测试](../../ddos-protection/test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解[渗透测试参与规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)。
