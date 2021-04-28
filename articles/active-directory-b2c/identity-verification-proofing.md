---
title: Azure AD B2C 的身份证明和验证
titleSuffix: Azure AD B2C
description: 了解 Azure AD B2C 与合作伙伴集成提供的身份证明和验证解决方案
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/23/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 536c63fec9958823191f3b01ad2142022832ef23
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124376"
---
# <a name="identity-verification-and-proofing-partners"></a>身份验证和证明合作伙伴

通过 Azure AD B2C 合作伙伴，客户可以在允许帐户注册或访问之前，对最终用户进行身份验证和证明。 身份验证和证明可以检查文档以及基于知识的信息和活动性。

高级体系结构关系图说明该流程。

![关系图表示身份证明流](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft 与以下 ISV 合作伙伴合作。

| ISV 合作伙伴 | 说明和集成细节 |
|:-------------------------|:--------------|
|![Experian 徽标的屏幕截图。](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 是一家身份识别和证明提供商，基于用户特性评估风险、防御欺诈。 |
|![IDology 徽标的屏幕截图。](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 是一家身份识别和证明提供商，提供 ID 验证、防御欺诈、合规性以及其他问题的解决方案。|
|![Jumio 徽标的屏幕截图。](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 提供 ID 验证服务，可以实时、自动验证 ID，保护用户数据。 |
| ![LexisNexis 徽标的屏幕截图。](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 是一家身份识别和证明提供商，验证用户身份并根据用户设备提供综合性风险评估。 |
| ![Onfido 徽标的屏幕截图](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 提供文档 ID 和面部生物识别解决方案，让企业能实时满足“了解客户”和身份的要求。  |

## <a name="additional-information"></a>其他信息

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>后续步骤

在上述表中选择一个合作伙伴，了解如何将其解决方案与 Azure AD B2C 集成。