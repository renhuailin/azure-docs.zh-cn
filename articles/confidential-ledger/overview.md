---
title: Microsoft Azure 机密账本概述
description: 概述 Azure 机密账本，这是用于管理敏感数据记录的高度安全的服务
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 7ea55c3f21664d504366657a653b5e3598666710
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385058"
---
# <a name="microsoft-azure-confidential-ledger-preview"></a>Microsoft Azure 机密账本（预览版）

Microsoft Azure 机密账本 (ACL)，也简称为机密账本（预览版），是用于管理敏感数据记录的一种新的、高度安全的服务。 根据授权区块链模型，机密账本提供唯一的数据完整性优势。 这包括不可变性，使账本仅追加并防篡改，以确保所有记录保持不变。

机密账本专用于硬件支持的安全 enclaves，这是一个高度监视的独立运行时环境，使潜在的攻击无处可逃。 此外，没有任何内容可“凌驾于”账本，甚至 Microsoft 也不行。 通过自行设计，机密账本在简单的可信计算基 (TCB) 上运行，这会阻止对账本服务开发人员、数据中心技术人员和云管理员的访问。

机密账本对不得修改关键元数据记录的用例（包括为遵守法规和存档目的而永久保存）很有吸引力。 下面是一些可在账本上存储的内容示例：

- 与业务事务相关的记录（例如资金转移或机密文档编辑）。
- 受信任资产的更新（例如核心应用程序或合同）。
- 管理和控制更改（例如授予访问权限）。
- 操作 IT 和安全事件（例如 Azure 安全中心警报）。

有关详细信息，请观看 [Microsoft Ignite 2020 机密账本演示](https://mediusprodstatic.studios.ms/asset-b88de19d-4187-40c4-98f2-a65efc419e2a/OD221_1920x1080_AACAudio_1461.mp4?sv=2018-03-28&sr=b&sig=k5roi6WXnlqK1zP0fs5KYlJd4FD3Nuaf97z%2B2gV0aTs%3D&st=2020-09-22T08%3A05%3A01Z&se=2025-09-22T08%3A10%3A01Z&sp=r&rscd=filename%3DIG20-OD221-Inside%2BAzure%2BDatacenter%2BArchitecture%2Bwith%2BMark%2BRu.mp4)。

## <a name="key-features"></a>关键功能

机密账本通过 REST API 公开，可以集成到新的或现有的应用程序中。 管理员使用管理 API（控制平面）可管理机密账本。 应用程序代码也可通过功能 API（数据平面）直接调用机密账本。 管理 API 支持创建、更新、获取和删除等基本操作。 功能 API 允许与实例化账本直接交互，并包含输入和获取数据等操作。

## <a name="ledger-security"></a>账本安全性

本部分定义账本的安全保护。 账本 API 使用基于客户端证书的身份验证。 目前，账本支持基于证书的身份验证过程和所有者角色。 我们将添加对基于 Azure Active Directory (AAD) 的身份验证以及基于角色的访问（例如所有者、读者和参与者）的支持。

账本的数据是通过 TLS 1.2 连接发送的，TLS 1.2 连接在硬件支持的安全 enclaves (Intel® SGX enclaves) 内终止。 这可确保没有人可以截获客户的客户端与机密账本服务器节点之间的连接。

### <a name="ledger-storage"></a>账本存储

机密账本是作为属于 Azure 存储帐户的 blob 存储容器中的块而创建。 事务数据可以存储为加密数据，也可以以纯文本形式存储，具体取决于你的需求。 创建账本时，将按照[注册机密账本服务主体](register-ledger-service-principal.md)中所述的步骤关联存储帐户。

管理员可使用管理 API（控制平面）管理机密账本，并且应用程序代码也可通过功能 API（数据平面）直接调用机密账本。 管理 API 支持创建、更新、获取和删除等基本操作。

功能 API 允许与实例化机密账本直接交互，并包含输入和获取数据等操作。

## <a name="preview-limitations"></a>预览版限制

- 创建机密账本后，无法更改账本类型。
- 机密账本目前不支持标准 Azure 灾难恢复。 但是，Azure 机密账本在 Azure 区域中提供内置冗余，因为机密账本在多个独立节点上运行。
- 机密账本删除会导致“硬删除”，因此删除后，你的数据将无法恢复。
- 机密账本名称必须是全局唯一的名称。 不允许使用具有相同名称的账本，无论其类型如何。

## <a name="terminology"></a>术语

| 术语 | 定义 |
|--|--|
| ACL | Azure 机密账本 |
| 账本 | 不可变的事务追加记录（也称为区块链） |
| 提交 | 确认事务已本地提交到节点。 本地提交本身并不保证事务属于账本。 |
| 全局提交 | 确认事务为全局提交并属于账本。 |
| 回执 | 验证交易是否由账本处理。 |

## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)
