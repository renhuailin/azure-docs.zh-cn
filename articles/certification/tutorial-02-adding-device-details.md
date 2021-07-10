---
title: Azure 认证设备计划 - 教程 - 添加设备详细信息
description: 有关在 Azure 认证设备门户上将设备详细信息添加到项目的分步指南
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 05/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: be6bcf84ebb9c979cd60e6ca6803b7a24a0944a1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108736269"
---
# <a name="tutorial-add-device-details"></a>教程：添加设备详细信息

现在，你已为设备创建了项目，并且一切准备就绪，可以开始认证过程了！ 首先，让我们添加你的设备详细信息。 这将包括你的客户可以在 Azure 认证设备目录上查看的技术规格，以及他们在做出决定后用于进行购买的营销详细信息。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用组件和依赖项功能添加设备详细信息
> * 上传设备的入门指南
> * 指定客户购买你的设备时使用的营销详细信息
> * 有选择性地标识任何行业认证

## <a name="prerequisites"></a>先决条件

* 应登录 [Azure 认证设备门户](https://certify.azure.com)，并为你的设备创建一个项目。 有关详细信息，请查看[教程](tutorial-01-creating-your-project.md)。
* 你应为设备提供 PDF 格式的入门指南。 我们提供了许多入门模板供你使用，具体取决于认证计划和你的首选语言。 模板位于我们的 GitHub [入门模板](https://aka.ms/GSTemplate "入门模板")位置。

## <a name="adding-technical-device-details"></a>添加设备的技术性详细信息

你的项目页的第一部分称为“输入设备详细信息”，可以在其中提供有关设备的核心硬件功能的信息，例如设备名称、说明、处理器、操作系统、连接选项、硬件接口、行业协议、物理尺寸，等等。 虽然许多字段是可选的，但如果你在设备通过认证后选择发布设备，则大多数此类信息会在 Azure 认证设备目录中提供给潜在客户。

1. 在项目摘要页的“输入设备详细信息”部分单击“`Add`”以打开设备详细信息部分。 你会看到有六个部分需要你完成。

![项目详细信息页的图像](./media/images/device-details-menu.png)

2. 在“`Basics`”选项卡下检查你之前创建项目时提供的信息。
1. 在“`Certifications`”选项卡下检查你为设备申请的认证。
1. 打开 `Hardware` 选项卡并添加至少一个描述你的设备的单独组件。 还可以查看关于[组件使用情况](how-to-using-the-components-feature.md)的指南。
1. 单击 `Save`。 然后，你将能够编辑组件设备并添加更高级的详细信息。
1. 添加有关操作条件的任何相关信息（例如 IP 分级、操作温度或安全认证）。

![硬件部分的图像](./media/images/hardware-section.png)

7. 在“`Additional product details`”下列出组件详细信息未涵盖的其他设备详细信息。
1. 如果你在任何组件字段中标记了“`Other`”，或者有特殊情况需要向 Azure 认证团队说明，请在“`Comments for reviewer`”部分提供一条澄清性的注释。
1. 打开 `Software` 选项卡，然后选择至少一个操作系统。
1. （开发套件设备需要，强烈建议所有其他设备使用）选择一个级别以指示将设备连接到 Azure 的预期设置过程。 如果选择级别 2，你将需要提供指向可用软件映像的链接。

![软件部分的图片](./media/images/software-section.png)

11. 如果你的设备需要额外的硬件或服务才能将数据发送到 Azure，请使用“`Dependencies`”选项卡列出任何依赖项。 还可以查看关于[列出依赖项](how-to-indirectly-connected-devices.md)的附加指南。
1. 如果你对已提供的信息满意，则可以使用“`Review`”选项卡了解已输入的完整设备详细信息的只读概述。
1. 单击页面顶部的“`Project summary`”，返回到摘要页。

![查看项目详细信息页](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>上传入门指南

入门指南是一个 PDF 文档，用于简化产品的设置、配置和管理。 它的用途是让客户能够轻松使用你的设备来连接和支持 Azure 上的设备。 在认证过程中，我们会要求合作伙伴为其最相关的认证计划提供 **一个** 入门指南。

1. 仔细检查你是否已根据提供的[模板](https://aka.ms/GSTemplate)在入门指南 PDF 中提供了所有要求的信息。 你使用的模板应取决于你申请的认证徽章。 （例如，IoT 即插即用设备将使用 IoT 即插即用模板。 仅申请 Azure 认证设备基线认证的设备将使用 Azure 认证设备模板。）
1. 单击项目摘要页的“入门”指南部分的“`Add`”。

![GSG 按钮的图像](./media/images/gsg-menu.png)

2. 单击“选择文件”以上传 PDF。
1. 在预览中查看文档的格式设置。
1. 单击“保存”按钮以保存你的上传。
1. 单击页面顶部的“`Project summary`”，返回到摘要页。

## <a name="providing-marketing-details"></a>提供营销详细信息

在此区域中，你将为设备提供可供客户使用的营销信息。 如果你选择发布已认证的设备，这些字段会显示在 Azure 认证设备目录中。

1. 单击“添加营销详细信息”部分的“`Add`”以打开营销详细信息页。

![营销详细信息部分的图像](./media/images/marketing-details.png)

1. 以 JPEG 或 PNG 格式上传将在目录中使用的产品照片。
1. 编写你的设备的简短说明，该说明会显示在目录的产品说明页上。
1. 指示设备的地域可用性。
1. 提供此设备的制造商营销页面的链接。 这应该是一个站点链接，该站点提供有关设备的其他信息。
    > [!Note]
    > 请确保提供的所有 URL 有效，或者在批准后发布时会处于活动状态。

1. 指定最多 3 个已针对其优化了你的设备的目标行业。
1. 提供最多 5 个设备分销商的信息。 这可能包括制造商自己的站点。

    > [!Note]
    > 如果未提供分销商产品页 URL，则目录中的“`Shop`”按钮将默认为为`Distributor page`提供的链接，该链接可能不特定于设备。 理想情况下，分销商 URL 应定位到特定页面，客户可以在该页面上购买设备，但这不是必需的。 如果分销商与制造商相同，则此 URL 可能与制造商的营销页面相同。

1. 单击“`Save`”以确认你的信息。
1. 单击页面顶部的“`Project summary`”，返回到摘要页。

## <a name="declaring-additional-industry-certifications"></a>声明额外的行业认证

你还可以宣传你的设备可能已获得的其他行业认证。 这些认证有助于进一步阐明设备的预期用途，并可在 Azure 认证设备目录上搜索。

1. 单击“提供行业认证”部分的“`Add`”。
1. 单击“`Add a certification`”以从常见的行业认证计划列表中进行选择。 如果你的产品没有获得我们的列表中的认证，则可以通过选择“`Other (please specify)`”来指定自定义字符串值。
1. （可选）向审阅者提供说明或注释。 但是，这些注释不可供在目录中公开查看。
1. 单击“`Save`”以确认你的信息。
1. 单击页面顶部的“`Project summary`”，返回到摘要页。

## <a name="next-steps"></a>后续步骤

现在，你已完成描述你的设备的过程！ 这将有助于 Azure 认证设备审查团队和你的客户更好地了解你的产品。 对你提供的信息感到满意后，你现在可以继续进入认证过程的测试阶段。
> [!div class="nextstepaction"]
> [教程：测试设备](tutorial-03-testing-your-device.md)
