---
title: 区域可用性和数据驻留
titleSuffix: Azure AD B2C
description: 区域可用性、数据驻留，以及有关 Azure Active Directory B2C 预览版租户的信息。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/16/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 92297e63739bca85a640120152ee864b85f0f640
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252321"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C：区域可用性和数据驻留

Azure AD B2C 标识数据存储在基于创建租户时提供的国家/地区的地理位置。

区域可用性和数据驻留是适用于 Azure AD B2C 的两个截然不同的概念。 本文介绍这两个概念之间的区别，并对它们应用到 Azure 和 Azure AD B2C 的方式进行比较。

Azure AD B2C 已在全球正式发布，并在美国、欧洲、亚太区域或澳大利亚提供数据驻留选项  。

[区域可用性](#region-availability)是指服务适用的地区。

[数据驻留](#data-residency)是指存储用户数据的位置。

## <a name="region-availability"></a>上市区域

Azure AD B2C 可通过 Azure 公有云在全球通用。 可在 Azure 的[可用产品（按区域）](https://azure.microsoft.com/regions/services/)页面和 [Active Directory B2C 定价计算器](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中查看此功能的示例。

## <a name="data-residency"></a>数据驻留

Azure AD B2C 将用户数据存储在美国、欧洲、亚太区域或澳大利亚。

数据驻留取决于[创建 Azure AD B2C 租户](tutorial-create-tenant.md)时选择的国家/地区：

![在其中可以选择国家或地区的“创建租户”窗体的屏幕截图。](./media/data-residency/data-residency-b2c-tenant.png)

来自以下国家/地区的数据驻留在美国：

> 美国 (US)、加拿大 (CA)、哥斯达黎加 (CR)、多米尼加共和国 (DO)、萨尔瓦多 (SV)、危地马拉 (GT)、墨西哥 (MX)、巴拿马 (PA)、波多黎各 (PR) 及特立尼达和多巴哥 (TT)

来自以下国家/地区的数据驻留在欧洲：

> 阿尔及利亚 (DZ)、奥地利 (AT)、阿塞拜疆 (AZ)、巴林 (BH)、白俄罗斯 (BY)、比利时 (BE)、保加利亚 (BG)、克罗地亚 (HR)、塞浦路斯 (CY)、捷克共和国 (CZ)、丹麦 (DK)、埃及 (EG)、爱沙尼亚 (EE)、芬兰 (FT)、法国 (FR)、 德国 (DE)、希腊 (GR)、匈牙利 (HU)、冰岛 (IS)、爱尔兰 (IE)、以色列 (IL)、意大利 (IT)、约旦 (JO)、哈萨克斯坦 (KZ)、肯尼亚 (KE)、科威特 (KW)、拉脱维亚 (LV)、黎巴嫩 (LB)、列支敦士登 (LI)、立陶宛 (LT)、卢森堡 (LU)、北马其顿 (ML)、马耳他 (MT)、黑山 (ME)、摩洛哥 (MA)、荷兰 (NL)、尼日利亚 (NG)、挪威 (NO)、阿曼 (OM)、巴基斯坦 (PK)、波兰 (PL)、葡萄牙 (PT)、卡塔尔 (QA)、罗马尼亚 (RO)、俄罗斯 (RU)、沙特阿拉伯 (SA)、塞尔维亚 (RS)、斯洛伐克 (SK)、斯洛文尼亚 (ST)、南非 (ZA)、西班牙 (ES)、瑞典 (SE)、瑞士 (CH)、突尼斯 (TN)、土耳其 (TR)、乌克兰 (UA)、阿联酋 (AE) 和英国 (GB)

来自以下国家/地区的数据驻留在亚太区域：

> 阿富汗 (AF)、中国香港特别行政区 (HK)、印度 (IN)、印度尼西亚 (ID)、日本 (JP)、韩国 (KR)、马来西亚 (MY)、菲律宾 (PH)、新加坡 (SG)、斯里兰卡 (LK)、中国台湾 (TW) 和泰国 (TH)

来自以下国家/地区的数据驻留在澳大利亚：

> 澳大利亚和新西兰

以下国家/地区即将加入该列表。 目前，仍然可以通过选择上述任何国家/地区来使用 Azure AD B2C。

> 阿根廷、巴西、智利、哥伦比亚、厄瓜多尔、伊拉克、巴拉圭、秘鲁、乌拉圭和委内瑞拉

## <a name="remote-profile-solution"></a>远程配置文件解决方案

使用 Azure AD B2C [自定义策略](custom-policy-overview.md)可以集成 [RESTful API 服务](api-connectors-overview.md)，以便能够从远程数据库（例如市场营销数据库、CRM 系统或任何业务线应用程序）存储和读取用户配置文件。  
- 在运行注册和配置文件编辑流期间，Azure AD B2C 会调用一个自定义 REST API 将用户配置文件持久保存到远程数据源。 用户的凭据存储在 Azure AD B2C 目录中。 
- 登录时，在使用本地帐户或社交帐户进行凭据验证后，Azure AD B2C 会调用该 REST API，将用户的唯一标识符作为用户主密钥（电子邮件地址或用户 objectId）发送。 该 REST API 从远程数据库读取数据并返回用户配置文件。  

完成注册、配置文件编辑或登录后，Azure AD B2C 将在返回给应用程序的访问令牌中包含用户配置文件。 有关详细信息，请参阅 GitHub 中的 [Azure AD B2C 远程配置文件示例解决方案](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)。

## <a name="next-steps"></a>后续步骤

- [创建 Azure AD B2C 租户](tutorial-create-tenant.md)。
