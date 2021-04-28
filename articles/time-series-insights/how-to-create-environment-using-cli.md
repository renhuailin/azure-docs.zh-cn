---
title: 使用 Azure CLI 创建 Azure 时序见解第 2 代环境 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解如何使用 Azure CLI 在 Azure 时序见解第 2 代中设置环境。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 07e7f21bd706d9f83d2813b0ab491b01fbc53672
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867558"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure 时序见解第 2 代环境

本文档将指导你完成创建新的时序见解第 2 代环境的操作。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

* 为环境的[冷存储](./concepts-storage.md#cold-store)创建 Azure 存储帐户。 此帐户旨在用于长期保留和历史数据分析。

> [!NOTE]
> 在代码中，将 `mytsicoldstore` 替换为冷存储帐户的唯一名称。

首先，创建存储帐户：

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>创建环境

现在存储帐户已创建，其名称和管理密钥已分配给变量，可以运行以下命令来创建 Azure 时序见解环境了：

> [!NOTE]
> 在代码中，将以下内容替换为方案的唯一名称：
>
> * 将 `my-tsi-env` 替换为你的环境名称。
> * 将 `my-ts-id-prop` 替换为时序 ID 属性的名称。

> [!IMPORTANT]
> 环境的时序 ID 类似于数据库分区键。 时序 ID 还充当时序模型的主键。
>
> 有关详细信息，请参阅[选择时序 ID 的最佳做法](./how-to-select-tsid.md)。

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>删除 Azure 时序见解环境

可以使用 Azure CLI 删除单个资源（例如时序见解环境），或删除资源组及其所有资源（包括任何时序见解环境）。

若要[删除时序见解环境](/cli/azure/tsi/environment#az_tsi_environment_delete)，请运行以下命令：

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

若要[删除存储帐户](/cli/azure/storage/account#az_storage_account_delete)，请运行以下命令：

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

若要[删除资源组](/cli/azure/group#az_group_delete)及其所有资源，请运行以下命令：

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>后续步骤

* 了解 Azure 时序见解第 2 代环境的[流式引入事件源](./concepts-streaming-ingestion-event-sources.md)。
* 了解如何连接到 [IoT 中心](./how-to-ingest-data-iot-hub.md)
