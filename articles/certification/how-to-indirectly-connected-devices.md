---
title: 认证设备捆绑和间接连接的设备
titleSuffix: Azure Certified
description: 请参阅如何提交间接连接的设备进行认证。
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: da3110b562bc5ddbd37657f31cbdd3790a13b897
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975701"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>设备捆绑和间接连接的设备

为支持通过设备、SaaS 或 PaaS 产品/服务与 Azure 进行交互的设备，我们的提交门户 (https://www.certify.azure.com) ) 和设备目录 (https://devicecatalog.azure.com) ) 启用了捆绑和依赖项的概念，以促进并允许这些设备组合访问 Azure 认证设备程序。

根据所提供的产品系列和服务，在此情况下，可能需要组合使用以下步骤：


![创建项目依赖项](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>传感器和间接设备
许多传感器都需要通过设备连接到 Azure。 此外，可能会有多个兼容设备可与传感器设备配合使用。 若要满足这些方案，必须首先认证设备，然后认证通过设备传递信息的传感器。

提交组合的示例矩阵 ![Submission example](./media/indirect-connected-device/picture-2.png )

若要认证传感器（需要单独的设备）：
1.  首先，[认证设备](https://certify.azure.com)并发布到 Azure 认证设备目录
    - 如果有多个兼容的直通设备（如上例所示），请分别提交这些设备进行认证，并同时发布到目录中
2.  通过设备连接传感器后，提交传感器以进行认证
    * 在“设备详细信息”部分的“依赖项”选项卡中，设置以下值
        * 依赖项类型 =“硬件网关”
        * 依赖项 URL =“设备目录上设备的 URL 链接”
        * 在测试期间使用 =“是”
        * 添加任何面向客户的注释，这些注释应提供给看到设备目录中产品说明的用户。 （示例：“传感器需要系列 100 设备才能连接到 Azure”）

3.  如果希望将更多设备添加为此设备的可选设备，可以选择“+ 添加其他依赖项”。 然后，请遵循相同的指导，并注意在测试过程中切勿执行此操作。 在面向客户的注释中，请确保你的客户知道有其他与此传感器关联的设备可用（作为测试过程中使用的设备的替代项）。

![替换文字](./media/indirect-connected-device/picture-3.png "硬件依赖项类型")

## <a name="paas-and-saas-offerings"></a>PaaS 和 SaaS 产品/服务
作为产品组合的一部分，你可能拥有经过自己认证的设备，但你的设备还需要所在公司或其他第三方公司提供的其他服务。 若要添加此依赖项，请执行以下步骤：
1. 启动设备的提交过程
2. 在“依赖项”选项卡中，设置以下值
    - 依赖项类型 =“软件服务”
    - 服务名称 =“[你的产品名称]”
    - 依赖项 URL =“用于描述服务的产品页面的 URL 链接”
    - 添加任何面向客户的注释，这些注释应提供给看到 Azure 认证设备目录中产品说明的用户
3. 如果希望将其他软件、服务或硬件依赖项添加为此设备的可选依赖项，则可以选择“+ 添加其他依赖项”，并遵循相同的指导。

![软件依赖项类型](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>捆绑产品
捆绑产品列表只是对具有其他组件的设备的成功认证，这些组件将作为产品列表中捆绑包的一部分进行销售。 你可以提交包含附加组件（如温度传感器和相机传感器） (#1) 的设备，也可以提交包含直通设备 (#2) 的触摸传感器。 可以通过“组件”功能向列表中添加多个组件。

如果要执行此操作，请设置产品列表图像的格式，以表示此产品与其他组件一起提供。  此外，如果捆绑包需要其他服务进行认证，则需要通过服务依赖项来标识这些服务。
捆绑产品的示例矩阵

![捆绑包提交示例](./media/indirect-connected-device/picture-5.png )

有关如何在 Azure 认证设备门户中使用组件功能的更详细说明，请参阅[帮助文档](./how-to-using-the-components-feature.md)。 

如果是在同一产品中具有单独传感器的直通设备，请创建一个组件来反映直通设备，并创建另一个组件来反映传感器。 可以在“设备详细信息”部分的“产品详细信息”选项卡中将组件添加到项目中：

![添加组件](./media/indirect-connected-device/picture-6.png )

对于直通设备，将“组件类型”设置为“客户就绪产品”，然后填写与产品相关的其他字段。 示例：

![组件详细信息](./media/indirect-connected-device/picture-7.png )

对于传感器，请添加第二个组件，并将“组件类型”设置为“外围设备”，将“附件方法”设置为“离散”。 示例：

![第二个组件的详细信息](./media/indirect-connected-device/picture-8.png )

创建传感器组件后，请编辑详细信息，导航至“传感器”选项卡，然后添加传感器详细信息。 示例：

![传感器详细信息](./media/indirect-connected-device/picture-9.png )

完成项目详细信息，然后像正常情况一样提交设备进行认证。

