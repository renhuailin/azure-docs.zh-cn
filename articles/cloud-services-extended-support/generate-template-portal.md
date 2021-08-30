---
title: 使用 Azure 门户为云服务生成 ARM 模板（扩展支持）
description: 使用 Azure 门户为云服务（外延支持）生成和下载 ARM 模板和参数文件
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 6654d1e167eefea1544262dab279b15b87b6b2ba
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460288"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>使用 Azure 门户为云服务生成 ARM 模板（扩展支持）

本文介绍如何从云服务的 [Azure 门户](https://portal.azure.com)下载 ARM 模板和参数文件。 可以通过 PowerShell 在部署中使用 ARM 模板和参数文件来创建或更新云服务

## <a name="get-arm-template-via-portal"></a>通过门户获取 ARM 模板

  1. 转到 Azure 门户并[创建新的云服务](deploy-portal.md)。 添加云服务配置、包和定义文件。 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="图像显示创建过程中“基本信息”选项卡的“上传”部分。":::
  
  2. 完成所有字段后，转到“审阅并创建”选项卡以验证部署配置，然后单击“下载自动化模板”以创建云服务（外延支持）。
    :::image type="content" source="media/download-template-portal-1.png" alt-text="此图显示了如何在 Azure 门户的“云服务(外延支持)”下下载模板。":::
  
  3. 下载模板和参数文件。 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="此图显示了在 Azure 门户中下载模板文件的情况。":::
  
  4. 从“审阅并创建”选项卡复制“包 SAS URI”和“配置 SAS URI”，并将其添加到 parameter.json 文件。 现在，可以使用这些文件通过 PowerShell 创建新的云服务。
    :::image type="content" source="media/download-template-portal-2.png" alt-text="此图显示了 Azure 门户上的“包 SAS URI”和“配置 SAS URI”参数。":::
  
## <a name="next-steps"></a>后续步骤 
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)部署云服务（外延支持）
  
