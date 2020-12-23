---
title: 如何响应 Azure Defender 资源管理器警报
description: 了解响应 Azure Defender for 资源管理器的警报所需的步骤
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754579"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>响应 Azure Defender 资源管理器警报

收到来自 Azure Defender for 资源管理器的警报时，建议你按下述方式调查和响应警报。 Azure Defender for 资源管理器保护所有连接的资源，因此即使你熟悉触发警报的应用程序或用户，也必须验证每个警报的情况。  


## <a name="step-1-contact"></a>步骤 1。 联系人

1. 请联系资源所有者，确定行为是预期的还是有意的。
1. 如果活动是预期的，请关闭警报。
1. 如果活动不是预期的，请根据以下步骤中所述，将相关的用户帐户、订阅和虚拟机视为已泄露，并加以缓解。

## <a name="step-2-immediate-mitigation"></a>步骤 2. 即时缓解 

1. 修正已泄露的用户帐户：
    - 如果不熟悉，请将其删除，因为它们可能是由威胁参与者创建的
    - 如果他们熟悉，请更改其身份验证凭据
    - 使用 Azure 活动日志查看用户执行的所有活动，并识别可疑的任何活动

1. 修正已泄露的订阅：
    - 从受影响的自动化帐户中删除所有不熟悉的 Runbook
    - 查看订阅的 IAM 权限，并删除任何不熟悉的用户帐户的权限
    - 查看订阅中的所有 Azure 资源并删除不熟悉的任何资源
    - 查看并调查 Azure 安全中心的订阅的任何安全警报
    - 使用 Azure 活动日志查看在订阅中执行的所有活动，并识别所有可疑活动

1. 修正已泄露的虚拟机
    - 更改所有用户的密码
    - 在计算机上运行完整的反恶意软件扫描
    - 从无恶意软件源重新映像计算机


## <a name="next-steps"></a>后续步骤

此页介绍了从 Azure Defender 资源管理器的警报的响应过程。 有关相关信息，请参阅以下页面：

- [Azure Defender for 资源管理器简介](defender-for-resource-manager-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [连续导出安全中心数据](continuous-export.md)