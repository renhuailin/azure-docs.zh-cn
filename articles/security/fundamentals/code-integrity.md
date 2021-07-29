---
title: 平台代码完整性 - Azure 安全性
description: 了解 Microsoft 如何确保只有授权软件能够运行。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/10/2021
ms.openlocfilehash: e9a58bf83181352adb3894fb159fd212f0f1b7ca
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063588"
---
# <a name="platform-code-integrity"></a>平台代码完整性

运行复杂系统（如 Microsoft Azure）时的一个挑战是确保只有授权软件能够在系统中运行。 未经授权的软件会给任何企业带来多种风险：

- 安全风险，例如专用攻击工具、自定义恶意软件和具有已知漏洞的第三方软件
- 如果没有使用经过批准的变更管理过程来引入新软件，便存在合规性风险
- 来自外部开发的软件的质量风险，可能不符合业务的运营要求

在 Azure 中，我们面临相同的挑战，并且相当复杂。 我们有数千台服务器，运行由数千名工程师开发和维护的软件。 这形成了一个大型攻击面，无法单独通过业务流程进行管控。

## <a name="adding-an-authorization-gate"></a>添加授权门

Azure 使用丰富的工程流程，实现所部署软件的安全性、合规性和质量的入口。 此过程包括对源代码的访问控制、执行同行代码评审、执行安全漏洞的静态分析、遵循 Microsoft 的[安全开发生命周期](https://www.microsoft.com/securityengineering/sdl/) (SDL) 以及执行功能和质量测试。 我们需要保证我们部署的软件已经完成了此流程。 代码完整性可帮助我们实现此保证。

## <a name="code-integrity-as-an-authorization-gate"></a>作为授权门的代码完整性

代码完整性是一种内核级别服务，从 Windows Server 2016 开始提供。 每当加载驱动程序或动态链接库 (DLL)、执行可执行二进制文件或运行脚本时，代码完整性都可以应用严格的执行控制策略。 Linux 存在类似的系统，例如 [DM-Verity](https://www.kernel.org/doc/html/latest/admin-guide/device-mapper/verity.html)。 代码完整性策略由一组授权指示器（代码签名证书或 [SHA256](https://en.wikipedia.org/wiki/Secure_Hash_Algorithms) 文件哈希）组成，内核在加载或执行二进制文件或脚本之前匹配这些指示器。

代码完整性允许系统管理员定义一项策略，这项策略只会授权由特定证书签名或匹配指定 SHA256 哈希的二进制文件或脚本。 内核实行此策略的方式是阻止执行不符合设定策略的所有内容。

代码完整性策略的一个关注点是，除非策略完全正确，否则便会阻止生产中的关键软件并造成中断。 考虑到这一点，你可能会问，在执行了未经授权的软件时，为什么使用安全监视进行检测并不足够。 代码完整性具有审核模式，该模式可在未经授权的软件运行时发出警报，而不是阻止执行。 警报当然可以在解决合规性风险方面增加很多价值，但对于勒索软件或自定义恶意软件等安全风险，是你充分保护还是敌人在您的设备群中站稳脚跟，区别可能就在于延迟几秒的响应。 在Azure中，我们投入了大量资金来管理代码完整性风险，避免任何导致影响客户的中断。

## <a name="build-process"></a>生成过程

如上所述，Azure 编译系统具有一组丰富的测试，可确保软件变更安全且合规。 编译通过验证后，编译系统会使用 Azure 生成证书对生成进行签名。 证书指示编译已通过整个变更管理过程。 编译经过的最后一项测试称为“代码签名验证 (CSV)”。 CSV 确认新编译的二进制文件符合代码完整性策略，然后我们才能将其部署到生产中。 这让我们非常确信，我们不会由于签名错误的二进制文件而造成对客户有影响的中断。 如果 CSV 发现问题，编译将中断，将安排相关工程师调查和解决问题。

## <a name="safety-during-deployment"></a>部署期间的安全性

即使我们针对每个编译执行 CSV，生产中的某些编译或不一致仍可能导致与代码完整性相关的中断。 例如，计算机可能正在运行旧版本的代码完整性策略，或者可能处于不正常状态，导致代码完整性误报。 （在 Azure 尺度上，我们已了解所有这些情况。）因此，我们需要继续防范部署期间发生中断的风险。

Azure 中的所有变更都需要通过一系列阶段才会部署。 第一个阶段是内部 Azure 测试实例。 接下来的一个阶段仅用于为其他 Microsoft 产品团队提供服务。 最后一个阶段为第三方客户提供服务。 部署变更时，变更将依次经过每个阶段，并暂停以测量阶段的运行状况。 如果发现变更没有负面影响，则将进入下一阶段。 如果我们对代码完整性策略进行错误的变更，则在此分阶段部署期间检测到变更并回滚。

## <a name="incident-response"></a>事件响应

即使使用这种分层保护，机器群中的某些服务器仍可能会阻止正确授权的软件，并引发客户所面临的问题，这是最糟糕的情况之一。 我们的最后一个防御层是人工调查。 每次代码完整性阻止文件时，都引发警报，让值班工程师进行调查。 警报让我们能够开始安全调查和干预，无论问题是表示真实攻击、误报或其他影响客户的情况。 这最大限度地减少了缓解任何代码完整性相关问题所花费的时间。  

## <a name="next-steps"></a>后续步骤

了解 [Windows 10](/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control) 如何使用配置的代码完整性。

若要详细了解为提高平台完整性和安全性而做的工作，请参阅：

- [固件安全性](firmware.md)
- [安全启动](secure-boot.md)
- [测量启动和主机证明](measured-boot-host-attestation.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)
- [虚拟机监控程序安全性](hypervisor.md)