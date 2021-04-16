---
title: Azure 认证设备计划 - 教程 - 测试设备
description: 有关在 Azure 认证设备门户上使用 AIC 服务测试设备的分步指南
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310948"
---
# <a name="tutorial-test-and-submit-your-device"></a>教程：测试并提交设备

认证过程的下一个主要阶段（尽管可以在添加设备详细信息之前完成）涉及对设备进行测试。 你将通过门户使用 Azure IoT 认证服务 (AICS) 根据我们的认证要求展示设备性能。 成功通过测试阶段后，你需要提交设备，供 Azure 认证团队进行最终审查和审批！

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用设备预配服务 (DPS) 将设备连接到 IoT 中心
> * 根据你选择的认证计划测试你的设备
> * 提交你的设备供 Azure 认证团队审查

## <a name="prerequisites"></a>先决条件

- 你应当登录到 [Azure 认证设备门户](https://certify.azure.com)并在其上为设备创建一个项目。 有关详细信息，请查看[教程](tutorial-01-creating-your-project.md)。
- （可选）建议你根据认证要求准备设备并手动验证其性能。 这是因为，如果你希望使用不同的设备代码或认证计划重新进行测试，则必须创建一个新项目。

## <a name="connecting-your-device-using-dps"></a>使用 DPS 连接设备

所有认证设备都需要展示使用 DPS 连接到 IoT 中心的功能。 以下步骤将引导你在门户上成功连接设备进行测试。

1. 若要开始测试阶段，请在项目摘要页上选择“`Connect & test`”链接：  

    ![“连接并测试”链接](./media/images/connect-and-test-link.png)

1. 你会在“连接并测试”页上看到需要进行的测试，具体取决于所选认证。 请查看这些测试，以确保你申请的是正确的认证计划。  

    ![“连接并测试”页面](./media/images/connect-and-test.png)

1. 使用设备预配服务 (DPS) 将设备连接到 IoT 中心。 DPS 支持“对称密钥”、“X.509 认证”和“受信任的平台模块(TPM)”连接选项。 所有认证都需要执行此步骤。

    - 若要详细了解如何通过 DPS 将设备连接到 Azure IoT 中心，请访问[预配设备概述](../iot-dps/about-iot-dps.md "设备预配服务概述")。
    
1. 如果使用对称密钥，则系统会要求你使用所提供的 DPS ID 范围、设备 ID、身份验证密钥和 DPS 终结点来配置 DPS。 否则，系统会要求你提供 X.509 证书或认可密钥。

1. 在通过 DPS 配置设备后，通过单击页面底部的“`Connect`”按钮来确认连接。 成功连接后，可通过单击“`Next`”按钮进入测试阶段。  

    ![“连接并测试”已连接](./media/images/connected.png)

## <a name="testing-your-device"></a>测试设备

成功将设备连接到 AICS 后，你便可以开始运行特定于所申请的认证计划的认证测试。

1. **对于 Azure 认证设备认证**：在“选择设备功能”选项卡中，需检查并选择你要在设备上运行的测试。
1. **对于 IoT 即插即用认证**：仔细检查将要在测试中检查的参数（已在设备型号中声明）。
1. **对于 Edge Managed 认证**：除了展示连接性之外，无需执行其他步骤。
1. 为指定的认证计划完成必要准备后，选择“`Next`”进入“测试”阶段。
1. 在页面上选择“`Run tests`”，开始对设备运行 AICS。
1. 收到通过测试的通知后，选择“`Finish`”返回到摘要页。

![已通过测试](./media/images/test-pass.png)

7. 如果你有其他问题或者在对 AICS 进行故障排除时需要帮助，请访问我们的故障排除指南。

> [!NOTE]
> 虽然你无需提交设备进行手动审核便可完成 IoT 即插即用和 Edge Managed 的在线认证过程，但 Azure 认证设备团队成员可能会联系你，以便进一步进行除自动化服务测试之外的设备验证。

## <a name="submitting-your-device-for-review"></a>提交需审查的设备

在“设备详细信息”部分填写所有必填字段并成功通过“连接并测试”过程中的自动化测试后，你现在可以通知 Azure 认证设备团队：你已准备好提交设备进行认证审查。

1. 在项目摘要页面上选择“`Submit for review`”：  

    ![“审查和认证”链接](./media/images/review-and-certify.png)

1. 在弹出窗口中确认你的提交。 提交设备后，所有设备详细信息均为只读，除非你请求进行编辑。 （请参阅[在发布后如何编辑设备信息](./how-to-edit-published-device.md)。）  

    ![“启动认证审查”对话框](./media/images/start-certification-review.png)

1. 提交项目后，项目摘要页会指示该项目正在由 Azure 认证团队进行`Under Certification Review`：  

    ![审查中](./media/images/review-and-certify-under-review.png)

1. 在 5-7 个工作日内，Azure 认证团队会向你在公司简介中提供的地址发送有关设备提交状态的电子邮件回复。

    - 已批准的提交  
        在你的项目经审查获得批准后，你会收到一封电子邮件。 该电子邮件会包含一组文件，其中包括 Azure 认证设备徽章、徽章使用指南，以及有关如何宣传你的设备已通过认证的其他信息。 祝贺你！

    - 待提交  
        如果你的项目未获得批准，你将能够更改项目详细信息，然后在准备就绪后重新提交设备进行认证。 我们会向你发送一封电子邮件，说明项目未获批准的原因以及重新提交项目进行认证的步骤。

## <a name="next-steps"></a>后续步骤

祝贺你！ 你的设备现在已成功通过所有测试，并已在 Azure 认证设备计划中获得批准。 现在，你可以将设备发布到我们的 Azure 认证设备目录中，客户可以放心地在其中购买你的产品。
> [!div class="nextstepaction"]
> [教程：发布设备](tutorial-04-publishing-your-device.md)

