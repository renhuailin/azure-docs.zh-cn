---
title: Azure 顾问 Azure 虚拟桌面演练 - Azure
description: 如何解决 Azure 顾问对 Azure 虚拟桌面的建议。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b8bc514f495a46e76ad882720c918824aab0aaac
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745474"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>如何解决 Azure 顾问建议

本文介绍了如何解决 Azure 顾问中对 Azure 虚拟桌面的建议。

## <a name="no-validation-environment-enabled"></a>“未启用验证环境”

>[!div class="mx-imgBorder"]
>![Azure 顾问“卓越运营”页的屏幕截图。 “未启用验证环境”建议用红色突出显示。](media/no-validation-environment.png)

此建议显示在“卓越运营”下。 此建议还应显示如下警告消息：

“你没有在此订阅中启用验证环境。 创建主机池时，在“属性”选项卡中为“验证环境”选择“否”。若要通过 Azure 虚拟桌面服务部署确保业务连续性，请确保至少有一个主机池带有可用于测试潜在问题的验证环境。”

可以通过在你的主机池之一中启用验证环境来消除此警告消息。

若要启用验证环境，请执行以下操作：

1. 转到 Azure 门户主页，然后选择要更改的主机池。

2. 接下来，选择要从生产环境更改为验证环境的主机池。

3. 在主机池中，选择左侧列中的“属性”。 接下来，向下滚动，直到看到“验证环境”。 选中“是”，然后选择“应用”。

>[!div class="mx-imgBorder"]
>![“属性”菜单的屏幕截图。 “验证环境”用红色突出显示，且“是”气泡处于选中状态。](media/validation-yes.png)

这些更改虽然不会让此警告立即消除，但它最终应该会消除。 Azure 顾问每天更新两次。 在此之前，你可以手动推迟或消除建议。 建议的做法是，让建议自行消除。 这样一来，当设置发生更改时，Azure 顾问可以通知你是否遇到任何问题。

## <a name="not-enough-production-non-validation-environments-enabled"></a>“未启用足够的生产（非验证）环境”

此建议显示在“卓越运营”下。

对于此建议，出现警告消息的原因之一是：

- 验证环境中的主机池过多。
- 没有任何生产主机池。

建议用户在验证环境中使用的主机池少于一半。

若要解决此警告，请执行以下操作：

1. 转到 Azure 门户主页。

2. 选择要从验证环境更改为生产环境的主机池。

3. 在主机池中，选择屏幕左侧列中的“属性”选项卡。 接下来，向下滚动，直到看到“验证环境”。 选中“否”，然后选择“应用”。

>[!div class="mx-imgBorder"]
>![“属性”菜单的屏幕截图。 “验证环境”用红色突出显示，且“否”气泡处于选中状态。](media/validation-no.png)

这些更改虽然不会让此警告立即消除，但它最终应该会消除。 Azure 顾问每天更新两次。 在此之前，你可以手动推迟或消除建议。 建议的做法是，让建议自行消除。 这样一来，当设置发生更改时，Azure 顾问可以通知你是否遇到任何问题。

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>“没有足够的链接被解除阻止，无法成功实现 VM”

此建议显示在“卓越运营”下。

你需要解除阻止特定 URL，以确保虚拟机 (VM) 正常运行。 可以在[安全 URL 列表](safe-url-list.md)中查看列表。 如果这些 URL 没有被解除阻止，则 VM 将无法正常运行。

若要解决此建议，请确保解除阻止[安全 URL 列表](safe-url-list.md)中的所有 URL。 也可以使用服务标记或 FQDN 标记来解除阻止 URL。

## <a name="next-steps"></a>后续步骤

如果在查找有关如何解决常见问题的更深入指南，请查看 [Azure 虚拟桌面问题排查概述、反馈和支持](troubleshoot-set-up-overview.md)。
