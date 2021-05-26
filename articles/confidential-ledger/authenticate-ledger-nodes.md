---
title: 对 Azure 机密账本节点进行身份验证
description: 对 Azure 机密账本节点进行身份验证
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 0a8f033aa540d2a469141c4c529981a0cbeef3ec
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384929"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>对 Azure 机密账本节点进行身份验证

可以由代码示例和用户对 Azure 机密账本节点进行身份验证。

## <a name="code-samples"></a>代码示例

初始化时，代码示例会通过查询标识服务来获取节点证书。 检索节点证书后，代码示例将查询账本网络以获取 Quote，然后系统会使用主机验证二进制文件对该 Quote 进行验证。 如果验证成功，代码示例将继续执行账本操作。

## <a name="users"></a>用户

用户可以验证机密账本节点的真实性，以确认它们确实正在与账本的 Enclave 交互。 可以通过几种方式在机密账本节点中建立信任，这些信任可以叠加起来以提高整体置信度。 因此，步骤 1 到 2 可以帮助在初始 TLS 握手和函数工作流中的身份验证过程中在该机密账本 Enclave 中建立信任。 除此之外，用户的客户端和机密账本之间会维护持久性客户端连接。

- 验证机密账本节点：这通过查询 Microsoft 托管的标识服务来完成，该服务提供网络证书，因此有助于验证账本节点是否提供由该特定实例的网络证书背书/签名的证书。 与基于 PKI 的 HTTPS 类似，服务器的证书由已知的证书颁发机构 (CA) 或中间 CA 签名。 对于机密账本，CA 证书由标识服务以网络证书的形式返回。对于机密账本用户来说，这是建立置信度的重要措施。 如果此节点证书未由返回的网络证书签名，则客户端连接应该会失败（如示例代码中所实现）。
- 验证机密账本 Enclave：机密账本在由 Quote 表示的 Intel® SGX Enclave 中运行，Quote 是在该 Enclave 中生成的数据 Blob。 任何其他实体都可以使用 Quote，以验证 Quote 是否已从在 Intel® SGX 的保护下运行的应用程序生成。 Quote 以有助于轻松验证的方式结构化。 它包含一些声明，这些声明有助于识别 Enclave 的各种属性及其正在运行的应用程序。 对于机密账本用户来说，这是建立置信度的重要机制。 这可以通过调用函数工作流 API 以获取 Enclave Quote 来完成。 如果 Quote 无效，则客户端连接应该会失败。 然后，可以使用 open_enclaves Host_Verify 工具来验证检索到的 Quote。 在此处可以找到关于此内容的更多详细信息。

## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)
- [Azure 机密账本体系结构](architecture.md)
