---
title: 如何使用 CLI 标记 Azure 虚拟机
description: 了解如何使用 Azure CLI 为虚拟机添加标签。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594935"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>如何使用 CLI 标记 VM

本文介绍如何使用 Azure CLI 标记 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 你还可以使用 Azure [PowerShell](tag-powershell.md)标记虚拟机。


您可以使用查看给定 VM 的所有属性，包括标记 `az vm show` 。

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

若要通过 Azure CLI 添加新的 VM 标记，可以使用 `azure vm update` 命令以及标记参数 `--set` ：

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

若要删除标记，可以使用 `--remove` 命令中的参数 `azure vm update` 。

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

既然我们已通过 Azure CLI 和门户将标记应用到资源中，那就让我们看一看使用情况详细信息，以在计费门户中查看标记。


后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。
- 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill](../cost-management-billing/understand/review-individual-bill.md)（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption](../cost-management-billing/manage/usage-rate-card-overview.md)（深入了解 Microsoft Azure 资源消耗）。
