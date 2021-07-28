---
title: 使用 UEBA 数据调查事件 | Microsoft Docs
description: 了解在调查时如何使用 UEBA 数据了解组织中发生的潜在恶意活动的更多背景信息。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2021
ms.author: bagol
ms.openlocfilehash: 1a3f199276d5c9b04ab5ac117c022a63fd3fb866
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665293"
---
# <a name="investigate-incidents-with-ueba-data"></a>使用 UEBA 数据调查事件

本文介绍关于在常规调查工作流中使用[用户实体行为分析 (UEBA)](identify-threats-with-entity-behavior-analytics.md) 的常用方法和示例程序。

## <a name="prerequisites"></a>先决条件

在调查中使用 UEBA 数据之前，必须[在 Azure Sentinel 中启用用户和实体行为分析 (UEBA)](enable-entity-behavior-analytics.md)。 

在启用 UEBA 约一周后开始寻找计算机助力的见解。

## <a name="run-proactive-routine-searches-in-entity-data"></a>在实体数据中运行主动例行搜索

建议通过用户活动定期进行主动搜索，为进一步调查建立线索。

可以使用 Azure Sentinel [用户和实体行为分析工作簿](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries)查询以下数据：

- **高风险用户**，存在异常或附加事件
- **特定用户数据**，确定使用者是否确实受到威胁，或者是否存在因操作不符合用户配置文件而导致的内部威胁。

此外，在 UEBA 工作簿中捕获非常规操作，并使用这些操作查找异常活动和潜在的不合规做法。

### <a name="investigate-an-anomalous-sign-in"></a>调查异常登录

例如，用户连接到以前从未使用过的 VPN 是一种异常活动，以下步骤是对此情况的调查。

1. 在 Sentinel“工作簿”区域中，搜索并打开“用户和实体行为分析”工作簿 。
1. 搜索要调查的特定用户名，并在“要调查的主要用户”表中选择其姓名。
1. 向下滚动“事件明细”表和“异常明细”表，查看与所选用户相关的事件和异常 。
1. 在异常中，例如在名为“异常成功登录”的异常中，查看表中所示的详细信息以进行调查。 例如：

    |步骤  |说明  |
    |---------|---------|
    |**注意右侧的说明**     |    每个异常都有一个说明，并附有一个链接，用于在 [MITRE ATT&CK 知识库](https://attack.mitre.org/)了解更多信息。 <br>例如： <br><br>        初始访问 **<br>攻击者正在试图入侵你的网络**。<br>初始访问包含使用各种进入途径在网络中获取初始立足点的技术。 获取立足点的技术包括有针对性的鱼叉式网络钓鱼和利用面向公众的 Web 服务器上的漏洞。 通过初始访问获取的立足点可能允许像有效帐户那样继续访问并使用外部远程服务，也可能因密码更改而被限制使用。     |
    |**注意“说明”列中的文本**     |   在异常行中，向右滚动以查看附加说明。 选择链接，查看全文。 例如： <br><br> 攻击者可能会使用凭据访问技术窃取特定用户或服务帐户的凭据，或者在侦查过程早期通过社交工程捕获凭据以获取初始访问。例如，APT33 已使用有效帐户进行初始访问。以下查询生成了成功登录的输出，不过登录的地理位置是一个该用户及其对等方从未执行过连接操作的新地点。     |
    |**注意 UsersInsights 数据**     |  在异常行中继续向右滚动，查看用户见解数据，例如帐户显示名称和帐户对象 ID。 选择文本，查看右侧的完整数据。         |
    |**注意证据数据**     |  在异常行中继续向右滚动，查看证据数据是否存在异常。 选择文本，查看右侧的完整数据，例如以下字段： <br><br>-  **ActionUncommonlyPerformedByUser** <br>- **UncommonHighVolumeOfActions** <br>- **FirstTimeUserConnectedFromCountry** <br>- **CountryUncommonlyConnectedFromAmongPeers** <br>- **FirstTimeUserConnectedViaISP** <br>- **ISPUncommonlyUsedAmongPeers** <br>- **CountryUncommonlyConnectedFromInTenant** <br>- **ISPUncommonlyUsedInTenant** |
    |     |         |

使用在“用户和实体行为分析”工作簿中查找到的数据来确定用户活动是否可疑以及是否需要采取进一步操作。

## <a name="use-ueba-data-to-analyze-false-positives"></a>使用 UEBA 数据分析假正

有时，调查过程中捕获的事件是假正。

假正的一个常见示例是检测到不可能的旅行活动，例如用户在同一小时内从纽约和伦敦登录应用程序或门户。 尽管 Azure Sentinel 将不可能的旅行记录为异常，但对用户进行调查后可能会发现是因为使用了 VPN，并且所用的位置替代了用户的实际位置。

### <a name="analyze-a-false-positive"></a>分析假正

例如，对于不可能的旅行事件，与用户确认使用了 VPN 后，从事件导航到用户实体页面。 使用此处所示的数据，确定捕获的位置是否属于用户的常见位置。

例如：

[![打开事件的用户实体页面。](media/ueba/open-entity-pages.png)](media/ueba/open-entity-pages.png#lightbox)

从[事件页面](tutorial-investigate-cases.md#how-to-investigate-incidents)本身和[调查图](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)还可以链接到用户实体页面。

> [!TIP]
> 确认与事件相关的特定用户的用户实体页面上的数据后，转到 Azure Sentinel“搜寻”区域，了解用户的对等方是否通常也从相同位置进行连接。 如果是，这些证据将为假正提供更强有力的理由。
>
> 在“搜寻”区域，运行“异常地理位置登录”查询 。 有关详细信息，请参阅[通过 Azure Sentinel 搜寻威胁](hunting.md)。
>

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>识别密码喷射和鱼叉式网络钓鱼攻击

如果不启用多重身份验证 (MFA)，用户凭据很容易受到希望尝试通过[密码喷射](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/)或[鱼叉式网络钓鱼](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/)进行泄露攻击的攻击者的威胁。

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>使用 UEBA 见解调查密码喷射事件

例如，若要使用 UEBA 见解调查密码喷射事件，可以执行以下操作，了解更多信息：

1. 在事件中，选择左下角的“调查”，查看可能成为攻击目标的帐户、计算机和其他数据点。

    浏览数据时，可能会发现某管理员帐户的登录失败次数相对较多。 尽管这很可疑，但不建议在没有进行进一步确认的情况下限制该帐户。

1. 选择映射中的管理用户实体，然后选择右侧的“见解”，查找更多详细信息，例如一段时间内的登录图。

1. 选择右侧的“信息”，然后选择“查看完整详细信息”，跳转到[用户实体页面](identify-threats-with-entity-behavior-analytics.md#entity-pages)进行深入了解 。 

    例如，注意这是否是用户的首次潜在密码喷射事件，或查看用户的登录历史记录，了解失败是否异常。

> [!TIP]
> 还可以运行 Anomalous Failed Logon [搜寻查询](hunting.md)，以监视组织的所有异常登录失败。 使用查询结果开始调查可能的密码喷射攻击。
>

## <a name="next-steps"></a>后续步骤

了解有关 UEBA、调查和搜寻的更多信息：

- [在 Azure Sentinel 中通过用户和实体行为分析 (UEBA) 来识别高级威胁](identify-threats-with-entity-behavior-analytics.md)
- [Azure Sentinel UEBA 扩充引用](ueba-enrichments.md)
- [教程：使用 Azure Sentinel 调查事件](tutorial-investigate-cases.md)
- [使用 Azure Sentinel 搜寻威胁](hunting.md)
