---
title: 针对 Azure 中虚拟机的安全建议
description: 应用这些针对 Azure 中 VM 的建议有助于履行共担责任模型中所述的安全义务，并提高部署的整体安全性。
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 167d46b96853c372790002bdb0d9609581970426
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690196"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>针对 Azure 中虚拟机的安全建议

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文包含适用于 Azure 虚拟机的安全建议。 请遵循这些建议来履行我们责任分担模型中所述的安全义务。 这些建议还有助于改善 Web 应用解决方案的整体安全性。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请参阅[云计算的分担责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

在 Azure 安全中心可以自动实施本文所述的某些建议。 在保护 Azure 中的资源方面，Azure 安全中心是第一道防线。 它定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后，它会建议如何解决漏洞。 有关详细信息，请参阅 [Azure 安全中心的安全建议](../security-center/security-center-recommendations.md)。

有关 Azure 安全中心的常规信息，请参阅[什么是 Azure 安全中心？](../security-center/security-center-introduction.md)。

## <a name="general"></a>常规

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 生成自定义 VM 映像时，请应用最新的更新。 | 在创建映像之前，为操作系统以及要包含在映像中的所有应用程序安装最新更新。  | - |
| 使 VM 保持最新。 | 可以使用 Azure 自动化中的[更新管理](../automation/update-management/overview.md)解决方案来管理 Azure 中 Windows 和 Linux 计算机的操作系统更新。 | [是](../security-center/asset-inventory.md) |
| 备份 VM。 | [Azure 备份](../backup/backup-overview.md)可帮助保护应用程序数据，其运行开销极低。 应用程序错误可能会损坏数据，人为错误可能会将 bug 引入应用程序。 Azure 备份可以保护运行 Windows 和 Linux 的 VM。 | - |
| 使用多个 VM 来提高复原能力和可用性。 | 如果 VM 运行的应用程序必须高度可用，请使用多个 VM 或[可用性集](./availability.md)。 | - |
| 采用业务连续性和灾难恢复 (BCDR) 策略。 | 使用 Azure Site Recovery 可以从支持业务连续性的不同选项中进行选择。 它支持不同的复制和故障转移方案。 有关详细信息，请参阅[关于 Site Recovery](../site-recovery/site-recovery-overview.md)。 | - |

## <a name="data-security"></a>数据安全性

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 加密操作系统磁盘。 | [Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)可帮助你加密 Windows 和 Linux IaaS VM 磁盘。 在没有所需密钥的情况下，无法读取已加密磁盘的内容。 磁盘加密可以防范有人未经授权访问存储的数据，否则他们可能会复制磁盘。| [是](../security-center/asset-inventory.md) |
| 加密数据磁盘。 | [Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)可帮助你加密 Windows 和 Linux IaaS VM 磁盘。 在没有所需密钥的情况下，无法读取已加密磁盘的内容。 磁盘加密可以防范有人未经授权访问存储的数据，否则他们可能会复制磁盘。| -  |
| 限制安装的软件。 | 将安装的软件限制为成功应用解决方案所需的软件。 此准则原则有助于减小解决方案的受攻击面。 | - |
| 使用防病毒软件或反恶意软件。 | 在 Azure 中，可以使用安全供应商（例如 Microsoft、Symantec、Trend Micro 和 Kaspersky）提供的反恶意软件。 这些软件可帮助保护 VM 免受恶意文件、广告程序和其他威胁的侵害。 可以根据应用程序工作负荷部署 Microsoft Antimalware。 Microsoft 反恶意软件仅适用于 Windows 计算机。 使用默认的基本安全性或高级自定义配置。 有关详细信息，请参阅[适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)。 | - |
| 安全存储密钥和机密。 | 为应用程序所有者提供安全的集中管理选项来简化机密和密钥的管理。 这种管理可以减少意外泄密或透露的风险。 Azure Key Vault 可以安全地将密钥存储在经过 FIPS 140-2 级别 2 认证的硬件安全模块 (HSM) 中。 如果你需要使用 FIPs 140.2 级别 3 来存储密钥和机密，可以使用 [Azure 专用 HSM](../dedicated-hsm/overview.md)。 | - |

## <a name="identity-and-access-management"></a>标识和访问管理 

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 集中进行 VM 身份验证。 | 可以使用 [Azure Active Directory 身份验证](../active-directory/develop/authentication-vs-authorization.md)集中进行 Windows 和 Linux VM 的身份验证。 | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 监视 VM。 | 可以使用[用于 VM 的 Azure Monitor](../azure-monitor/vm/vminsights-overview.md) 来监视 Azure VM 和虚拟机规模集的状态。 VM 性能问题可能会导致服务中断，从而违反可用性安全原则。 | - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 限制对管理端口的访问。 | 攻击者可能会利用猜出的常用密码和已知的未修补漏洞，扫描公有云 IP 范围中的开放管理端口，然后试图发起“轻而易举”的攻击。 可以使用[实时 (JIT) VM 访问](../security-center/security-center-just-in-time.md)来锁定发往 Azure VM 的入站流量，降低遭受攻击的可能性，同时在需要时提供与 VM 的连接。 | - |
| 限制网络访问。 | 可以通过网络安全组限制网络访问并控制公开的终结点数。 有关详细信息，请参阅[创建、更改或删除网络安全组](../virtual-network/manage-network-security-group.md)。 | - |

## <a name="next-steps"></a>后续步骤

请咨询应用程序提供商，了解是否有其他安全要求。 有关开发安全的应用程序的详细信息，请参阅[安全开发文档](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)。