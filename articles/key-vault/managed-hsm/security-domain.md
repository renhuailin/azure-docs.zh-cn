---
title: 关于 Azure 托管 HSM 安全域
description: 概述托管 HSM 安全域，即恢复托管 HSM 所需的一组核心凭据
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.openlocfilehash: b309b4e9189c551ed40e0dac1ad45de47f7632d6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443550"
---
# <a name="about-the-managed-hsm-security-domain"></a>关于托管 HSM 安全域

托管 HSM 安全域 (SD) 是在发生灾难时恢复托管 HSM 所需的一组核心凭据。 安全域在托管 HSM 硬件和服务软件领地中生成，表示 HSM 的“所有权”。

每个托管 HSM 都必须具有一个要运行的安全域。 在请求时新的托管 HSM 时会预配安全域，但是直到下载安全域时才会将其激活。 当托管 HSM 处于已预配但未激活的状态时，可以通过以下两种方式将其激活：
- 下载安全域是默认方法，此方法使你可以安全存储安全域，以与另一个托管 HSM 一起使用或从总体灾难恢复。
- 上传已经拥有的现有安全域，这使你能够创建多个共享同一安全域的托管 HSM 实例。

## <a name="download-your-security-domain"></a>下载安全域

如果已预配托管 HSM 但尚未激活，则下载安全域将捕获从所有硬件完全丢失中恢复所需的核心凭据。 若要下载安全域，必须创建至少 3 个（最多 10 个）RSA 密钥对，并在请求下载安全域时将这些公钥发送到该服务。 你还需要指定将来解密安全域所需的最小密钥数（仲裁）。 托管 HSM 将初始化安全域，并通过使用 [Shamir 的机密共享算法](https://dl.acm.org/doi/10.1145/359168.359176)提供的公钥对其进行加密。 只有在至少有可用的私钥仲裁时，才可以对下载的安全域进行解密。因此你必须安全保存私钥，确保安全域的安全性。 下载完成后，托管 HSM 将处于可供使用的激活状态。  

> [!IMPORTANT]
> 对于完全灾难恢复，你必须具有安全域和用于对其进行保护的私钥仲裁，以及完整的 HSM 备份。 如果因为丢失安全域或足量的 RSA 密钥（私钥）而丢失仲裁，并且没有运行中的托管 HSM 实例，那么将无法进行灾难恢复。

## <a name="upload-a-security-domain"></a>上传安全域

如果已预配托管 HSM 但尚未激活，则可以启动安全域恢复进程。 托管 HSM 将生成 RSA 密钥对，并返回公钥。 然后可以将安全域上传到托管 HSM。 在上传之前，需要为客户端（Azure CLI 或 PowerShell）提供下载安全域时所使用的私钥最小仲裁数。 客户端将使用此仲裁解密安全域，并使用请求恢复时下载的公钥对其进行重新加密。 上传完成后，托管 HSM 将处于激活状态。

## <a name="backup-and-restore-behavior"></a>备份和还原行为

只有在源托管 HSM（创建备份的位置）和目标托管 HSM（还原备份的位置）共享同一安全域时，才可能成功还原备份（完整备份或单个密钥备份）。 通过此方式，安全域还会为每个托管 HSM 定义加密边界。

## <a name="next-steps"></a>后续步骤

- 请参阅[托管 HSM 概述](overview.md)
- 了解如何[使用 Azure CLI 管理托管 HSM](key-management.md)
- 查看[托管 HSM 最佳做法](best-practices.md)
