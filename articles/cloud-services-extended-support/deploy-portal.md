---
title: 部署 Azure 云服务（外延支持）- Azure 门户
description: 使用 Azure 门户部署 Azure 云服务（外延支持）
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a147d4ca7b947c6a1cdfeec1587e9ae6b2d27fa0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448900"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>使用 Azure 门户部署 Azure 云服务（外延支持）
本教程介绍如何使用 Azure 门户创建云服务（外延支持）部署。 

## <a name="before-you-begin"></a>在开始之前

查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)，并创建关联的资源。 

## <a name="deploy-a-cloud-services-extended-support"></a>部署云服务（外延支持） 
1. 登录到 [Azure 门户](https://portal.azure.com)

2.  使用 Azure 门户顶部的搜索栏，搜索云服务（外延支持）并将其选中。

    :::image type="content" source="media/deploy-portal-1.png" alt-text="图像显示 Azure 门户中的“所有资源”边栏选项卡。":::
 
3.  在“云服务(外延支持)”窗格中，选择“创建”。 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="图像显示从市场购买云服务的情况。":::

4. “云服务(外延支持)”创建窗口将打开“基本信息”选项卡。 
    - 选择订阅。
    - 选择一个资源组，或创建新的资源组。
    - 输入云服务（外延支持）部署所需的名称。
        - 云服务的 DNS 名称分别由公共 IP 地址的 DNS 名称标签指定，并且可以在“配置”选项卡的“公共 IP”部分中修改。
    -  选择要部署到的区域。

    :::image type="content" source="media/deploy-portal-3.png" alt-text="图像显示“云服务(外延支持)”主页边栏选项卡。":::

5. 添加云服务配置、包和定义文件。 可从 Blob 存储添加现有文件，也可从本地计算机上传这些文件。 如果从本地计算机上传，则这些文件将存储在存储帐户中。 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="图像显示创建过程中“基本信息”选项卡的“上传”部分。":::

6. 完成所有字段后，转到并完成“配置”选项卡。 
    - 选择要与云服务关联的虚拟网络或创建新网络。 
        - 云服务（外延支持）部署必须在虚拟网络中。 还必须在 `NetworkConfiguration` 部分下的服务配置文件 (.cscfg) 中引用虚拟网络。
    - 选择要与云服务关联的现有公共 IP 地址或创建新的 IP 地址。
        - 如果在服务定义文件 (.csdef) 中定义了 IP 输入终结点，则需要为云服务创建公共 IP 地址。 
        - 云服务（外延支持）仅支持基本 IP 地址 SKU。
        - 如果服务配置 (.cscfg) 包含保留的 IP 地址，则必须将公共 IP 的分配类型设置为“静态”。 
        - （可选）通过更新与云服务关联的公共 IP 地址的 DNS 标签属性，为云服务终结点分配 DNS 名称。  
    - （可选）启动云服务。 创建后选择“启动”，或者不立即启动服务。
    - 选择 Key Vault 
        - 指定服务配置文件 (.cscfg) 中的一个或多个证书时，需要使用 Key Vault。 在你选择 Key Vault 时，我们将根据所选证书的指纹在你的服务配置文件 (.cscfg) 中查找这些证书。 如果 Key Vault 中缺少任何证书，你可立即上传它们，然后单击“刷新”。   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="图像显示创建云服务（外延支持）时 Azure 门户中的“配置”边栏选项卡。":::

7. 完成所有字段后，移动到“查看和创建”选项卡，来验证部署配置并创建云服务（外延支持）。

## <a name="next-steps"></a>后续步骤 
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)
