---
title: 固件完整性-Azure 安全性
description: 了解用于确保固件完整性的加密度量。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557565"
---
# <a name="project-cerberus"></a>项目 Cerberus

Cerberus 是具有无法克隆的标识的 NIST 800-193 兼容硬件信任。 Cerberus 旨在进一步提高 Azure 基础结构的安全状况，因为它提供了针对固件完整性的强大信任锚。

## <a name="enabling-an-anchor-of-trust"></a>启用信任锚
每个 Cerberus 芯片都具有唯一的加密标识，该标识是使用) 的 Microsoft 证书颁发机构 (的签名证书链建立的。 从 Cerberus 获取的度量值可用于验证组件的完整性，如：

- 主机
- 基板管理控制器 (BMC)
- 所有外围设备，包括网络接口卡和 [系统芯片](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC) 

此信任锁定点可帮助保护平台固件：

- 平台上运行的已泄露固件二进制文件
- 利用操作系统、应用程序或虚拟机监控程序中的 bug 的恶意软件和黑客
- 某些类型的供应链攻击 (制造、组装、中转) 
- 具有管理权限或访问硬件的恶意预览体验人员

## <a name="cerberus-attestation"></a>Cerberus 证明
Cerberus 使用平台固件清单 (PFM) 为服务器组件的固件完整性进行身份验证。 PFM 定义授权固件版本的列表，并向 Azure [主机证明服务](measured-boot-host-attestation.md)提供平台度量。 主机证明服务验证度量，并做出决定，只允许受信任的主机加入 Azure 汽油并托管客户工作负荷。

与主机证明服务一起使用时，Cerberus 的功能增强并提升了高安全性的 Azure 生产基础结构。

> [!NOTE]
> 若要了解详细信息，请参阅 GitHub 上的 [项目 Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) 信息。

## <a name="next-steps"></a>后续步骤
若要详细了解如何驱动平台的完整性和安全性，请参阅：

- [固件安全性](firmware.md)
- [安全启动](secure-boot.md)
- [标准启动和主机证明](measured-boot-host-attestation.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)