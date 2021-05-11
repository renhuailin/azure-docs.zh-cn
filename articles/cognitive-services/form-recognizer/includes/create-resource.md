---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 734b548953c4ede5e7d09feb0042509ae5528e82
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108026303"
---
访问 Azure 门户并<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="创建新的表单识别器资源" target="_blank">创建新的表单识别器资源</a>。 在“创建”窗格中提供以下信息：

| 项目详细信息   | 说明   |
|--|--|
| **订阅** | 选择已被授予访问权限的 Azure 订阅。 |
| **资源组** | 将包含资源的 [Azure 资源组](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 可以创建新组或将其添加到预先存在的组。 |
| **区域** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
| **名称** | 资源的描述性名称。 建议使用描述性名称，例如“MyNameFormRecognizer”。 |
| **定价层** | 资源的费用取决于所选的定价层和使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。

> [!NOTE]
> 在 Azure 门户中创建认知服务资源时，可以选择创建多服务订阅密钥（跨多个认知服务使用）或单服务订阅密钥（仅与某个特定的认知服务配合使用）。 多服务订阅中当前不包含表单识别器。

表单识别器资源完成部署以后，请在门户的“所有资源”列表中找到并选中它。 你的密钥和终结点将位于资源的“密钥和终结点”页的“资源管理”下。 请先将它们保存到临时位置，然后继续。
