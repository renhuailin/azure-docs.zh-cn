---
title: 预览：Azure VM 的受信任启动
description: 了解 Azure 虚拟机的受信任启动。
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 0e86c503fb016f79097d9ddde1d30f4314942870
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695287"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Azure 虚拟机的受信任启动（预览版）

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集

Azure 提供受信任启动作为一种无缝提高[第 2 代](generation-2.md) VM 安全性的方式。 受信任启动能够防范具有持续性的高级攻击手法。 受信任启动由多种可单独启用的协调式基础结构技术组成。 每种技术都针对错综复杂的威胁提供另一层防御。

> [!IMPORTANT]
> 受信任启动要求创建新的虚拟机。 如果现有的虚拟机在最初创建时未配置受信任启动，则无法在其上启用受信任启动。
>
> 受信任启动目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
>
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="benefits"></a>好处 

- 安全部署其启动加载程序、OS 内核和驱动程序已经过验证的虚拟机。
- 安全保护虚拟机中的密钥、证书和机密。
- 获取整个启动链的完整性的见解和置信度。
- 确保工作负载受信任且可验证。

## <a name="public-preview-limitations"></a>公共预览版限制

**大小支持**：
- B 系列
- Dav4 系列、Dasv4 系列
- DCsv2 系列
- Dv4 系列、Dsv4 系列、Dsv3 系列、Dsv2 系列
- Ddv4 系列、Ddsv4 系列
- Fsv2 系列
- Eav4 系列和 Easv4 系列
- Ev4 系列、Esv4 系列、Esv3 系列
- Edv4 系列、Edsv4 系列
- Lsv2 系列

**OS 支持**：
- Redhat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 专业版
- Windows 10 企业版
- Windows 10 Enterprise 多会话

**区域**： 
- 美国中部
- 美国东部
- 美国东部 2
- 美国中北部
- 美国中南部
- 美国西部
- 美国西部 2
- 北欧
- 西欧
- 日本东部
- 东南亚

**定价**：按现有 VM 定价收费，无额外费用。

**此预览版不支持以下功能**：
- 备份
- Azure Site Recovery
- 共享的映像库
- 临时 OS 磁盘
- 共享磁盘
- 托管映像
- Azure 专用主机 

## <a name="secure-boot"></a>安全启动

受信任启动的根本作用是使 VM 安全启动。 此模式在平台固件中实现，可以防止安装基于恶意软件的 Rootkit 和 Bootkit。 安全启动旨在确保只有已签名的操作系统和驱动程序能够启动。 它为 VM 上的软件堆栈建立“信任根”。 启用安全启动后，所有 OS 启动组件（启动加载程序、内核、内核驱动程序）都必须由受信任的发布者签名。 Windows 和某些 Linux 发行版都支持安全启动。 如果安全启动无法对受信任发布者签名的映像进行身份验证，则不允许启动 VM。 有关详细信息，请参阅[安全启动](/windows-hardware/design/device-experiences/oem-secure-boot)。

## <a name="vtpm"></a>vTPM

受信任启动还引入了适用于 Azure VM 的 vTPM。 vTPM 是虚拟化版本的[受信任平台模块](/windows/security/information-protection/tpm/trusted-platform-module-overview)硬件，符合 TPM2.0 规范。它充当密钥和度量的专用安全保管库。 受信任启动将其自身专用的 TPM 实例提供给 VM，该实例在安全环境中运行，任何 VM 都无法访问它。 vTPM 通过度量 VM 的整个启动链（UEFI、OS、系统和驱动程序）来启用[证明](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation)。 

受信任启动使用 vTPM 通过云执行远程证明。 此功能可用于平台运行状况检查以及做出基于信任的决策。 作为一项运行状况检查，受信任启动能够以加密方式验证 VM 是否正常启动。 如果验证过程失败（原因可能是 VM 正在运行未经授权的组件），Azure 安全中心将发出完整性警报。 这些警报包含有关哪些组件未能通过完整性检查的详细信息。

