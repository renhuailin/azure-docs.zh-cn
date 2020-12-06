---
title: 配置解决方案模板计划
description: 了解如何在合作伙伴中心为 Azure 应用程序产品/服务配置解决方案模板计划。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744612"
---
# <a name="configure-a-solution-template-plan"></a>配置解决方案模板计划

本文仅适用于 Azure 应用程序产品/服务的解决方案模板计划。 如果要配置托管应用程序计划，请参阅 [配置托管应用程序计划](create-new-azure-apps-offer-managed.md)。

## <a name="choose-who-can-see-your-plan"></a>选择可以查看你的计划的人员

你可以将每个计划配置为对每个用户可见，或仅针对特定受众。 你可以使用 Azure 订阅 Id 向专用用户授予访问权限，其中包含分配给每个订阅 ID 的说明。 你最多可以手动添加10个订阅 Id，或使用最多添加10000个订阅 Id。CSV 文件。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

> [!NOTE]
> 如果发布专用计划，则可以在以后将其可见性更改为 "公共"。 但是，在发布公共计划后，不能将其可见性更改为私有。

在 " **可用性** " 选项卡上的 " **计划可见性**" 下，执行以下操作之一：

- 若要使该计划成为公共计划，请选择 " **公共** " 选项按钮 (也称为 _单选按钮_) 。
- 要使该计划成为私有计划，请选择 " **专用** " 选项按钮，然后手动或使用 CSV 文件添加 Azure 订阅 id。

    > [!NOTE]
    > 专用或受限制的受众不同于你在 " **预览** " 选项卡上定义的预览观众。预览版观众在 marketplace 发布之前，可以访问产品/服务。 专用受众选项仅适用于特定计划，而预览受众可以查看所有计划（专用或非专用）以进行验证。

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>手动为私有计划添加 Azure 订阅 Id

1. 在 " **计划可见性**" 下，选择 " **专用** " 选项按钮。
1. 在出现的 " **Azure 订阅 id** " 框中，输入想要授予对此私有计划的访问权限的受众的 AZURE 订阅 id。 至少需要一个订阅 ID。
1.  (可选) 在 " **说明** " 框中输入此受众的说明。
1. 若要添加其他订阅 ID，请选择 " **添加 ID (最大 10)** " 链接，并重复步骤2和3。

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>使用。用于为私有计划添加 Azure 订阅 Id 的 CSV 文件

1. 在 " **计划可见性**" 下，选择 " **专用** " 选项按钮。
1. 选择 " **导出访问群体 (csv)** " 链接。
1. 打开。CSV 文件，并添加要向其授予对专用产品/服务的访问 **权限的 Azure** 订阅 id。
1. （可选）在 " **说明** " 列中为每个受众输入描述。
1. 在 " **类型** " 列中，为每个具有订阅 ID 的行添加 "SubscriptionId"。
1. 保存。CSV 文件。
1. 在 " **可用性** " 选项卡上的 " **计划可见性**" 下，选择 " **导入访问群体 (csv)** " 链接。
1. 在出现的对话框中，选择 **"是"**。
1. 选择。CSV 文件，然后选择 " **打开**"。 此时将显示一条消息，指示。已成功导入 CSV 文件。

### <a name="hide-your-plan"></a>隐藏计划

如果你的解决方案模板只应在被其他解决方案模板或托管应用程序引用时间接部署，请选中 " **隐藏计划** 以发布解决方案模板" 下的复选框，但将其从直接搜索和浏览的客户隐藏。

选择 " **保存草稿** "，然后继续下一部分：定义技术配置。

## <a name="define-the-technical-configuration"></a>定义技术配置

在 " **技术配置** " 选项卡上，将上载部署包，以便客户部署计划并为包提供版本号。

> [!NOTE]
> 如果在 " **计划设置** " 选项卡上选择重新使用其他计划中的包，则此选项卡将不可见。如果是，请参阅 [查看你的计划](#view-your-plans)。

### <a name="assign-a-version-number-for-the-package"></a>为包分配版本号

在 " **版本** " 框中提供技术配置的当前版本。 每次将更改发布到此页时，都会递增此版本。 版本号的格式必须为：整数。 例如，`1.0.2`。

### <a name="upload-a-package-file"></a>上传包文件

在 " **包文件 ( .zip)** 下，将包文件拖到灰色框，或选择" **浏览文件 (")** 链接。

> [!NOTE]
> 如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

### <a name="previously-published-packages"></a>以前发布的包

Live 发布产品/服务后，"**技术配置**" 页上将显示 "**以前发布的包**" 子选项卡。 此选项卡列出了以前发布的技术配置的所有版本。

## <a name="view-your-plans"></a>查看计划

- 选择 " **保存草稿**"，然后在页面的左上方，选择 " **计划概述** " 以返回到 " **计划概述** " 页。

创建一个或多个计划后，你将在 " **计划概述** " 选项卡上看到计划名称、计划 ID、计划类型、可用性 (公共或私有) 、当前发布状态和任何可用操作。

"**计划概述**" 选项卡的 "**操作**" 列中的可用操作取决于计划的状态，其中可能包括以下内容：

- 如果计划状态为 " **草稿**"，则 " **操作** " 列中的链接将显示 " **删除草稿**"。
- 如果计划状态为 " **活动**"，则 " **操作** " 列中的链接将是 " **停止销售计划** " 或 " **同步专用受众**"。 " **同步专用用户** " 链接将仅发布对专用用户的更改，而不会发布对产品/服务的任何其他更新。
- 若要为此服务创建另一个计划，请在 " **计划概述** " 选项卡的顶部选择 " **+ 创建新计划**"。 然后，重复 [如何为 Azure 应用程序产品/服务创建计划](create-new-azure-apps-offer-plans.md)中的步骤。 否则，如果你已完成创建计划，请参阅下一节：后续步骤。

## <a name="next-steps"></a>后续步骤

- [如何测试和发布 Azure 应用程序产品/服务](create-new-azure-apps-offer-test-publish.md)。
- 了解如何通过与 Microsoft 共同销售并转售通过 Csp 计划的 [方式销售 Azure 应用程序产品](create-new-azure-apps-offer-marketing.md) 。
