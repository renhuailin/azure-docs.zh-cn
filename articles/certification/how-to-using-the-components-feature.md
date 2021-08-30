---
title: 如何在 Azure 认证设备门户中使用组件特征
description: 本指南介绍如何充分利用“设备详细信息”部分中的组件特征来准确描述设备
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 05/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 494ff350866d8de1386a5fd98539f0e3e5ff2c3f
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126295"
---
# <a name="add-components-on-the-portal"></a>在门户中添加组件

在完成[向认证项目添加设备详细信息的教程](tutorial-02-adding-device-details.md)时，你将需要描述设备的硬件规格。 这样做，用户可突出强调组成你的设备的多个单独的硬件产品（称为“组件”）， 这样，你可更好地推广配有附加硬件的设备，客户也可根据这些特征在目录中搜索来找到合适的产品。

## <a name="prerequisites"></a>先决条件

- 应登录 [Azure 认证设备门户](https://certify.azure.com)，并为你的设备创建一个项目。 有关详细信息，请查看[教程](tutorial-01-creating-your-project.md)。

## <a name="how-to-add-components"></a>如何添加组件

提交来进行认证的每个项目都将包含一个“客户可用产品”组件（在许多情况下，它将表示整个产品本身）。 为了更好地了解“客户可用产品”组件类型的特性，请查看我们的[认证术语表](./resources-glossary.md)。 为了准确捕捉到你的设备，所有其他组件都由你自行决定是否要包括在内。

1. 在“硬件”选项卡上选择“`Add a component`”。

    ![“添加组件”链接](./media/images/add-component-new.png)

1. 填写组件的相关表单域。

    ![“组件详细信息”部分](./media/images/component-details-section.png)

1. 使用页面底部的`Save Product Details`按钮保存信息：  

    ![“保存产品详细信息”按钮](./media/images/save-product-details-button.png)

1. 保存组件后，可进一步定制它支持的硬件功能。 选择组件名称旁的`Edit`链接。  

    ![“编辑组件”按钮](./media/images/component-edit.png)

1. 在适当的位置提供相关的硬件功能信息。  

    ![可编辑组件部分的图像](./media/images/component-selection-area.png)  

    可编辑组件字段（如上所示）包括：

    - **常规**：硬件详细信息，如处理器和安全硬件
    - **连接性**：连接选项、协议和接口，如收音机和 GPIO
    - **加速器**：指定硬件加速，如 GPU 和 VPU
    - **传感器**：指定可用传感器，如 GPS 和振动
    - **其他规格**：有关设备的额外信息，如实际尺寸和存储/电池信息

1. 在“产品详细信息”页面底部选择`Save Product Details`。

## <a name="component-use-requirements-and-recommendations"></a>组件使用要求和建议

你可能对要包含多少组件或要使用什么组件类型有疑问。 下面几个示例场景显示了你可能要认证的设备，以及你可如何使用组件特征。

| 产品类型                                       | 否。 组件 | 组件 1/附件类型      | 组件 2+/附件类型                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| 成品                                   | 1          | 客户可用产品，独立 | 空值                                              |
| 具有可拆卸外设的成品 | 2 或更大  | 客户可用产品，独立 | 外设/独立或集成              |
| 具有集成组件的成品  | 2 或更大  | 客户可用产品，独立 | 选择适当的类型/独立或集成 |
| 解决方案就绪开发工具包                             | 大于等于 1  | 客户可用产品或开发板，离散或集成| 选择适当的类型/独立或集成 |

## <a name="example-component-usage"></a>示例组件用法

下面的示例演示了一个名为 Contoso 的 OEM 如何使用组件特征来认证其产品（称为 Falcon）。

1. Falcon 是一个完全独立的设备，不会集成到更大的产品中。
    1. 否。 组件编号：1
    1. 组件设备类型：客户可用产品
    1. 附件类型：独立

     ![客户可用产品的图像](./media/images/customer-ready-product.png)

1. Falcon 是一种设备，配有 INC Electronics 制造的集成外设照相机模块，该模块通过 USB 连接到 Falcon。
    1. 否。 组件编号：2
    1. 组件设备类型：客户可用产品、外设
    1. 附件类型：独立、集成
    
    > [!Note]
    > 外设组件被视为是集成的，因为它不可移动。

     ![外设示例组件的图像](./media/images/peripheral.png)

1. Falcon 是一种设备，配有 INC Electronics 制造的集成系统模块，该模块使用 Espressif 公司提供的内置处理器 Apollo52，并具有 ARM64 体系结构。
    1. 否。 组件编号：2
    1. 组件设备类型：客户可用产品、系统模块
    1. 附件类型：独立、集成

    > [!Note]
    > 外设组件被视为是集成的，因为它不可移动。 SoM 组件还包含处理器信息。

     ![系统模块示例组件的图像 ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>其他提示

我们在下面提供了有关组件使用策略的更多详细说明。 如果你对相应的组件用法有任何疑问，请通过 [iotcert@microsoft.com](mailto:iotcert@microsoft.com) 与我们的团队联系，我们将非常乐意提供帮助！

1. 一个项目只能包含一个客户可用产品组件。 如果要对具有两台独立设备的项目进行认证，应对这些设备分别认证。
1. 使用还是不使用组件向潜在客户推广你设备的功能主要取决于你。
1. 在我们审查你的设备期间，Azure 认证团队将只要求至少列出一个客户可用产品组件。 但是，如果详细信息不清楚或似乎缺少详细信息（例如未提供客户可用产品类型的组件制造商），我们可能会请求对组件信息进行编辑。

## <a name="next-steps"></a>后续步骤

现在你已准备好使用我们的组件特征，可开始填写设备详细信息或编辑项目来使其更清晰了。

- [教程：添加设备详细信息](tutorial-02-adding-device-details.md)
- [编辑已发布的设备](how-to-edit-published-device.md)
