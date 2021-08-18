---
title: Internet 分析器常见问题解答 | Microsoft Docs
description: Azure Internet 分析器的常见问题解答。
services: internet-analyzer
author: KumudD
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: kumud
ms.openlocfilehash: 9a9bedc1dcc1d217a3f7dac270245afa923482d8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454826"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet 分析器常见问题解答（预览版）

这是 Azure Internet 分析器的常见问题解答，如果你有其它问题，请访问[反馈论坛](https://aka.ms/internetAnalyzerFeedbackForum)，并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="how-do-i-participate-in-the-preview"></a>如何参与预览版？

预览版向选定的客户提供。 如果有兴趣加入预览版，请执行以下操作：

1. 登录 [Azure 门户](https://ms.portal.azure.com)。
2. 导航到“订阅”页。
3. 单击计划与 Internet 分析器一起使用的 Azure 订阅。
4. 转到该订阅的“资源提供程序”设置。
5. 搜索“Microsoft.Network”，然后单击“注册”（或“重新注册”）按钮  。
![访问请求](./media/ia-faq/request-preview-access.png)

6. 通过向我们提供用于发出访问请求的电子邮件地址和 Azure 订阅 ID 来[请求批准](https://aka.ms/internetAnalyzerContact)。
7. 请求得到批准后，你将收到一封电子邮件确认，并能够从新允许的 Azure 订阅中创建、更新或修改 Internet 分析器资源。

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>是否需要嵌入客户端来运行测试？

是的，Internet 分析器客户端必须安装在应用程序中，以收集特定于用户的指标。 [了解如何安装客户端。](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>参与预览版是否需要支付费用？
不需要，Azure Internet 分析器的预览版可以免费使用。 在预览期间，没有服务级别协议。

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Internet 分析器旨在用于哪些场景？

Internet 分析器旨在根据用户群体为你提供网络性能见解。 为了帮助用户做出最佳性能决策，Internet 分析器根据不同的用户群体比较两个 Internet 终结点的性能。 虽然 Internet 分析器可以回答许多问题，但最常见的问题有：

* 迁移到云对性能有什么影响？ 
    * *建议的测试：自定义（当前的本地基础结构）与Azure （任何预配置的终结点）*
* 将数据放在边缘与数据中心的价值是什么？ 
    *  *建议的测试：Azure 与Azure Front Door，Azure 与Microsoft 推出的 Azure CDN*
* Azure Front Door 的性能优势是什么？
    *  *建议的测试：自定义/Azure/CDN 与Azure Front Door*
* Microsoft 推出的 Azure CDN 的性能优势是什么？ 
    *  *建议的测试：自定义/Azure/AFD 与Microsoft 推出的 Azure CDN*
* Microsoft 推出的 Azure CDN 如何堆叠？ 
    *  *建议的测试：自定义（其他 CDN 终结点）与Microsoft 推出的 Azure CDN*
* 对于每个地区的最终用户群来说，最好的云是什么？ 
    *  *建议的测试：自定义（其他云服务）与Azure （任何预配置的终结点）*

## <a name="which-tests-can-i-run-in-preview"></a>在预览版中可以运行哪些测试？

在 Internet 分析器中创建的每种测试都由两个终结点（终结点 A 和终结点 B）组成。以下任何组合都可以作为测试运行：  
* 两个预配置的终结点，
* 一个自定义终结点和一个预配置的终结点，或
* 两个[自定义终结点](internet-analyzer-custom-endpoint.md)（其中一个自定义终结点必须位于 Azure 中）。

预览期间，可以使用以下预配置的终结点：
* **Azure 区域**
    * 巴西南部
    * 印度中部
    * 美国中部
    * 东亚
    * 美国东部
    * 日本西部
    * 北欧
    * 南非北部
    * 东南亚
    * 阿拉伯联合酋长国北部
    * 英国西部  
    * 西欧
    * 美国西部
    * 美国西部 2
* **多个 Azure 区域组合**
    * 美国东部、巴西南部
    * 美国东部、东亚
    * 欧洲西部、巴西南部
    * 欧洲西部、东南亚
    * 欧洲西部、阿联酋北部
    * 美国西部、美国东部
    * 美国西部、欧洲西部
    * 美国西部、阿拉伯联合酋长国北部
    * 欧洲西部、阿联酋北部、东南亚
    * 美国西部、欧洲西部、东亚
    * 美国西部、欧洲北部、东南亚、阿联酋北部、南非北部 
* **Azure + Azure Front Door** - 部署在上面列出的任何单个或多个 Azure 区域组合上
* **Azure + Microsoft 推出的 Azure CDN** - 部署在上面列出的任何单一 Azure 区域组合上
* **Azure + Azure 流量管理器** - 部署在上面列出的任何多个 Azure 区域组合上

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet 分析器与 Azure 提供的其他监视服务有何不同？

Internet 分析器有助于了解最终用户的性能，并帮助作出可提高其性能的决策。 尽管其他 Azure 监视工具提供对 Azure 服务的见解，但 Internet 分析器专注于为用户测量端到端的 Internet 性能。

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Internet 分析器如何处理测量数据？

Azure 有[严格的安全过程，并符合各种法规标准](https://azure.microsoft.com/support/trust-center/)。 只有你和指定的团队可以访问数据。 Microsoft 工作人员只会在知情的情况下和受限的具体情况下，才对数据拥有受限的访问权限。 将对传输中的静态数据加密。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [Internet 分析器概述](internet-analyzer-overview.md)。
