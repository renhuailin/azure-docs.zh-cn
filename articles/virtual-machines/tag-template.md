---
title: 如何使用模板来标记 VM
description: 了解如何使用模板来标记虚拟机。
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: 92be32279b812d4a8ff3baad3e3ccc881fe20049
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695323"
---
# <a name="tagging-a-vm-using-a-template"></a>使用模板来标记 VM

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文介绍如何使用资源管理器模板在 Azure 中标记 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。

[此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags)将标记放置在以下资源中：计算（虚拟机）、存储（存储帐户）和网络（公共 IP 地址、虚拟网络和网络接口）。 此模板适用于 Windows VM，但经过改造后也可用于 Linux VM。

单击 [模板链接](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags) 中的 **部署至 Azure** 按钮。 此操作将导航到 [Azure 门户](https://portal.azure.com/)，可在其中部署此模板。

![使用标记进行简单部署](./media/tag/deploy-to-azure-tags.png)

此模板包括以下标记：*Department*、*Application* 和 *Created By*。 如果想要不同的标记名称，则可以直接在模板中添加/编辑这些标记。

![模板中的 Azure 标记](./media/tag/azure-tags-in-a-template.png)

如你所见，标记将被定义为键值对，用冒号 (:) 分隔。 必须按以下格式定义标记：

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

完成编辑后，使用选择的标记保存模板文件。

接下来，在“编辑参数”部分中，可以填写标记的值。 

![通过 Azure 门户编辑标记](./media/tag/edit-tags-in-azure-portal.png)

单击  “创建”使用标记值部署此模板。

### <a name="next-steps"></a>后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。
- 要了解标记如何帮助你管理 Azure 资源的使用，请参阅[了解 Azure 帐单](../cost-management-billing/understand/review-individual-bill.md)。
