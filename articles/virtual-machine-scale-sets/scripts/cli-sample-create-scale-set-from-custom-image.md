---
title: Azure CLI 示例 - 使用自定义 VM 映像
description: 此 Azure CLI 脚本创建使用自定义 VM 映像作为 VM 实例源的虚拟机规模集。
author: mamccrea
ms.author: mamccrea
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: aaf58fafa757f0d601609682b70a5aa9946d6362
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223553"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli"></a>使用 Azure CLI 基于自定义 VM 映像创建虚拟机规模集
此脚本创建使用自定义 VM 映像作为 VM 实例源的虚拟机规模集。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建资源组、虚拟机规模集和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/ad/group) | 创建用于存储所有资源的资源组。 |
| [az vmss create](/cli/azure/vmss) | 创建虚拟机规模集并将其连接到虚拟网络、子网和网络安全组。 负载均衡器也会被创建，以将流量分配到多个 VM 实例。 此命令还指定要使用的 VM 映像和管理凭据。  |
| [az group delete](/cli/azure/ad/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。
