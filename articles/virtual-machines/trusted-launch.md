---
title: 预览： Azure Vm 的可信启动
description: 了解 Azure 虚拟机的受信任启动。
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: bc1afa72a0eebd2bb467616237641222b790923c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679169"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Azure 虚拟机的受信任启动 (预览) 

Azure 提供受信任的启动，是一种用于提高 [第2代](generation-2.md) vm 安全性的无缝方式。 受信任的启动可防止出现高级和持续攻击方法。 受信任的发布由多个可单独启用的协调式基础结构技术组成。 每种技术均提供另一层防御措施来应对复杂的威胁。

> [!IMPORTANT]
> 可信发布要求创建新的虚拟机。 不能对最初创建的现有虚拟机启用受信任的启动。
>
> 可信发布目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
>
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="benefits"></a>优点 

- 安全地部署具有经验证的启动加载程序、操作系统内核和驱动程序的虚拟机。
- 安全保护虚拟机中的密钥、证书和密码。
- 深入了解整个启动链的完整性。
- 确保工作负载可信且可验证。

## <a name="public-preview-limitations"></a>公共预览版限制

**大小支持**：除之外的所有 [第2代](generation-2.md) VM 大小：

- HBv3 
- Lsv2 系列 
- M 系列 
- Mv2 系列 
- NDv4 系列 
- NVv4 系列

**OS 支持**：
- Redhat Enterprise Linux 8。3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 专业版
- Windows 10 企业版

**区域**： 
- 美国中南部
- 北欧

**定价**：现有 VM 定价无额外成本。

**此预览版不支持以下功能**：
- 备份
- Azure Site Recovery
- 共享的映像库
- 临时 OS 磁盘
- 共享磁盘
- 托管映像
- Azure 专用主机 

## <a name="secure-boot"></a>安全启动

可信启动的根目录是 VM 的安全启动。 此模式在平台固件中实现，可防止安装基于恶意软件的木马和启动工具包。 安全启动工作，确保只有已签名的操作系统和驱动程序可以启动。 它为 VM 上的软件堆栈建立 "信任根"。 启用安全启动后， (启动加载程序、内核、内核驱动程序) 的所有操作系统启动组件都必须由受信任的发布者签名。 Windows 和选择 Linux 分发版都支持安全启动。 如果安全启动未能通过受信任的发布者对映像进行身份验证，则将不允许 VM 启动。 有关详细信息，请参阅[安全启动](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)。

## <a name="vtpm"></a>vTPM

受信任的启动还介绍 Azure Vm 的 vTPM。 这是与 TPM 2.0 规范兼容的硬件 [受信任的平台模块](/windows/security/information-protection/tpm/trusted-platform-module-overview)的虚拟化版本。它充当密钥和度量的专用安全保管库。 可信发布为 VM 提供了自己的专用 TPM 实例，该实例在不在任何 VM 范围内的安全环境中运行。 VTPM 通过测量 VM 的整个启动链 (UEFI、OS、系统和驱动程序) 来启用 [证明](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) 。 

可信发布使用 vTPM 通过云执行远程认证。 这用于平台运行状况检查和进行基于信任的决策。 作为运行状况检查，受信任的启动可以加密方式验证 VM 是否可正常启动。 如果进程失败（可能是因为 VM 正在运行未经授权的组件），则 Azure 安全中心将发出完整性警报。 这些警报包括有关哪些组件无法通过完整性检查的详细信息。

## <a name="virtualization-based-security"></a>基于虚拟化的安全功能

[基于虚拟化的安全](/windows-hardware/design/device-experiences/oem-vbs) (VBS) 使用虚拟机监控程序创建安全且隔离的内存区域。 Windows 使用这些区域来运行各种安全解决方案，并增强保护以防止漏洞和恶意攻击。 可信发布允许 (要求 HVCI) 和 Windows Defender Credential Guard 启用虚拟机监控程序代码完整性。

要求 HVCI 是一种功能强大的系统缓解措施，可保护 Windows 内核模式进程，防止恶意或未验证代码注入和执行。 它在运行前检查内核模式驱动程序和二进制文件，以防未签名的文件加载到内存中。 这可确保在允许加载此类可执行代码后，不能对其进行修改。 有关 VBS 和要求 HVCI 的详细信息，请参阅 [基于虚拟化的安全 (VBS) 和虚拟机监控程序强制代码完整性 (要求 hvci) ](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571)。

