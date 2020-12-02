---
title: 通过固件测量的启动和主机证明-Azure 安全性
description: Azure 固件的技术概述标准启动和主机证明。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498857"
---
# <a name="measured-boot-and-host-attestation"></a>标准启动和主机证明
本文介绍 Microsoft 如何通过测量的启动和主机证明来确保主机的完整性和安全性。

## <a name="measured-boot"></a>标准引导

[受信任的平台模块](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) 是使用受信任的第三方提供的固件的防篡改加密安全审核组件。 启动配置日志包含其平台配置注册中记录的哈希链接度量值 () 主机上一次启动序列时，将在该主机上注册。 下图显示了此录制过程。 以增量方式将以前的哈希度量值添加到下一个度量值的哈希，并在联合上运行哈希算法来完成哈希链。

![显示主机证明服务哈希链接的关系图。](./media/measured-boot-host-attestation/hash-chaining.png)

当主机使用其启动配置日志 (TCGLog) 验证其配置状态时，将完成证明。 伪造启动日志是非常困难的，因为 TPM 不公开读取和扩展操作以外的 PCR 值。 而且，主机证明服务提供的凭据会密封到特定的 PCR 值。 使用哈希链使其无法进行计算，以便在带外欺骗或解封凭据。

## <a name="host-attestation-service"></a>主机证明服务

主机证明服务是一种预防措施，在允许主机计算机与客户数据或工作负荷交互之前，检查主机是否可信。 主机证明服务检查的方法是，通过验证符合性声明的主机的符合性声明 (可验证的主机的符合性证明) 安全状态) 的证明策略 (定义。 TPM 提供的 [信任根](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) 提供此系统的完整性。

主机证明服务在专用锁定环境中的每个 Azure 群集中都存在。 锁定的环境包括参与主机启动协议的其他守卫服务。  (PKI) 的公钥基础结构充当用于验证证明请求的 provenance 的中介，并作为标识颁发者 (在成功的主机证明) 时获得。 颁发给证明主机的后期证明凭据会密封为其标识。 只有发出请求的主机才能解封凭据并利用它们来获取增量权限。 这可以防止中间人攻击和哄骗攻击。

如果 Azure 主机在工厂中由于安全配置错误或已被篡改，则其 TCGLog 包含在下一次证明时由主机证明服务标记的折衷指标，这会导致证明失败。 证明失败会阻止 Azure 汽油信任有问题的主机。 此防护有效地阻止与主机之间的所有通信，并触发事件工作流。 进行调查，并进行详细的事后分析来确定根本原因和任何可能的折衷迹象。 仅在分析完成后，主机经过修正，并有机会加入 Azure 汽油并接管客户工作负荷。

下面是主机证明服务的高级体系结构：

![显示主机证明服务体系结构的关系图。](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>证明度量

下面是今天捕获的许多度量值的示例。

### <a name="secure-boot-and-secure-boot-keys"></a>安全启动和安全启动密钥
通过验证签名数据库和吊销的签名数据库摘要是否正确，主机证明服务可确保客户端代理认为合适的软件是受信任的。 通过验证公钥注册密钥数据库和公共平台密钥的签名，主机证明服务会确认只有可信方有权修改被视为受信任的软件的定义。 最后，通过确保安全启动处于活动状态，主机证明服务会强制验证这些定义。

### <a name="debug-controls"></a>调试控件
调试器是面向开发人员的强大工具。 但是，如果提供给不受信任的参与方，自由的内存和其他调试命令的访问可能会降低数据保护和系统的完整性。 在生产计算机上启动时，主机证明服务可确保禁用任何类型的调试。

### <a name="code-integrity"></a>代码完整性
UEFI [安全启动](secure-boot.md) 可以确保只有受信任的低级别软件可以在启动顺序中运行。 不过，相同的检查还必须在启动后环境中应用到使用内核模式访问的驱动程序和其他可执行文件。 为此， (CI) 策略的代码完整性用于通过指定有效和无效的签名来定义哪些驱动程序、二进制文件和其他可执行文件被视为受信任。 强制实施这些策略。 策略违规会生成警报到安全事件响应团队进行调查。

## <a name="next-steps"></a>后续步骤
若要详细了解如何驱动平台的完整性和安全性，请参阅：

- [固件安全性](firmware.md)
- [安全启动](secure-boot.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)