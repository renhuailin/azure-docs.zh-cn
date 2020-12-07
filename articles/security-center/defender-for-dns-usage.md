---
title: 如何响应 Azure Defender DNS 警报
description: 了解响应来自 Azure Defender for DNS 的警报所需的步骤
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754611"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>响应 Azure Defender DNS 警报

收到来自 Azure Defender for DNS 的警报时，建议你按如下所述调查和响应警报。 Azure Defender for DNS 保护所有连接的资源，因此即使你熟悉触发警报的应用程序或用户，也必须验证每个警报的情况。  


## <a name="step-1-contact"></a>步骤 1。 联系人

1. 请联系资源所有者，确定行为是预期的还是有意的。
1. 如果活动是预期的，请关闭警报。
1. 如果活动不是预期的，请根据下一步中所述，将资源视为潜在的泄漏和缓解。

## <a name="step-2-immediate-mitigation"></a>步骤 2. 即时缓解 

1. 将资源与网络隔离，以防侧向移动。
1. 按照任何得到的修正建议，对资源运行完整的反恶意软件扫描。
1. 查看资源上已安装并正在运行的软件，删除任何未知或不需要的包。
1. 将计算机还原到已知的良好状态，根据需要重新安装操作系统，并从已验证的无恶意软件源还原软件。
1. 解决计算机的任何 Azure 安全中心建议，修正突出显示的安全问题以防止将来出现漏洞。


## <a name="next-steps"></a>后续步骤

此页介绍了从 Azure Defender for DNS 响应警报的过程。 有关相关信息，请参阅以下页面：

- [Azure Defender for DNS 简介](defender-for-dns-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [连续导出安全中心数据](continuous-export.md)