使用可信启动和 VBS 可以启用 Windows Defender Credential Guard。 此功能可以隔离和保护机密，以便只有特权系统软件可以访问这些机密。 它有助于防止对机密和凭据盗窃攻击（例如传递哈希 (PtH) 攻击）进行未经授权的访问。 有关详细信息，请参阅 [Credential Guard](https://docs.microsoft.com/windows/security/identity-protection/credential-guard/credential-guard)。


## <a name="security-center-integration"></a>安全中心集成

受信任的启动与 Azure 安全中心集成，以确保正确配置 Vm。 Azure 安全中心将持续评估兼容的 Vm 并提出相关的建议。

- **启用安全启动的建议** -此建议仅适用于支持受信任启动的 vm。 Azure 安全中心将标识可以启用安全启动但已禁用的 Vm。 它将发出低严重性建议以启用它。
- **启用 vTPM 的建议** -如果 VM 启用了 vTPM，则 Azure 安全中心可以使用它来执行来宾认证并识别高级威胁模式。 如果 Azure 安全中心识别支持受信任的启动并禁用 vTPM 的 Vm，则会发出低严重性建议来启用它。 
- **证明运行状况评估** -如果 vm 启用了 vTPM，则 Azure 安全中心的扩展可远程验证 VM 是否以正常的方式启动。 这称为远程认证。 Azure 安全中心发出评估，指出远程证明的状态。

## <a name="azure-defender-integration"></a>Azure Defender 集成

如果 Vm 已正确设置为受信任的启动，Azure Defender 可以检测并发出 VM 运行状况问题的警报。

- **Vm 证明失败警报** -Azure Defender 会定期对 vm 执行认证。 VM 启动后也会发生这种情况。 如果证明失败，它将触发中等严重性警报。
    VM 证明可能会因为以下原因而失败：
    - 证明信息（包括启动日志）偏离了可信基线。 这可能表示已加载不受信任的模块，并且 OS 可能会受到威胁。
    - 无法验证该证明报价是否源自证明 VM 的 vTPM。 这可能表示存在恶意软件，并且可能会截获到 vTPM 的流量。
    - VM 上的证明扩展未响应。 这可能表示恶意软件或操作系统管理员拒绝服务攻击。

    > [!NOTE]
    >  此警报适用于启用了 vTPM 且已安装证明扩展的 Vm。 若要传递证明，必须启用安全启动。 如果禁用安全启动，则证明将失败。 如果必须禁用安全启动，则可以禁止显示此警报，以避免误报。

- **不受信任的 Linux 内核模块警报** -启用启用安全启动的可信启动后，即使内核驱动程序验证失败且禁止加载，VM 也可以启动。 如果发生这种情况，Azure Defender 将发出低严重性警报。 尽管没有直接的威胁，因为未加载不受信任的驱动程序，但应调查这些事件。 考虑以下情况：
    - 哪个内核驱动程序失败？ 我熟悉此驱动程序，并希望其加载？
    - 这是我所期待的驱动程序的确切版本吗？ 驱动程序二进制文件是否完整？ 如果这是第三方驱动程序，则供应商是否通过了 OS 符合性测试来对其进行签名？


## <a name="faq"></a>常见问题解答

有关可信发布的常见问题。

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>为什么应使用可信发布？ 受信任的启动防护的情况如何？

受信任的启动会阻止启动包、rootkit 和内核级别的恶意软件。 这种复杂类型的恶意软件在内核模式下运行，并且对用户保持隐藏状态。 例如：
- 固件 rootkit：这些工具包将覆盖虚拟机的 BIOS 的固件，使 rootkit 可在 OS 之前开始。 
- 启动工具包：这些工具包替换操作系统的启动程序，以便虚拟机在 OS 之前加载启动工具包。
- 内核 rootkit：这些工具包替换操作系统内核的一部分，因此，当操作系统加载时，rootkit 会自动启动。
- 驱动程序 rootkit：这些工具包假设是操作系统用来与虚拟机组件进行通信的受信任驱动程序之一。

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>安全引导和标准引导之间的区别是什么？

在安全启动链中，启动过程中的每个步骤都会检查后续步骤的加密签名。 例如，BIOS 将检查加载程序上的签名，并且加载程序将检查它加载的所有内核对象上的签名，等等。 如果任何对象已泄露，则签名将不匹配，并且 VM 将无法启动。 有关详细信息，请参阅[安全启动](/windows-hardware/design/device-experiences/oem-secure-boot)。 测量的启动不会停止启动进程，它测量或计算链中下一个对象的哈希值，并将哈希存储在平台配置中 (PCRs) 在 vTPM 上。 测量的启动记录用于启动完整性监视。

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>检测到完整性错误时会发生什么情况？

监视 Azure 虚拟机的受信任启动以获得高级威胁。 如果检测到此类威胁，则会触发警报。 警报仅在 Azure 安全中心的 [标准层](/azure/security-center/security-center-pricing) 中提供。
Azure 安全中心定期执行证明。 如果证明失败，将触发中等严重性警报。 受信任的启动证明可能会因为以下原因而失败： 
- 证明信息（包括受信任的计算基础 (TCB) 的日志）与受信任的基线 (如启用安全启动时) 。 这可能表示已加载不受信任的模块，并且操作系统可能会受到损害。
- 无法验证该证明报价是否源自证明 VM 的 vTPM。 这可能表示存在恶意软件，并且可能会截获到 TPM 的流量。 
- VM 上的证明扩展未响应。 这可能表示恶意软件或操作系统管理员拒绝服务攻击。

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>受信任的启动与 Hyper-v 受防护的 VM 相比如何？
Hyper-v 受防护的 VM 当前仅在 Hyper-v 上可用。 [Hyper-v 受防护的 VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) 通常与受保护的构造一起部署。 受保护的构造包含主机保护者服务 (HGS) 、一个或多个受保护的主机和一组受防护的 Vm。 Hyper-v 受防护的 Vm 专用于构造，其中必须保护虚拟机的数据和状态，使其免受构造管理员和可能在 Hyper-v 主机上运行的不受信任软件的保护。 另一方面，可以在 Azure 上将受信任的启动部署为独立虚拟机或虚拟机规模集，而无需额外部署和管理 HGS。 可以通过简单的部署代码更改或 Azure 门户上的复选框来启用所有受信任的启动功能。  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>如何将现有 Vm 转换为受信任的启动？
对于第2代 VM，转换为受信任启动的迁移路径的目标是公开上市 (GA) 。

## <a name="next-steps"></a>后续步骤

[使用门户部署受信任的启动 VM](trusted-launch-portal.md)。
