---
title: 在 Azure 数据共享中添加收件人
description: 了解如何在 Azure 数据共享中将收件人添加到现有数据共享。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680645"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>如何将收件人添加到共享

你可以在创建新共享或现有共享时添加收件人。 通过 Azure 数据共享 UI，你可以使用用户的 Azure 登录电子邮件添加收件人。  从 API 中，可以结合使用用户/服务主体和租户 ID。 指定租户 ID 后，邀请只能接受到此租户中。 此外，还可以在 API 中创建邀请，无需向收件人发送电子邮件。 

## <a name="add-recipient-to-an-existing-share"></a>将收件人添加到现有共享

在 Azure 数据共享中，导航到已发送的共享，然后选择 " **邀请** " 选项卡。此处列出了此数据共享的所有邀请收件人。 若要添加新的，请单击 " **添加收件人**"。

![屏幕截图显示 "添加收件人" 选项。](./media/how-to/how-to-add-recipients/add-recipient.png)

页面右侧将弹出一个面板。 单击 " **添加收件人** "，然后在空行上填写新收件人的电子邮件。 请确保使用收件人的 Azure 登录电子邮件 (使用其电子邮件别名) 不起作用。 

![屏幕截图显示 "添加收件人" 窗格，你可以在其中添加和发送邀请。](./media/how-to/how-to-add-recipients/add-recipient-side.png)

单击 " **添加并发送邀请**"。 新收件人 () 会向此共享发送邀请电子邮件。

## <a name="next-steps"></a>后续步骤
详细了解如何 [删除共享邀请](how-to-delete-invitation.md)。