## <a name="virtualization-based-security"></a>基于虚拟化的安全功能

[基于虚拟化的安全性](/windows-hardware/design/device-experiences/oem-vbs) (VBS) 使用虚拟机监控程序创建安全且隔离的内存区域。 Windows 使用这些区域来运行各种安全解决方案，以针对漏洞和恶意攻击增强防范。 受信任启动允许启用虚拟机监控程序代码完整性 (HVCI) 和 Windows Defender Credential Guard。

HVCI 是功能强大的系统缓解机制，可以防范在 Windows 内核模式进程中注入和执行恶意代码或未经验证的代码。 在运行内核模式驱动程序和二进制文件之前，HVCI 会对其进行检查，防止未签名的文件载入内存中。 这可以确保在允许加载此类可执行代码之后，无法对其进行修改。 有关 VBS 和 HVCI 的详细信息，请参阅[基于虚拟化的安全性 (VBS) 和虚拟机监控程序强制实施的代码完整性 (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571)。

可以结合受信任启动和 VBS 启用 Windows Defender Credential Guard。 此功能可以隔离和保护机密，确保只有特权系统软件能够访问这些机密。 它有助于防止未经授权访问机密，以及遭到凭据盗窃攻击，例如哈希传递 (PtH) 攻击。 有关详细信息，请参阅 [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard)。


## <a name="security-center-integration"></a>安全中心集成

受信任启动与 Azure 安全中心集成，确保正确配置 VM。 Azure 安全中心将持续评估兼容的 VM 并提出相关建议。

- **有关启用安全启动的建议** - 此项建议仅适用于支持受信任启动的 VM。 Azure 安全中心将识别可以启用安全启动、但已禁用安全启动的 VM。 它将发出一条启用安全启动的低严重性建议。
- **有关启用 vTPM 的建议** - 如果 VM 上启用了 vTPM，Azure 安全中心可以使用 vTPM 来执行来宾证明并识别高级威胁模式。 如果 Azure 安全中心识别到支持受信任启动、但已禁用 vTPM 的 VM，它将发出一条启用 vTPM 的低严重性建议。 
- **证明运行状况评估** - 如果 VM 上启用了 vTPM，Azure 安全中心的某个扩展可以远程验证 VM 是否以正常方式启动。 这称为远程证明。 Azure 安全中心将发出评估，指明远程证明的状态。

## <a name="azure-defender-integration"></a>Azure Defender 集成

如果在 VM 上正确设置了受信任启动，Azure Defender 可以检测并提醒 VM 运行状况问题。

- **VM 证明失败警报** - Azure Defender 定期对 VM 执行证明。 VM 启动后也会发生执行此操作。 如果证明失败，将触发中等严重性警报。
    VM 证明可能出于以下原因而失败：
    - 证明的信息（包括启动日志）与受信任基线相背离。 这可能表示加载了不受信任的模块，并且 OS 可能已遭入侵。
    - 无法验证证明引述是否源自被证明 VM 的 vTPM。 这可能表示存在恶意软件，它可能正在截获发送到 vTPM 的流量。
    - VM 上的“证明”扩展未做出响应。 这可能表示恶意软件或 OS 管理员发起了拒绝服务攻击。

    > [!NOTE]
    >  此警报适用于已启用 vTPM 并已安装“证明”扩展的 VM。 若要通过证明，必须启用安全启动。 如果禁用安全启动，证明将会失败。 如果必须禁用安全启动，可以禁止显示此警报以避免误报。

- 针对不受信任的 Linux 内核模块的警报 - 对于启用了安全启动的受信任启动，即使内核驱动程序没有通过验证并被禁止加载，VM 也可以启动。 如果发生这种情况，Azure Defender 将发出低严重性警报。 尽管不会遭受直接威胁（因为不受信任的驱动程序尚未加载），但应该调查这些事件。 考虑以下情况：
    - 哪个内核驱动程序失败？ 我是否知悉此驱动程序，并且已意料到会加载它？
    - 它是否为我确切预期的驱动程序版本？ 驱动程序二进制文件是否未经改动？ 如果它是第三方驱动程序，供应商是否通过了 OS 合规性测试，可对其进行签名？


## <a name="faq"></a>常见问题解答

有关受信任启动的常见问题。

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>为何应使用受信任启动？ 受信任启动防范哪些行为？

受信任启动防范 Bootkit、Rootkit 和内核级恶意软件。 这种复杂类型的恶意软件在内核模式下运行，对用户是隐身的。 例如：
- 固件 Rootkit：这些工具包可覆盖虚拟机 BIOS 的固件，因此 Rootkit 可以在 OS 启动之前启动。 
- Bootkit：这些工具包可替换 OS 的启动加载程序，使虚拟机在 OS 启动之前加载 Bootkit。
- 内核 Rootkit：这些工具包可替换 OS 内核的一部分，因此，Rootkit 可在 OS 加载时自动启动。
- 驱动程序 Rootkit：这些工具包假装是 OS 用来与虚拟机组件通信的受信任驱动程序之一。

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>安全启动与受度量启动之间的区别是什么？

在安全启动链中，启动过程中的每个步骤都会检查后续步骤的加密签名。 例如，BIOS 将检查加载程序中的签名，而加载程序将检查它所加载的所有内核对象中的签名，依此类推。 如有任何对象泄密，则签名将不匹配，VM 也就不会启动。 有关详细信息，请参阅[安全启动](/windows-hardware/design/device-experiences/oem-secure-boot)。 受度量启动不会停止启动过程，它会度量或计算链中后续对象的哈希，并将哈希存储在 vTPM 上的平台配置寄存器 (PCR) 中。 受度量启动记录用于启动完整性监视。

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>检测到完整性错误时会发生什么情况？

Azure 虚拟机的受信任启动将受到监视，以识别高级威胁。 如果检测到此类威胁，将触发警报。 警报仅在 Azure 安全中心的[标准层](../security-center/security-center-pricing.md)中提供。
Azure 安全中心定期执行证明。 如果证明失败，将触发中等严重性警报。 受信任启动证明可能出于以下原因而失败： 
- 证明的信息（包括受信任计算基础 (TCB) 的日志）偏离受信任基线（如启用安全启动时）。 这可能表示加载了不受信任的模块，并且 OS 可能已遭入侵。
- 无法验证证明引述是否源自被证明 VM 的 vTPM。 这可能表示存在恶意软件，它可能正在截获发送到 TPM 的流量。 
- VM 上的证明扩展未做出响应。 这可能表示恶意软件或 OS 管理员发起了拒绝服务攻击。

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>受信任启动与 Hyper-V 防护的 VM 有何差别？
Hyper-V 防护的 VM 目前仅在 Hyper-V 上可用。 [Hyper-V 防护的 VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) 通常与 Guarded Fabric 一同部署。 Guarded Fabric 由一个主机保护服务 (HGS)、一个或多个受保护的主机以及一组受防护的 VM 组成。 Hyper-V 防护的 VM 适合在下述结构中使用：其中的虚拟机数据和状态必须受到保护，避免结构管理员和可能在 Hyper-V 主机上运行的不受信任软件对其进行入侵。 另一方面，受信任启动可作为独立的虚拟机或虚拟机规模集部署在 Azure 上，而无需额外的 HGS 部署和管理。 只需在部署代码中做出一项简单更改，或者在 Azure 门户上选中相应的复选框，即可启用所有的受信任启动功能。  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>如何将现有 VM 转换为受信任启动？
对于第 2 代 VM，转换为受信任启动的迁移路径面向正式版 (GA)。

## <a name="next-steps"></a>后续步骤

[使用门户部署受信任启动 VM](trusted-launch-portal.md)。
