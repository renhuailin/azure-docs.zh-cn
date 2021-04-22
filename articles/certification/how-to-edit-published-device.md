---
title: 如何编辑已发布的 Azure 认证设备
description: 通过 Azure 认证设备计划对设备进行认证和发布之后，可使用此指南编辑设备信息。
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304437"
---
# <a name="edit-your-published-device"></a>编辑已发布的设备

将设备认证并发布到 Azure 认证设备目录后，你可能需要更新设备详细信息。 这可能是由于要对分发服务器列表进行更新、对购买页 URL 进行更改或对硬件规范进行更新（如操作系统版本或新的组件添加）。 使用 Azure 认证设备门户，可以轻松更新设备信息，而无需从目录中删除产品。

## <a name="prerequisites"></a>必备条件

- 应已登录 [Azure 认证设备门户](https://certify.azure.com)并为设备创建了一个 **已批准** 的项目。 如果没有认证的设备，可以查看此[教程](tutorial-01-creating-your-project.md)。

## <a name="editing-your-published-project"></a>编辑已发布的项目

在项目摘要上，应会注意到你的项目处于只读模式，因为它已经过评审并被接受。 若要进行更改，需要请求对项目进行编辑，并由 Azure 认证团队再次批准申请的更新。

1. 单击页面顶部的 `Request Metadata Edit` 按钮  

    ![请求元数据更新](./media/images/request-metadata-edit.png)

1. 请在页面上确认指示你需要在编辑后提交产品以供审查的通知。
    > [!NOTE]
    > 确认此编辑后，如果设备已发布，不会从 Azure 认证设备目录中删除设备。 目录中将保留之前版本的产品，直到重新发布设备为止。

1. 确认此警告后，可以编辑设备详细信息。 请确保在 `Device Details` 的 `Comments for Reviewer` 部分中保留一条有关已更改内容的注释。

    ![元数据编辑说明](./media/images/edit-notes.png)

1. 在项目摘要页面上，单击 `Submit for review` 以便 Azure 认证团队重新批准你所做的更改。
1. 查看并批准所做的更改后，可以通过门户将更改重新发布到目录（请参阅[教程](./tutorial-04-publishing-your-device.md)）。

## <a name="next-steps"></a>后续步骤

现已成功在 Azure 认证设备目录中编辑设备。 可查看对目录所做的更改，或认证另一台设备！
- [Azure 认证设备目录](https://devicecatalog.azure.com/)
- [开始认证设备](./tutorial-01-creating-your-project.md)
