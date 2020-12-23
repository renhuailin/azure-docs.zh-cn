---
title: 固件安全性-Azure 安全性
description: 了解 Microsoft 如何保护 Azure 硬件和固件。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557603"
---
# <a name="firmware-security"></a>固件安全性
本文介绍 Microsoft 如何保护云硬件生态系统和提供链。

## <a name="securing-the-cloud-hardware-ecosystem"></a>保护云硬件生态系统
Microsoft 积极在云硬件生态系统中的合作伙伴，通过以下方式驱动持续安全改进：

- 与 Azure 硬件和固件伙伴 (，如组件制造商和系统集成商) ，以满足 Azure 硬件和固件安全要求。

- 让合作伙伴可以在以下方面使用 Microsoft 定义的要求对其产品安全状况进行持续评估和改进：

  - 固件安全启动
  - 固件安全恢复
  - 固件安全更新
  - 固件加密
  - 锁定的硬件
  - 精细调试遥测
  - TPM 2.0 硬件的系统支持，用于启用标准启动

- 通过开发规范 (OCP) 安全项目，参与和贡献 [开放式计算项目 ](https://www.opencompute.org/wiki/Security) 。 规范提升了一致性和清晰度，以确保生态系统中的安全设计和体系结构。

   > [!NOTE]
   > 我们对 OCP 安全项目的贡献示例是 [硬件安全启动](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) 规范。

## <a name="securing-hardware-and-firmware-supply-chains"></a>保护硬件和固件供应链
还需要适用于 Azure 的云硬件供应商和供应商，以遵守供应链安全流程和 Microsoft 开发的要求。 需要硬件和固件开发和部署过程才能遵循 Microsoft [安全开发生命周期](https://www.microsoft.com/securityengineering/sdl) (SDL) 过程，例如：

- 威胁建模
- 安全设计评审
- 固件审查和渗透测试
- 保护生成和测试环境
- 安全漏洞管理和事件响应

## <a name="next-steps"></a>后续步骤
若要详细了解如何驱动平台的完整性和安全性，请参阅：

- [安全启动](secure-boot.md)
- [标准启动和主机证明](measured-boot-host-attestation.md)
- [项目 Cerberus](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)