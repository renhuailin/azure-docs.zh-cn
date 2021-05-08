---
title: 将 Windows 虚拟桌面与 Azure 顾问集成 - Azure
description: 如何将 Azure 顾问与 Windows 虚拟桌面部署结合使用。
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b141d3829e9d98cb793d2038e93331804d2e6b8f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448281"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>将 Azure 顾问与 Windows 虚拟桌面结合使用

Azure 顾问可帮助用户自行解决常见问题，而无需提交支持案例。 这些建议可以减少用于提交帮助请求的次数，从而节省了时间和成本。

本文将介绍如何在 Windows 虚拟桌面部署中设置 Azure 顾问，以对用户有所帮助。

## <a name="what-is-azure-advisor"></a>什么是 Azure 顾问？

Azure 顾问会分析你的配置和遥测，提供个性化的建议来解决常见问题。 利用这些建议，你可以优化 Azure 资源以提高可靠性、安全性、卓越运营、性能并降低成本。 有关详细信息，请访问 [Azure 网站](https://azure.microsoft.com/services/advisor/)。

## <a name="how-to-start-using-azure-advisor"></a>如何开始使用 Azure 顾问

你只需在 Azure 门户上注册一个 Azure 帐户即可开始使用。 首先打开 Azure 门户，网址为 <https://portal.azure.com/#home>，然后选择“Azure 服务”下的“顾问”，如下图所示。  还可以在 Azure 门户的搜索栏中输入“Azure 顾问”。

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图。 用户将鼠标光标悬停在 Azure 顾问链接上，这样会出现一个下拉菜单。](media/azure-advisor.png)

打开 Azure Advisor时，你将看到五个类别：

- 成本
- 安全性
- 可靠性
- 卓越运营
- 性能

> [!div class="mx-imgBorder"]
> ![每个类别显示了五个菜单的 Azure 顾问的屏幕截图。 这五项在单独的框中显示，分别是“成本”、“安全性”、“可靠性”、“卓越运营”和“性能”。](media/advisor-categories.png)

选择某个类别后，你将看到其积极建议页。 在此页上，可以查看 Azure 顾问为你提供的建议，如下图所示。

> [!div class="mx-imgBorder"]
> ![针对越运营的积极建议列表的屏幕截图。 此列表显示具有不同优先级的七个建议。](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure 顾问的其他提示

- 请务必经常检查这些建议，至少每周检查一次。 Azure 顾问每天多次更新其积极建议。 检查新建议可以帮助你找出并解决轻微问题，从而防止出现严重问题。

- 请始终尝试解决 Azure 顾问中优先级最高的问题。 高优先级问题以红色标注。 不解决高优先级建议可能会导致线路出现问题。

- 如果建议似乎不太重要，则可以将其取消或延迟。 要消除或推迟建议，请转到“操作”列并更改项的状态。

- 只有在你知道显示建议的原因并确保它不会对你或你的用户产生负面影响之后，再关闭建议。 始终选择“了解更多”以了解问题。 如果根据 Azure 顾问中的说明解决了某个问题，这个问题将自动从列表中消失。 最好是及时解决这些问题，而不是不断地推迟。

- 每当遇到 Windows 虚拟桌面中的问题时，始终首先检查 Azure 顾问。 Azure 顾问将为你提供有关如何解决此问题的说明，或者至少指向有帮助的资源。

## <a name="next-steps"></a>后续步骤

要了解如何解决建议，请参阅[如何解决 Azure 顾问建议](azure-advisor-recommendations.md)。
