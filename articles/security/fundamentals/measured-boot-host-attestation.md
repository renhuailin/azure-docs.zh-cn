---
title: 固件测量启动和主机证明 - Azure 安全性
description: Azure 固件测量启动和主机证明技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: 6dac3b8230a0bad9c6492b236eac5dddc1839471
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895771"
---
# <a name="measured-boot-and-host-attestation"></a>测量启动和主机证明
本文介绍 Microsoft 如何通过测量启动和主机证明来确保主机的完整性和安全性。

## <a name="measured-boot"></a>度量的启动

[受信任的平台模块](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) 使用受信任的第三方提供的固件，是一种防篡改加密安全审核组件。 启动配置日志包含主机上次经历启动序列时，在其平台配置寄存器 (PCR) 中记录的哈希链接的度量值。 下图显示了此记录过程。 以增量方式将以前的哈希度量值添加到下一个度量值的哈希，并在该并集上运行哈希算法，可完成哈希链接。

![此图显示了主机证明服务哈希链接。](./media/measured-boot-host-attestation/hash-chaining.png)

当主机使用其启动配置日志 (TCGLog) 提供其配置状态的证明时，即完成证明。 伪造启动日志是非常困难的，因为 TPM 不公开读取和扩展操作以外的 PCR 值。 而且，主机证明服务提供的凭据会密封到特定的 PCR 值。 使用哈希链接，使得在带外假冒或解封凭据的计算难以实现。

## <a name="host-attestation-service"></a>主机证明服务

主机证明服务是一种预防措施，它将检查主机是否可信，再允许可信的主机计算机与客户数据或工作负载交互。 主机证明服务通过验证每个主机发送的合规性声明（主机合规性的可验证证明）是否符合证明策略（安全状态的定义）来进行检查。 此系统的完整性由 TPM 提供的[信任根](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf)确保。

主机证明服务存在于专用锁定环境中的每个 Azure 群集中。 锁定环境包括主机启动协议涉及的其他守卫服务。 公钥基础结构 (PKI) 充当用于验证证明请求出处的中介，还充当标识颁发者（取决于成功的主机证明）。 颁发给证明主机的证明后凭据会密封到其标识。 只有发出请求的主机才能解封凭据并利用它们来获取增量权限。 这样可以防止中间人攻击和欺诈攻击。

如果 Azure 主机来自安全配置错误的工厂或在数据中心内被篡改，则其 TCGLog 在下一次证明时将包含由主机证明服务标记的泄露标志，这将导致证明失败。 证明失败会使 Azure 机组不信任有攻击性的主机。 此防护可有效阻止与主机之间的所有通信，并触发事件工作流。 将进行调查和详细的事后分析，来确定根本原因和任何可能的泄露迹象。 只有在分析完成后，才会修正主机，并使其有机会加入 Azure 机组并接管客户工作负载。

主机证明服务的大概体系结构如下：

![此图显示了主机证明服务体系结构。](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>证明度量值

下面是今天捕获的许多度量值的示例。

### <a name="secure-boot-and-secure-boot-keys"></a>安全启动和安全启动密钥
通过验证签名数据库和已吊销的签名数据库摘要是否正确，主机证明服务可以确保客户端代理信任适当的软件。 通过验证公钥注册密钥数据库和公共平台密钥的签名，主机证明服务可以确认只有受信任的参与方有权修改受信任软件的定义。 最后，通过确保安全启动处于活动状态，主机证明服务可以验证是否正在强制执行这些定义。

### <a name="debug-controls"></a>调试控件
调试器是面向开发人员的强大工具。 但是，由于可以不受限制地访问内存和其他调试命令，因此如果将其提供给不受信任的参与方，则可能弱化数据保护和系统完整性。 主机证明服务可以确保在生产计算机上启动时禁用任何类型的调试。

### <a name="code-integrity"></a>代码完整性
UEFI [安全启动](secure-boot.md)可确保只有受信任的低级别软件可以在启动序列中运行。 不过，还必须对启动后环境中具有内核模式访问权限的驱动程序和其他可执行文件应用相同的检查。 为此，可以通过指定有效和无效的签名，使用代码完整性 (CI) 策略来定义应信任的驱动程序、二进制文件和其他可执行文件。 强制实施这些策略。 违反策略会生成警报，并发送给安全事件响应团队进行调查。

## <a name="next-steps"></a>后续步骤
若要详细了解为提高平台完整性和安全性而做的工作，请参阅：

- [固件安全性](firmware.md)
- [平台代码完整性](code-integrity.md)
- [安全启动](secure-boot.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)