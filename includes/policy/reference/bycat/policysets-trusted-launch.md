---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b0ce25df54d966e952a2fd026ee66940af173770
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618275"
---
|名称 |说明 |策略 |版本 |
|---|---|---|---|
|[\[预览版\]：配置先决条件以在启用了受信任启动的 VM 上启用来宾证明](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Trusted%20Launch/TrustedLaunch_GuestAttestationPrerequisites.json) |将受信任的支持启动的虚拟机配置为自动安装来宾证明扩展并启用系统分配的管理标识以允许 Azure 安全中心主动证明和监视启动完整性。 启动完整性是通过远程证明来证明的。 有关更多详细信息，请参阅以下链接 - [https://aka.ms/trustedlaunch](../../../../articles/virtual-machines/trusted-launch.md) |5 |1.0.0-preview |