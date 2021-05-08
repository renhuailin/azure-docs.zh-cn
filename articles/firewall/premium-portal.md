---
title: Azure 门户中的 Azure 防火墙高级预览版
description: 了解 Azure 门户中的 Azure 防火墙高级预览版
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549573"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure 门户中的 Azure 防火墙高级预览版

> [!IMPORTANT]
> Azure 防火墙高级版目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 Azure 防火墙高级预览版是新一代防火墙，其中包含高度敏感环境和受管制环境所需的功能。 它包括以下功能：

- TLS 检查 - 解密出站流量、处理数据，然后加密数据并将数据发送到目的地。
- IDPS - 网络入侵检测和防护系统 (IDPS) 使你可以监视网络活动是否出现恶意活动，记录有关此活动的信息，予以报告，选择性地尝试阻止。
- URL 筛选 - 将 Azure 防火墙的 FQDN 筛选功能扩展到考虑整个 URL。 例如，`www.contoso.com/a/c`，而非 `www.contoso.com`。
- Web 类别 - 管理员可以允许或拒绝用户访问某些类别的网站，例如赌博网站、社交媒体网站等。

有关详细信息，请参阅 [Azure 防火墙高级版功能](premium-features.md)。

## <a name="deploy-the-firewall"></a>部署防火墙

部署 Azure 防火墙高级预览版类似于部署标准 Azure 防火墙：

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="门户部署":::

对于“防火墙层”，选择“高级（预览版）”，对于“防火墙策略”，选择一个现有高级策略或创建一个新策略。  

## <a name="configure-the-premium-policy"></a>配置高级策略

配置高级防火墙策略类似于配置标准防火墙策略。 使用高级策略，可以配置高级功能：

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="高级策略部署":::

### <a name="rule-configuration"></a>规则配置

在高级策略中配置应用程序规则时，可以配置添加高级功能：

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="高级规则":::

## <a name="next-steps"></a>后续步骤

要查看 Azure 防火墙高级预览功能的运行情况，请参阅[部署和配置 Azure 防火墙高级预览版](premium-deploy.md)。