---
title: 响应适用于 DNS 的 Azure Defender 警报
description: 了解响应适用于 DNS 的 Azure Defender 发出的警报而所需的步骤
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754611"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>响应适用于 DNS 的 Azure Defender 警报

当收到来自适用于 DNS 的 Azure Defender 的警报时，建议按如下所述调查和响应警报。 适用于 DNS 的 Azure Defender 为所有连接的资源提供保护，因此即使你熟悉触发警报的应用程序或用户，也请务必基于每个警报验证相关情况。  


## <a name="step-1-contact"></a>步骤 1。 联系人

1. 请联系资源所有者，确定行为是预期的还是有意的。
1. 如果活动是预期的，请关闭警报。
1. 如果活动是意外发生的，请将资源处理为可能存在泄露风险并根据下一步中所述进行缓解。

## <a name="step-2-immediate-mitigation"></a>步骤 2。 立即缓解风险 

1. 将资源与网络隔离，以防止横向移动。
1. 按照任何生成的修正建议，对资源运行完整的反恶意软件扫描。
1. 查看资源上已安装且正在运行的软件，删除任何未知或不需要的包。
1. 将计算机还原到已知的良好状态，根据需要重新安装操作系统，并从经过验证的无恶意软件源还原软件。
1. 解决针对计算机的任何 Azure 安全中心建议，修正突出显示的安全问题以防止将来出现漏洞。


## <a name="next-steps"></a>后续步骤

此页介绍了响应来自适用于 DNS 的 Azure Defender 的警报的过程。 如需相关信息，请参阅以下页面：

- [适用于 DNS 的 Azure Defender 简介](defender-for-dns-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [连续导出安全中心数据](continuous-export.md)