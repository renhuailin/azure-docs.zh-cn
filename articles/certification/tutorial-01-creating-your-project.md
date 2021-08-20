---
title: Azure 认证设备计划 - 教程 - 创建项目
description: 有关在 Azure 认证设备门户上创建项目的指南
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 06/22/2021
ms.custom: template-tutorial
ms.openlocfilehash: f634cb35590a428613858a88a71db1d0792ceb06
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967495"
---
# <a name="tutorial-create-your-project"></a>教程：创建项目

祝贺你选择通过 Azure 认证设备计划认证你的设备！ 你现已为设备选择了适当的认证计划，接下来可以在门户上开始操作了。

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 登录到 [Azure 认证设备门户](https://certify.azure.com/)
> * 为设备创建新的认证项目
> * 指定项目的基本设备详细信息

## <a name="prerequisites"></a>先决条件

- 有效的工作/学校 [Azure Active Directory 帐户](../active-directory/fundamentals/active-directory-whatis.md)。
- 已验证的 Microsoft 合作伙伴网络 (MPN) 帐户。 如果你没有 MPN 帐户，请在开始之前[加入合作伙伴网络](https://partner.microsoft.com/)。 

> [!NOTE] 
> 如果在设置或验证 MPN 帐户时遇到问题，请参阅[合作伙伴中心支持](/partner-center)文档。


## <a name="signing-into-the-azure-certified-device-portal"></a>登录到 Azure 认证设备门户

若要开始，必须先登录到门户，你将在其中提供设备信息、完成认证测试，并管理将设备发布到 Azure 认证设备目录的操作。

1. 转到 [Azure 认证设备门户](https://certify.azure.com)。
1. 在左侧选择 `Company profile`，并更新制造商信息。
   ![公司档案部分](./media/images/company-profile.png)
1. 接受计划协议以开始创建项目。

## <a name="creating-your-project-on-the-portal"></a>在门户上创建项目

完成门户中的所有设置后，接下来可以开始执行认证过程。 首先，必须为设备创建一个项目。

1. 在主屏幕上选择 `Create new project`。 这会打开一个窗口，可在其中添加下一部分所需的基本设备信息。

 ![“创建新项目”按钮的插图](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>确定基本设备信息

然后，必须提供基本设备信息。 稍后可以编辑此信息。

1. 填写 `Basics` 部分下请求的字段。 有关 **必填** 字段的说明，请参阅下表：

    | 字段                  | 说明                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | 项目名称           | 不会在 Azure 认证设备目录中显示的内部名称                                                        |
    | 设备名称            | 设备的公用名称                                                                                                |
    | 设备类型            | 成品或解决方案就绪开发人员工具包的规格。     有关术语的详细信息，请参阅[认证术语表](./resources-glossary.md)。                                                                     |
    | 设备分类           | 网关、传感器或其他分类。  有关术语的详细信息，请参阅[认证术语表](./resources-glossary.md)。                                                                    |
    | 设备源代码 URL | 如果要认证解决方案就绪开发人员工具包，则此字段是必填的；否则是可选的。 URL 必须指向设备代码的 GitHub 位置。 |

    > [!Note]
    > 如果你正在营销 Microsoft 服务（例如 Azure Sphere），请确保你的设备名称符合 Microsoft [品牌准则](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks)。

1. 选择 `Next` 按钮转到 `Certifications` 选项卡。

    ![“创建新项目”窗体中“认证”选项卡的插图](./media/images/create-new-project-certificationswindow.png)

1. 指定要为设备实现哪种认证。
1. 选择 `Create`，然后，新项目将会保存并显示在门户的主页中。

    ![项目表的插图](./media/images/project-table.png)

1. 在表中选择“项目名称”。 这会启动项目摘要页，在其中可以添加和查看有关设备的其他详细信息。

    ![项目详细信息页的插图](./media/images/device-details-section.png)

## <a name="next-steps"></a>后续步骤

现在，你已准备好添加设备详细信息，并使用我们的认证服务测试你的设备。 请转到下一篇文章来了解如何编辑设备详细信息。
> [!div class="nextstepaction"]
> [教程：添加设备详细信息](tutorial-02-adding-device-details.md)