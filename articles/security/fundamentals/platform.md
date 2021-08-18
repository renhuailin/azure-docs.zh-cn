---
title: Azure 平台完整性和安全性 - Azure 安全性
description: Azure 平台完整性和安全性的技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e0522d6dcb02571a1ed197d60734906ee8131ab4
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893953"
---
# <a name="platform-integrity-and-security-overview"></a>平台完整性和安全性概述
Azure 机群由数百万台服务器（主机）组成，每天增加数千台服务器。 每天还会有数千台主机通过重新启动、操作系统刷新或维修来进行维护。 在主机可以加入机群并开始接受客户的工作负载之前，Microsoft 会验证主机是否处于安全可信的状态。 此验证可确保在供应链或维护工作流过程中，启动序列组件上没有发生恶意更改或无意的更改。

## <a name="securing-azure-hardware-and-firmware"></a>保护 Azure 硬件和固件
此系列文章介绍 Microsoft 如何在主机整个生命周期的各个阶段（从制造到弃用）中确保其完整性和安全性。 文章讨论了：
 
- [固件安全性](firmware.md)
- [平台代码完整性](code-integrity.md)
- [UEFI 安全启动](secure-boot.md)
- [测量启动和主机证明](measured-boot-host-attestation.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)
 
## <a name="next-steps"></a>后续步骤

- 了解 Microsoft 如何在云硬件生态系统内积极合作，以推动[固件安全性的持续改进](firmware.md)。

- 了解[云中责任分担](shared-responsibility.md)。