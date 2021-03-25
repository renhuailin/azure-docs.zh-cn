---
title: 邀请用户加入视频索引器 - Azure
titleSuffix: Azure Media Services
description: 本文介绍如何将用户邀请到视频索引器。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 02/03/2021
ms.author: juliako
ms.openlocfilehash: 7d3415ffad4820d8651841398ec6d47352869ce2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100534718"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>快速入门：邀请用户加入视频索引器

若要与同事进行协作，可以邀请他们加入你的视频索引器帐户。 

> [!NOTE]
> 只有帐户管理员可以添加或删除用户。

## <a name="invite-new-users"></a>邀请新用户

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。 请确保使用管理员帐户进行连接。
1. 如果你是管理员，可以看到右上角的“共享帐户”按钮。 单击该按钮即可邀请用户。 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="共享帐户":::
1. 在“与他人共享此帐户”对话框中，输入要邀请到视频索引器帐户的人员的电子邮件地址：

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="邀请用户加入此帐户":::  
1. 按下“邀请”后，用户将添加到待接受邀请的列表中。 <br/>对于尚未加入帐户的每个被邀请者，可从两个选项中进行选择：“删除邀请”或“复制邀请 URL” 。

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="待接受邀请":::  
1. 当被邀请者加入帐户后，你将看到三个选项可供选择。 两个角色选项：参与者（默认）或所有者 。 当然，可以选择按“删除”来删除被邀请者。

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="已加入的被邀请者":::  

    删除时，用户不会收到通知。 删除后，用户将无权登录。

## <a name="manage-roles-invite-more-users"></a>管理角色，邀请更多用户

除了通过单击“共享帐户”打开“与他人共享此帐户”对话框（如上所述）外，还可以从“设置”中执行此操作  。

1. 按下打开帐户中的“设置”按钮。 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="帐户设置":::  
1. 单击“管理角色”按钮。
1. 若要邀请其他用户，请单击“邀请更多人员加入此帐户”。

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="邀请多个用户":::  
1. 按下“邀请更多人员加入此帐户”后，将出现“邀请”对话框
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="邀请用户加入此帐户":::  

## <a name="next-steps"></a>后续步骤

现在可以使用[视频索引器](video-indexer-view-edit.md)网站或[视频索引器开发人员门户](video-indexer-use-apis.md)来查看视频见解。

## <a name="see-also"></a>另请参阅

- [视频索引器概述](video-indexer-overview.md)
- [如何注册并上传第一个视频](video-indexer-get-started.md)
- [开始使用 API](video-indexer-use-apis.md)
