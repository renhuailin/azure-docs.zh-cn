---
title: 固件完整性 - Azure 安全性
description: 了解用于确保固件完整性的加密度量。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: cda9aacfc3a207aa43c63cc26189da64fac87e85
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893917"
---
# <a name="project-cerberus"></a>Cerberus 项目

Cerberus 是符合 NIST 800-193 要求的硬件信任根，其标识无法克隆。 Cerberus 旨在通过为固件完整性提供强大的信任锚来进一步提高 Azure 基础结构的安全状况。

## <a name="enabling-an-anchor-of-trust"></a>启用信任锚
每个 Cerberus 芯片都有一个唯一的加密标识，该标识是使用植根于 Microsoft 证书颁发机构 (CA) 的签名证书链建立的。 从 Cerberus 获得的度量可用于验证组件的完整性，例如：

- 主机
- 基板管理控制器 (BMC)
- 所有外设，包括网卡和[片上系统](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC)

此信任锚可帮助保护平台固件免受以下攻击：

- 平台上运行的已泄露固件二进制文件
- 利用操作系统、应用程序或虚拟机监控程序中的 bug 的恶意软件和黑客
- 某些类型的供应链攻击（制造、组装、运输）
- 具有管理特权或硬件访问权限的恶意内部攻击

## <a name="cerberus-attestation"></a>Cerberus 证明
Cerberus 使用平台固件清单 (PFM) 验证服务器组件的固件完整性。 PFM 定义授权固件版本的列表，并为 Azure [主机证明服务](measured-boot-host-attestation.md)提供平台度量。 主机证明服务可验证度量，并决定仅允许受信任的主机加入 Azure 机群以及托管客户工作负载。

结合主机证明服务，Cerberus 的功能可以增强和提升高度安全的 Azure 生产基础结构。

> [!NOTE]
> 要了解详细信息，请参阅 GitHub 上的 [Project Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) 信息。

## <a name="next-steps"></a>后续步骤
若要详细了解为提高平台完整性和安全性而做的工作，请参阅：

- [固件安全性](firmware.md)
- [平台代码完整性](code-integrity.md)
- [安全启动](secure-boot.md)
- [测量启动和主机证明](measured-boot-host-attestation.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)