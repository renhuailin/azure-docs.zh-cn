---
title: 使用自定义 DNS 服务器
titleSuffix: Azure Machine Learning
description: 如何配置自定义 DNS 服务器以使用 Azure 机器学习工作区和专用终结点。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2215c47fcd250a9ac1d6621f7e4b434bd33b3832
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871089"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>如何将工作区用于自定义 DNS 服务器

将 Azure 机器学习工作区与专用终结点一起使用时， [可以通过多种方式来处理 DNS 名称解析](../private-link/private-endpoint-dns.md)。 默认情况下，Azure 会自动处理工作区和专用终结点的名称解析。 如果你改为 _使用你自己的自定义 dns 服务器_ _，则必须手动创建 dns 条目或对工作区使用条件转发器。

> [!IMPORTANT]
> 本文仅介绍如何查找这些条目的完全限定域名 (FQDN) 和 IP 地址，但不包括有关如何配置这些内容的 DNS 记录的信息。 请参阅 DNS 文档获取如何添加记录的信息。

## <a name="prerequisites"></a>先决条件

- 使用[你自己的 DNS 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)的 Azure 虚拟网络。

- 具有专用终结点的 Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 熟悉 [在定型 & 推理期间使用网络隔离](./how-to-network-security-overview.md)。

- 熟悉 [Azure 专用终结点 DNS 区域配置](../private-link/private-endpoint-dns.md)

- （可选）[Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="fqdns-in-use"></a>使用的 Fqdn
### <a name="these-fqdns-are-in-use-in-the-following-regions-eastus-southcentralus-and-westus2"></a>这些 Fqdn 在以下区域中使用： eastus、default-machinelearning-southcentralus 和 westus2。
以下列表包含工作区使用的 (FQDN) 的完全限定的域名：

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* 如果创建计算实例，还需要使用工作区专用终结点的专用 IP 来添加 `<instance-name>.<region>.instances.azureml.ms` 的条目。

    > [!NOTE]
    > 只能从虚拟网络内访问计算实例。
    
### <a name="these-fqdns-are-in-use-in-all-other-regions"></a>这些 Fqdn 在所有其他区域中使用
以下列表包含工作区使用的 (FQDN) 的完全限定的域名：

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > 只能从虚拟网络内访问计算实例。

## <a name="find-the-ip-addresses"></a>查找 IP 地址

若要在 VNet 中查找 FQDN 的内部 IP 地址，请使用以下方法之一：

> [!NOTE]
> 根据你的配置，完全限定的域名和 IP 地址会有所不同。 例如，域名中的 GUID 值将特定于你的工作区。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 机器学习工作区。
1. 从“设置”部分，选择“专用终结点连接” 。
1. 选择显示的“专用终结点”列中的链接。
1. 页面底部显示了工作区专用终结点的完全限定的域名 (FQDN) 和 IP 地址的列表。

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="门户中的 FQDN 列表":::

---

所有方法返回的信息是相同的；资源的 FQDN 和专用 IP 地址的列表。

| FQDN | IP 地址 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> 有些 Fqdn 不显示在私有终结点列出的中，但在 eastus、default-machinelearning-southcentralus 和 westus2 的工作区中是必需的。 下表列出了这些 Fqdn，还必须将其添加到 DNS 服务器和/或 Azure 专用 DNS 区域：
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * 如果你有一个计算实例，请使用 `<instance-name>.<region>.instances.azureml.ms`，其中 `<instance-name>` 是计算实例的名称。 请使用工作区专用终结点的专用 IP 地址。 请注意，只能从虚拟网络内访问计算实例。
>
> 对于所有这些 IP 地址，请使用与从之前步骤中返回的 `*.api.azureml.ms` 项相同的地址。

## <a name="next-steps"></a>后续步骤

有关使用虚拟网络 Azure 机器学习的详细信息，请参阅 [虚拟网络概述](how-to-network-security-overview.md)。

有关将专用终结点集成到 DNS 配置的详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。