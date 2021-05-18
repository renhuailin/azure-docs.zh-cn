---
title: 如何响应适用于资源管理器的 Azure Defender 警报
description: 了解响应适用于资源管理器的 Azure Defender 发出的警报而所需的步骤
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754579"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>响应适用于资源管理器的 Azure Defender 警报

当收到来自适用于资源管理器的 Azure Defender 的警报时，建议按如下所述调查和响应警报。 适用于资源管理器的 Azure Defender 为所有连接的资源提供保护，因此即使你熟悉触发警报的应用程序或用户，也请务必基于每个警报验证相关情况。  


## <a name="step-1-contact"></a>步骤 1。 联系人

1. 请联系资源所有者，确定行为是预期的还是有意的。
1. 如果活动是预期的，请关闭警报。
1. 如果活动是意外发生的，请将相关的用户帐户、订阅和虚拟机视为处于已泄露状态，并根据下一步中所述进行缓解。

## <a name="step-2-immediate-mitigation"></a>步骤 2。 立即缓解风险 

1. 修正已泄露的用户帐户：
    - 如果不熟悉这些用户帐户，请将其删除，因为它们可能是由威胁者创建的
    - 如果熟悉这些用户帐户，请更改其身份验证凭据
    - 使用 Azure 活动日志查看该用户执行的所有活动，并识别任何可疑活动

1. 修正已泄露的订阅：
    - 从已泄露的自动化帐户中删除所有不熟悉的 Runbook
    - 查看订阅的 IAM 权限，并删除任何不熟悉的用户帐户的权限
    - 查看订阅中的所有 Azure 资源并删除所有不熟悉的资源
    - 查看并调查 Azure 安全中心内针对订阅的任何安全警报
    - 使用 Azure 活动日志查看在订阅中执行的所有活动，并识别任何可疑活动

1. 修正被入侵的虚拟机
    - 更改所有用户的密码
    - 在计算机上运行全面的反恶意软件扫描
    - 从无恶意软件源对计算机重置映像


## <a name="next-steps"></a>后续步骤

此页介绍了响应来自适用于资源管理器的 Azure Defender 的警报的过程。 如需相关信息，请参阅以下页面：

- [适用于资源管理器的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [连续导出安全中心数据](continuous-export.md)