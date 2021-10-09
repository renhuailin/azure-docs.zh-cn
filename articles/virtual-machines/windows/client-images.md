---
title: 在 Azure 中使用 Windows 客户端映像
description: 如何使用 Visual Studio 订阅权益，在 Azure 中针对开发/测试方案部署 Windows 7、Windows 8 或 Windows 10
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: e75b06655fc6fe34be24b295be3d9255bc2b27fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600240"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>在 Azure 中使用 Windows 客户端实现开发/测试方案

**适用于：** :heavy_check_mark: Windows VM 

如果有相应的 Visual Studio（以前为 MSDN）订阅，可以在 Azure 中使用 Windows 7、Windows 8 或 Windows 10 企业版 (x64) 实施开发/测试方案。 

若要在生产环境中运行 Windows 10，请参阅[如何使用多租户托管权限在 Azure 上部署 windows 10](windows-desktop-multitenant-hosting-deployment.md)。


## <a name="subscription-eligibility"></a>订阅条件
有效的 Visual Studio 订阅者（已获得 Visual Studio 订阅许可证的用户）可以使用 Windows 客户端映像进行开发和测试。 你可以在自己的硬件或 Azure 虚拟机上使用 Windows 客户端映像。

某些 Windows 客户端映像可以从 Azure 市场中获取。 属于任一产品类型的 Visual Studio 订户也可以[准备和创建](prepare-for-upload-vhd-image.md) 64 位 Windows 7、Windows 8 或 Windows 10 映像，并[上传到 Azure](upload-generalized-managed.md)。

## <a name="eligible-offers-and-client-images"></a>符合条件的产品和客户端映像
下表详细描述了可通过 Azure 市场部署 Windows 客户端映像的产品 ID。 只有在以下产品中才能看到 Windows 客户端映像。 

> [!NOTE]
> 映像产品/服务位于 Azure 市场中的“Windows 客户端”下。 在搜索 Visual Studio 订阅者可用的客户端映像时，请使用“Windows 客户端”。 如果需要购买 Visual Stuido 订阅，请参阅[购买 Visual Studio](https://visualstudio.microsoft.com/vs/pricing/?tab=business) 上的各种选项

| 产品名称 | 产品编号 | 可用客户端映像 | 
|:--- |:---:|:---:|
| [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |
| [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |
| [Visual Studio Professional 订户](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |
| [Visual Studio Test Professional 订户](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |
| [Visual Studio Enterprise 订户](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |
| [Visual Studio Enterprise (BizSpark) 订户](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |
| [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 企业版 N (x64) <br> Windows 8.1 企业版 N (x64) <br> Windows 7 企业版 N SP1 (x64) |

## <a name="check-your-azure-subscription"></a>检查 Azure 订阅
如果你不知道自己的产品/服务 ID，则可以通过 Azure 门户获取。  
- 在“订阅”窗口中：![Azure 门户中的产品/服务 ID 详细信息](./media/client-images/offer-id-azure-portal.png) 
- 或者，单击“计费”，并单击订阅 ID。 产品 ID 会显示在“计费”窗口中。 
- 还可以从 Azure 帐户门户的[“订阅”选项卡](https://account.windowsazure.com/Subscriptions)中查看产品/服务 ID：![Azure 帐户门户中的产品/服务 ID 详细信息](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>后续步骤
现在，可以使用 [PowerShell](quick-create-powershell.md)、[Resource Manager 模板](ps-template.md)或 [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) 部署 VM。
