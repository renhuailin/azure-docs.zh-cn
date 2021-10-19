---
title: Azure Active Directory 可验证凭据的新增功能
description: Azure Active Directory 可验证凭据的最新更新
author: barclayn
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: reference
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 44c3d652671b83a34e5884b02558a79453ac4424
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730546"
---
# <a name="whats-new-in-azure-active-directory-verifiable-credentials-preview"></a>Azure Active Directory 可验证凭据（预览版）的新增功能

在本文档中可以找到 Azure AD 可验证凭据服务的最新功能、改进和更改。

## <a name="october-2021"></a>2021 年 10 月

请求服务 API 公共预览版。

开发人员可以开始使用新的[请求服务 API](get-started-request-api.md) 在所选的任何编程语言中轻松生成能够颁发和/或验证凭据的应用程序。 此新 REST API 提供改进的抽象层，并提供与 Azure AD 可验证凭据服务的集成。
随着请求服务 API 的发布，我们已计划停用最初的基于 NodeJS 的 SDK，因此，在未来几个月弃用 NodeJS SDK 之前，我们强烈建议开始使用该 API。 我们的文档和示例已重新整理为使用请求服务 API。 详细了解[请求服务 API](get-started-request-api.md)

## <a name="april-2021"></a>2021 年 4 月

Azure Active Directory 可验证凭据目前为公共预览版。

Azure AD 客户现在可以轻松颁发[可验证凭据](decentralized-identifier-overview.md)来表示雇佣、教育或任何其他声明的证明，使此类凭据的持有者能够决定何时以及与谁共享其凭据。 每个凭据已通过与用户拥有和控制的 DID 关联的加密密钥进行签名。

## <a name="next-steps"></a>后续步骤

如有疑问，可以通过[支持](https://azure.microsoft.com/support/options/)渠道联系我们。
