---
title: Azure 门户中的 Azure 防火墙高级版
description: 了解 Azure 门户中的 Azure 防火墙高级版。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: cfb9f76751bbf5966b4aaa29e382a0946bf7cfd6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440411"
---
# <a name="azure-firewall-premium-in-the-azure-portal"></a>Azure 门户中的 Azure 防火墙高级版


 Azure 防火墙高级版是新一代防火墙，具备了高度敏感环境和受管制环境所需的功能。 它包括以下功能：

- TLS 检查 - 解密出站流量、处理数据，然后加密数据并将数据发送到目的地。
- IDPS - 网络入侵检测和防护系统 (IDPS) 使你可以监视网络活动是否出现恶意活动，记录有关此活动的信息，予以报告，选择性地尝试阻止。
- URL 筛选 - 将 Azure 防火墙的 FQDN 筛选功能扩展到考虑整个 URL。 例如，`www.contoso.com/a/c`，而非 `www.contoso.com`。
- Web 类别 - 管理员可以允许或拒绝用户访问某些类别的网站，例如赌博网站、社交媒体网站等。

有关详细信息，请参阅 [Azure 防火墙高级版功能](premium-features.md)。

## <a name="deploy-the-firewall"></a>部署防火墙

部署 Azure 防火墙高级版与部署标准 Azure 防火墙类似：

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="门户部署":::

对于“防火墙层”，选择“高级”；对于“防火墙策略”，选择一个现有高级策略或创建一个新策略。  

## <a name="configure-the-premium-policy"></a>配置高级策略

配置高级防火墙策略类似于配置标准防火墙策略。 使用高级策略，可以配置高级功能：

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="高级策略部署":::

### <a name="rule-configuration"></a>规则配置

在高级策略中配置应用程序规则时，可以配置添加高级功能：

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="高级规则":::

## <a name="next-steps"></a>后续步骤

若要查看 Azure 防火墙高级功能的运行情况，请参阅[部署和配置 Azure 防火墙高级版](premium-deploy.md)。