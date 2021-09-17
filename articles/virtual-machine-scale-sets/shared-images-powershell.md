---
title: 使用共享 VM 映像在 Azure PowerShell 中创建规模集
description: 了解如何使用 Azure PowerShell 在 Azure 中创建用于部署虚拟机规模集的共享 VM 映像。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: 876f38b06b3166eea8069a149c987db2c88206c9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693089"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>通过 Azure PowerShell 创建和使用虚拟机规模集的共享映像

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

创建规模集时，需指定部署 VM 实例时要使用的映像。 共享映像库服务大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

库是顶级资源，它提供完整的 Azure 基于角色的访问控制 (Azure RBAC)。 你可以控制映像的版本，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。 

共享映像库功能具有多种资源类型。 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>准备阶段

下列步骤详细说明如何将现有 VM 转换为可重用自定义映像，以便将其用于创建新 VM 实例。

完成本文中的示例，必须具有现有托管映像。 可以按照[教程：通过 Azure PowerShell 创建和使用虚拟机规模集的自定义映像](tutorial-use-custom-image-powershell.md)根据需要创建。 如果托管映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>后续步骤

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

有关共享映像库的详细信息，请参阅[概述](../virtual-machines/shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](../virtual-machines/troubleshooting-shared-images.md)。
