---
title: 在 Azure 市场中配置每月计费价格
description: 了解如何配置 VM 价格。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshah
ms.author: iqshah
ms.date: 09/28/2021
ms.openlocfilehash: 6c08319037a06ff7bb1051fd41b40dc20ec37fb6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219257"
---
# <a name="configure-prices-for-usage-based-monthly-billing"></a>配置基于使用情况的每月计费价格

基于使用情况的每月计费计划将按小时使用计费，并按月计费。 这就是我们的“即用即付”计划，客户只需按使用时间计费。
选择此计划时，请选择以下定价选项之一：

- 免费 - VM 产品/服务免费。
- 固定费率（建议） - VM 产品/服务的价格相同，而不考虑其运行的硬件。
- 根据内核 - VM 产品/服务定价按 CPU 内核数计算（你向我们提供一个 CPU 内核的价格，我们将根据硬件的大小增加定价）。
- 根据内核大小 - 根据所部署的硬件上的 CPU 内核数指定价格。
- 根据市场和内核大小 - 根据所部署的硬件上的 CPU 内核数指定价格，并适用于所有市场（币种转换由你的发布者完成，如果使用“导入定价”功能，此选项会比较简单）。

选择定价选项时要考虑的事项如下：

- 在前四个选项中，Microsoft 执行币种转换。
- Microsoft 建议对软件解决方案使用固定费率定价。
- 价格是固定的，因此一旦发布，就无法调整。 但是，如果你想要降低 VM 产品/服务的价格，可以开具[支持票证](/azure/marketplace/support)。

## <a name="new-offering-pricing"></a>新产品/服务定价

Microsoft Azure 会定期添加新的 VM 基础结构。 有时我们添加的计算机具有之前未提供的 CPU 计数。 Microsoft 根据以前的定价确定新内核大小的价格，并将其添加为建议价格。

如果为新的内核大小设置了价格，发布者将收到电子邮件，并且可以在一定时间内根据需要审查并进行调整。 截止时间过后，Microsoft 将为新添加的内核大小发布价格。

如果发布者选择了“免费”、“固定费率”或“根据内核大小”，则发布者已就如何为新内核大小定价提供的所需的详细信息，无需执行其他操作。 但是，如果发布者之前选择了“根据内核大小”或“根据市场和内核大小”，则他们需要与 Microsoft 联系，并提供更新后的定价信息。

## <a name="next-steps"></a>后续步骤

- 如果你有任何问题，请开具[支持票证](/azure/marketplace/support)。