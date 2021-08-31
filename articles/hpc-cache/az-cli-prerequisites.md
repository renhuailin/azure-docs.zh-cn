---
title: Azure HPC 缓存的 Azure CLI 先决条件
description: 设置步骤，然后才能使用 Azure CLI 来创建或修改 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 8e153a513219d59ac3a5825da9c4f59179aae99f
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121258"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>设置适用于 Azure HPC Cache 的 Azure CLI

使用 Azure CLI 创建或管理 Azure HPC 缓存之前，请按照以下步骤准备环境。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC 缓存需要 2.7 或更高版本的 Azure CLI。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="set-default-resource-group-optional"></a>设置默认资源组（可选）

大多数 hpc 缓存命令要求传递缓存的资源组。 可以使用 [az config](/cli/azure/reference-index#az_config) 设置默认资源组。

## <a name="next-steps"></a>后续步骤

安装 Azure CLI 扩展并登录后，可以使用 Azure CLI 来创建和管理 Azure HPC 缓存系统。

* [创建 Azure HPC 缓存](hpc-cache-create.md)
* [Azure CLI hpc 缓存文档](/cli/azure/hpc-cache)
