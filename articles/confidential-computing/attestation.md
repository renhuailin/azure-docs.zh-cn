---
title: 在 Azure 中证明领地
description: 了解如何使用证明来验证你的机密计算受信任环境是否安全
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551345"
---
# <a name="attesting-sgx-enclaves"></a>证明 SGX Enclave

Azure 上的机密计算提供基于 Intel SGX 的虚拟机，该虚拟机可隔离部分代码或数据。 使用这些[领地](confidential-computing-enclaves.md)时，你需要验证受信任环境是否安全。 这种验证是一个证明过程。 

## <a name="overview"></a>概述 

通过证明，信赖方可以更加确信其软件：(1) 在领地中运行；(2) 领地是最新且安全的。 例如，领地请求底层硬件生成一个凭据，该凭据可以证明该领地在平台上存在。 然后，可以向另一个领地提供报告，验证该报告是否是在同一平台上生成的。

![证明领地中的代码](media/attestation/attestation.png)



必须使用与系统软件和芯片兼容的安全证明服务来实施证明。 可使用的一些服务示例包括

- [Microsoft Azure 证明（预览版）](../attestation/overview.md)或
- [Intel 的证明和预配服务](https://software.intel.com/sgx/attestation-services)


这两者都与 Azure 机密计算 Intel SGX 基础结构兼容。 

## <a name="next-steps"></a>后续步骤
尝试[领地感知应用的 Microsoft Azure 证明示例](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)。