---
title: 用 Azure 前门标准/高级 (预览) 规则集配置安全标头
description: 本文提供了有关如何使用规则集配置安全标头的指导。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098680"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>用 Azure 前门标准/高级 (预览) 规则集配置安全标头

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本文介绍如何实现安全标头，以阻止基于浏览器的漏洞（例如 HTTP 严格传输-安全 (HSTS) 、X-XSS-保护、内容安全策略或 X 框架选项）。 基于安全性的属性也可以用 Cookie 来定义。

下面的示例演示如何向与路由中的路径匹配的所有传入请求添加内容安全策略标头。 在这里，我们仅允许来自受信任的站点 https://apiphany.portal.azure-api.net 的脚本在我们的应用程序上运行。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* 必须先创建前门，然后才能配置安全标头。 有关详细信息，请参阅[快速入门：创建 Front Door](create-front-door-portal.md)。
* 如果以前未使用过规则集功能，请查看如何 [设置规则集](how-to-configure-rule-set.md) 。

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>在 Azure 门户中添加 Content-Security-Policy 标头

1. 请在 Azure 前门标准/高级配置文件中，选择 "设置" 下的 " **规则集** " **。**

1. 选择 " **添加** " 以添加新规则集。 为规则设置一个 **名称** ，然后为规则提供一个 **名称** 。 选择 " **添加操作** "，然后选择 " **响应标头**"。

1. 将 "运算符" 设置为 " **追加** " 可以将此标头添加为对此路由的所有传入请求的响应。

1. 添加标头名称：“Content-Security-Policy”，并定义此标头应接受的值。 在此方案中，我们将选择 *"script-src" 自 " https://apiphany.portal.azure-api.net "*。

1. 添加了所有要配置的规则后，请不要忘记将规则集与路由关联起来。 *需要* 执行此步骤才能让规则集执行操作。 

> [!NOTE]
> 在此示例中，我们没有向规则添加[匹配条件](concept-rule-set-match-conditions.md)。 与关联路由中定义的路径匹配的所有传入请求将应用此规则。 如果希望它仅应用于这些请求的一个子集，请确保将特定的“匹配条件”添加到此规则。

## <a name="clean-up-resources"></a>清理资源

### <a name="deleting-a-rule"></a>删除规则

在前面的步骤中，你已配置了内容安全策略标头和规则集。 如果不再需要规则，可选择规则集名称，然后选择 "删除规则"。 

### <a name="deleting-a-rule-set"></a>删除规则集

如果要删除规则集，请确保在删除规则时将其与所有路由解除关联。 有关删除规则集的详细指南，请参阅 [配置规则集](how-to-configure-rule-set.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何为前门配置 Web 应用程序防火墙，请参阅 [Web 应用程序防火墙和前门](../../web-application-firewall/afds/afds-overview.md)。
