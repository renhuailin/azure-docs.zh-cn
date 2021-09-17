---
title: 媒体服务中的 Azure Policy 内置支持
description: 本文探讨适用于 Azure 媒体服务方案的 Azure Policy 内置支持。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 88b1907d0c767d77dd4a1f2e38ab859d4147ea96
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429814"
---
# <a name="azure-policy-for-media-services"></a>适用于媒体服务的 Azure Policy

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒体服务提供多个内置 [Azure Policy](../../governance/policy/overview.md) 定义，以帮助大规模强制实施组织标准和符合性。
Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本管理方面的要求。

媒体服务为 Azure Policy 提供一些常见的内置用例定义，以帮助用户入门。

## <a name="built-in-azure-policy-definitions-for-media-services"></a>适用于媒体服务的内置 Azure Policy 定义

你可以借助多个内置策略用例定义开始使用媒体服务，而且还能定义你自己的自定义策略。

[!INCLUDE [Azure Policy Media Services](../../../includes/policy/reference/bycat/policies-media-services.md)]

[媒体服务的内置策略定义列表](../../governance/policy/samples/built-in-policies.md#media-services)提供最新定义，并链接代码定义以及在门户中访问代码的方法。

## <a name="common-scenarios-that-require-azure-policy"></a>需要 Azure Policy 的常见方案

* 如果企业安全性要求你必须使用专用链接创建所有媒体服务帐户，你可以使用策略定义来确保仅使用 2020-05-01 API（或更高版本）来创建帐户，以禁止访问旧版 REST v2 API 以及使用专用链接功能。
* 如果要对内容密钥策略使用的令牌强制实施特定选项，则可以通过设计 Azure Policy 定义来支持特定要求。
* 如果安全目标要求将作业输入源限制为仅来自受信任的存储帐户，并通过使用 JobInputHttp 限制对外部 HTTP(S) 输入的访问，则可以构建 Azure 策略来限制输入 URI 模式。

## <a name="example-policy-definitions"></a>示例策略定义

Azure 媒体服务可以在 Git 中心维护并发布一组 Azure Policy 定义示例。
请在 azure-policy Git 中心存储库中，参阅[适用于媒体服务的内置策略定义](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policyDefinitions/Media%20Services)示例。

有关详细信息，请参阅以下文章：

- [Azure Policy 简介](../../governance/policy/overview.md)
- [快速入门：在门户中创建策略](../../governance/policy/assign-policy-portal.md)
- [适用于媒体服务的内置策略定义列表](../../governance/policy/samples/built-in-policies.md#media-services)

## <a name="next-steps"></a>后续步骤

- [使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)
- [媒体服务中基于角色的访问控制](security-rbac-concept.md)
- [如何使用专用链接创建媒体服务帐户](security-private-link-how-to.md)