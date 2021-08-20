---
title: Azure 证明概述
description: 简要介绍了 Microsoft Azure 证明，这是一种证明受信任的执行环境 (TEE) 的解决方案
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: fe025fad4955095ef16b546b7d326d80b4aea15c
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112427574"
---
# <a name="microsoft-azure-attestation"></a>Microsoft Azure 证明 

Microsoft Azure 证明是一个统一的解决方案，用于远程验证平台的可信度和在该平台中运行的二进制文件的完整性。 该服务支持对受信任的平台模块 (TPM) 支持的平台的证明，以及对受信任执行环境 (TEE) 的状态进行证明的功能，例如 [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) enclave 和[基于虚拟化的安全性](/windows-hardware/design/device-experiences/oem-vbs) (VBS) enclave。 

证明是演示如何在受信任的平台上正确实例化软件二进制文件的过程。 然后，远程信赖方可以确信只有此类软件在受信任的硬件上运行。 Azure 证明是一个统一的面向客户的认证服务和框架。

Azure 证明使用先进的安全范例，如 [Azure 机密计算](../confidential-computing/overview.md)和智能边缘保护。 客户一直在请求独立验证计算机位置、计算机上的虚拟机 (VM) 状态以及在该 VM 上运行 enclave 的环境的功能。 Azure 证明将满足这些请求以及诸多其他客户请求。

Azure 证明从计算实体接收证据，将它们转换为一组声明，根据可配置的策略对其进行验证，并为基于声明的应用（例如信赖方和审核机构）生成加密证明。

## <a name="use-cases"></a>用例

Azure 证明为多种环境和各种不同的用例提供全面的证明服务。

### <a name="sgx-attestation"></a>SGX 证明

SGX 指硬件级隔离，这只在一些英特尔 CPU 型号上受支持。 SGX 使代码可以在清理过的隔离舱（称为 SGX enclave）中运行。 然后，通过硬件管理访问和内存权限，利用适当的隔离将攻击面降至最小。

通过委派在这些 enclave 内执行的安全敏感任务，可以将客户端应用程序设计为利用 SGX enclave。 这样，此类应用程序就可以利用 Azure 证明定期在 enclave 中建立信任并访问敏感数据。

英特尔® 至强® 可扩展处理器仅支持[基于 ECDSA 的证明解决方案](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html#Elliptic%20Curve%20Digital%20Signature%20Algorithm%20(ECDSA)%20Attestation)以远程方式证明 SGX enclave。 利用基于 ECDSA 的证明模型，Azure 证明支持对英特尔® 至强® E3 处理器和基于英特尔® 至强® 可扩展处理器的服务器平台的验证。 

> [!NOTE]
> 若要使用 Azure 证明执行对基于英特尔® 至强® 可扩展处理器的服务器平台的证明，用户需要安装 [Azure DCAP 版本 1.10.0](https://github.com/microsoft/Azure-DCAP-Client) 或更高版本。

### <a name="open-enclave"></a>开放式 Enclave
[开放式 Enclave](https://openenclave.io/sdk/) (OE) 是一系列库，旨在创建一个统一的 enclaving 抽象，供开发人员生成基于 TEE 的应用程序。 它提供了一个可最大程度降低平台特异性的通用安全应用模型。 Microsoft 将其视为普及基于硬件的 enclave 技术（例如 SGX）和增加它们在 Azure 上的使用的方法。

OE 使 enclave 证据的特定验证要求实现了标准化。 因此，OE 有资格作为非常适合 Azure 证明的证明使用者。

### <a name="tpm-attestation"></a>TPM 证明 

基于受信任的平台模块 (TPM) 的证明对于证明平台状态至关重要。 TPM 用作信任根和安全协处理器，为指标（证据）提供加密有效性。 具有 TPM 的设备可以依靠证明来证明启动完整性未受损害，并且可以使用声明来检测启动期间的功能启用状态。 

客户端应用程序可设计为利用 TPM 认证，方法是将安全敏感的任务指定为仅在验证平台安全后才执行。 这样，此类应用程序就可以利用 Azure 证明定期在平台中建立信任并访问敏感数据。

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure 证明可以在 TEE 中运行

Azure 证明对于机密计算场景至关重要，因为它可执行以下操作：

- 验证 enclave 证据是否有效。
- 根据客户定义的策略评估 enclave 证据。
- 管理和存储特定于租户的策略。
- 生成并签署信赖方用来与 enclave 交互的令牌。

Azure 证明可在两类环境中运行：
- 在启用了 SGX 的 TEE 中运行的 Azure 证明。
- 在非 TEE 中运行的 Azure 证明。

Azure 证明客户已经表达了一个要求，那就是希望 Microsoft 在操作上脱离可信计算基 (TCB)。 这是为了防止 Microsoft 实体（例如 VM 管理员、主机管理员和 Microsoft 开发人员）修改证明请求、策略和 Azure 证明颁发的令牌。 Azure 证明还设计为在 TEE 中运行，在这种情况下，Azure 证明的功能（如 Quote 验证、令牌生成和令牌签名）会移到 SGX enclave 中。

## <a name="why-use-azure-attestation"></a>为什么使用 Azure 证明

Azure 证明是用于证明 TEE 的首选服务，因为它具有以下优势： 

- 统一的框架，可证明多种环境，如 TPM、SGX enclave 和 VBS enclave 
- 允许创建自定义证明提供程序和配置策略来限制令牌生成
- 提供区域共享提供程序，它们无需用户配置即可进行证明
- 可在与 SGX enclave 中的实现一起使用时保护其数据
- 高度可用的服务 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>业务连续性和灾难恢复 (BCDR) 支持

Azure 证明的[业务连续性和灾难恢复](../best-practices-availability-paired-regions.md) (BCDR) 支持可减少因某个区域出现重大的可用性问题或灾难事件而导致的服务中断。

在正常情况下，部署在两个区域的群集会独立运行。 如果某个区域出现故障或中断，则会发生以下情况：

- Azure 证明 BCDR 将提供无缝故障转移，客户无需执行任何额外的步骤即可恢复
- 该区域的 [Azure 流量管理器](../traffic-manager/index.yml)会检测运行状况探测是否已降级，并会将终结点切换到配对区域
- 现有连接将不起作用，并将收到内部服务器错误或遇到超时问题
- 所有控制平面操作都将被阻止。 客户将无法在主要区域中创建证明提供程序
- 所有数据平面操作（包括证明调用和策略配置）将由次要区域提供服务。 客户可以继续使用与主要区域对应的原始 URI 进行数据平面操作

## <a name="next-steps"></a>后续步骤
- 了解 [Azure 证明基本概念](basic-concepts.md)
- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
