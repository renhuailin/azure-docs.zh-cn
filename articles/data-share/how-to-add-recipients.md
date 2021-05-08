---
title: 在 Azure Data Share 中添加收件人
description: 了解如何在 Azure Data Share 中将收件人添加到现有数据共享。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680645"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>如何将收件人添加到共享

可以在创建新共享时添加收件人或将收件人添加到现有共享。 通过 Azure Data Share UI，你可以使用用户的 Azure 登录电子邮件添加收件人。  通过 API，你可以使用用户/服务主体和租户 ID 的组合。 如果指定了租户 ID，则只能接受此租户的邀请。 同样，通过 API，你可以创建邀请，而无需向收件人发送电子邮件。 

## <a name="add-recipient-to-an-existing-share"></a>将收件人添加到现有共享

在 Azure Data Share 中，导航到已发送的共享并选择“邀请”选项卡。此处会列出已针对此数据共享向其发送邀请的所有收件人。 若要新添加一个，请单击“添加收件人”。

![屏幕截图显示已选中“添加收件人”。](./media/how-to/how-to-add-recipients/add-recipient.png)

页面右侧将弹出一个面板。 单击“添加收件人”，然后在空白行中填写新收件人的电子邮件地址。 请确保使用收件人的 Azure 登录电子邮件（使用其电子邮件别名将不起作用）。 

![屏幕截图显示了“添加收件人”窗格，你可以在其中添加和发送邀请。](./media/how-to/how-to-add-recipients/add-recipient-side.png)

单击“添加并发送邀请”。 这将向新收件人发送指向此共享的邀请电子邮件。

## <a name="next-steps"></a>后续步骤
详细了解如何[删除共享邀请](how-to-delete-invitation.md)。
