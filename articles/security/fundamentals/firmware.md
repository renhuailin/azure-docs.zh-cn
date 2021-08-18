---
title: 固件安全性 - Azure 安全性
description: 了解 Microsoft 如何保护 Azure 硬件和固件。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: 64c1a42147a55423336c9994246f08046f44adf5
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895645"
---
# <a name="firmware-security"></a>固件安全性
本文介绍了 Microsoft 如何保护云硬件生态系统和供应链。

## <a name="securing-the-cloud-hardware-ecosystem"></a>保护云硬件生态系统
Microsoft 通过以下方式在云硬件生态系统内积极合作，以推动持续的安全改进：

- 与 Azure 硬件和固件合作伙伴（例如组件制造商和系统集成商）协作，以满足 Azure 硬件和固件安全要求。

- 使合作伙伴能够使用 Microsoft 定义的要求在以下领域持续评估产品和改近其安全状况：

  - 固件安全启动
  - 固件安全恢复
  - 固件安全更新
  - 固件加密
  - 锁定的硬件
  - 精细调试遥测
  - 对 TPM 2.0 硬件的系统支持，用于启用测量启动

- 通过制定规范来参与[开放计算项目 (OCP)](https://www.opencompute.org/wiki/Security) 安全项目并为之投入努力和精力。 规范可提升生态系统中安全设计和体系结构的一致性和清晰度。

   > [!NOTE]
   > [硬件安全启动](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0)规范是我们对 OCP 安全项目所做的努力之一。

## <a name="securing-hardware-and-firmware-supply-chains"></a>保护硬件和固件供应链
Azure 的云硬件提供商和供应商还必须遵守 Microsoft 制定的供应链安全流程和要求。 硬件和固件的开发和部署流程必须遵循 Microsoft [安全开发生命周期](https://www.microsoft.com/securityengineering/sdl) (SDL) 流程，例如：

- 威胁建模
- 安全设计评审
- 固件评审和渗透测试
- 保护构建和测试环境
- 安全漏洞管理和事件响应

## <a name="next-steps"></a>后续步骤
若要详细了解为提高平台完整性和安全性而做的工作，请参阅：

- [平台代码完整性](code-integrity.md)
- [安全启动](secure-boot.md)
- [测量启动和主机证明](measured-boot-host-attestation.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)