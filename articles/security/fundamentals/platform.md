---
title: Azure 平台完整性和安全性-Azure 安全性
description: Azure 平台完整性与安全性技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557607"
---
# <a name="platform-integrity-and-security-overview"></a>平台完整性和安全性概述
Azure 汽油包含数百万服务器 (主机) ，每日增加了上千个。 数千台主机还会通过重新启动、操作系统刷新或修复日常维护。 在主机可以加入汽油并开始接受客户工作负荷之前，Microsoft 会验证该主机是否处于安全可信状态。 此验证可确保在供应链或维护工作流期间启动序列组件上未发生恶意或无意的更改。

## <a name="securing-azure-hardware-and-firmware"></a>保护 Azure 硬件和固件
此系列文章介绍了 Microsoft 如何通过其生命周期中的各个阶段（从制造到日落）来确保主机的完整性和安全性。 文章讨论了：
 
- [固件安全性](firmware.md)
- [UEFI 安全启动](secure-boot.md)
- [标准启动和主机证明](measured-boot-host-attestation.md)
- [项目 Cerberus](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)
 
## <a name="next-steps"></a>后续步骤

- 了解 Microsoft 如何积极地在云硬件生态系统中合作来驱动持续 [固件安全改进](firmware.md)。

- 了解[云中责任分担](shared-responsibility.md)。