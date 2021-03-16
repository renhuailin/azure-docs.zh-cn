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
ms.openlocfilehash: 66a709f15191a8142f10f15d825276ea2ba4b83f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487978"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>如何将工作区用于自定义 DNS 服务器

当使用具有专用终结点的 Azure 机器学习工作区时，可以[通过几种方法来处理 DNS 名称解析](../private-link/private-endpoint-dns.md)。 默认情况下，Azure 会自动处理工作区和专用终结点的名称解析。 如果改为使用自己的自定义 DNS 服务器，则必须为工作区手动创建 DNS 条目或使用条件转发器。

> [!IMPORTANT]
> 本文仅介绍如何查找这些条目的完全限定域名 (FQDN) 和 IP 地址，但不包括有关如何配置这些内容的 DNS 记录的信息。 请参阅 DNS 文档获取如何添加记录的信息。

## <a name="prerequisites"></a>先决条件

- 使用[你自己的 DNS 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)的 Azure 虚拟网络。

- 具有专用终结点的 Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 可熟练使用[训练和推理过程中的网络隔离](./how-to-network-security-overview.md)。

- 熟悉 [Azure 专用终结点 DNS 区域配置](../private-link/private-endpoint-dns.md)

- （可选）[Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="fqdns-in-use"></a>使用中的 FQDN
### <a name="these-fqdns-are-in-use-in-the-following-regions-eastus-southcentralus-and-westus2"></a>这些 FQDN 在以下区域中使用：eastus、southcentralus 和 westus2。
以下列表包含工作区使用的完全限定的域名 (FQDN)：

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* 如果创建计算实例，还需要使用工作区专用终结点的专用 IP 来添加 `<instance-name>.<region>.instances.azureml.ms` 的条目。

    > [!NOTE]
    > 只能从虚拟网络内访问计算实例。
    
### <a name="these-fqdns-are-in-use-in-all-other-public-regions"></a>所有其他公共区域中都使用这些 FQDN
以下列表包含工作区使用的完全限定的域名 (FQDN)：

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > 只能从虚拟网络内访问计算实例。

### <a name="azure-china-21vianet-regions"></a>Azure 中国世纪互联区域

以下 FQDN 适用于 Azure 中国世纪互联区域：

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > 此 FQDN 的工作区名称可能会被截断。 截断是为了使 FQDN 的字符小于或等于 63 个。
* `<instance-name>.<region>.instances.ml.azure.cn`
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

所有方法返回的信息是相同的；资源的 FQDN 和专用 IP 地址的列表。 以下示例来自某个全局 Azure 区域：

| FQDN | IP 地址 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> eastus、southcentralus 和 westus2 中的工作区需要的某些 FQDN 未显示在专用终结点显示的列表中。 下表列出了这些 FQDN，还需要将其添加到 DNS 服务器和/或 Azure 专用 DNS 区域：
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * 如果你有一个计算实例，请使用 `<instance-name>.<region>.instances.azureml.ms`，其中 `<instance-name>` 是计算实例的名称。 请使用工作区专用终结点的专用 IP 地址。 请注意，只能从虚拟网络内访问计算实例。
>
> 对于所有这些 IP 地址，请使用与从之前步骤中返回的 `*.api.azureml.ms` 项相同的地址。

下表显示 Azure 中国世纪互联区域的示例 IP：

| FQDN | IP 地址 |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |
## <a name="next-steps"></a>后续步骤

有关将 Azure 机器学习与虚拟网络结合使用的详细信息，请参阅[虚拟网络概述](how-to-network-security-overview.md)。

有关将专用终结点集成到 DNS 配置的详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。