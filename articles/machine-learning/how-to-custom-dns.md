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
ms.date: 08/03/2021
ms.topic: how-to
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: 74c8fd2c096d8a4b236d9f26bdd27737fac897ca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632987"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>如何将工作区用于自定义 DNS 服务器

当使用具有专用终结点的 Azure 机器学习工作区时，可以[通过几种方法来处理 DNS 名称解析](../private-link/private-endpoint-dns.md)。 默认情况下，Azure 会自动处理工作区和专用终结点的名称解析。 如果要使用自己的自定义 DNS 服务器，需要为工作区手动创建 DNS 条目或使用条件转发器。

> [!IMPORTANT]
> 本文介绍了如果你想在 DNS 解决方案中手动注册 DNS 记录，如何为这些条目查找完全限定的域名 (FQDN) 和 IP 地址。 此外，本文还提供了有关如何配置自定义 DNS 解决方案以自动将 FQDN 解析为正确 IP 地址的体系结构建议。 本文不提供有关如何配置这些项的 DNS 记录的信息。 请参阅 DNS 文档获取如何添加记录的信息。

> [!TIP]
> 本文是介绍如何保护 Azure 机器学习工作流的系列文章的一部分。 请参阅本系列中的其他文章：
>
> * [虚拟网络概述](how-to-network-security-overview.md)
> * [保护工作区资源](how-to-secure-workspace-vnet.md)
> * [保护训练环境](how-to-secure-training-vnet.md)
> * [保护推理环境](how-to-secure-inferencing-vnet.md)
> * [启用工作室功能](how-to-enable-studio-virtual-network.md)
> * [使用防火墙](how-to-access-azureml-behind-firewall.md)
## <a name="prerequisites"></a>先决条件

- 使用[你自己的 DNS 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)的 Azure 虚拟网络。

- 具有专用终结点的 Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 可熟练使用[训练和推理过程中的网络隔离](./how-to-network-security-overview.md)。

- 熟悉 [Azure 专用终结点 DNS 区域配置](../private-link/private-endpoint-dns.md)

- 熟悉 [Azure 专用 DNS](../dns/private-dns-privatednszone.md)

- （可选）[Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="automated-dns-server-integration"></a>自动 DNS 服务器集成

### <a name="introduction"></a>简介

将自动 DNS 服务器集成与 Azure 机器学习结合使用有两种常见的体系结构：

* [托管在 Azure 虚拟网络中的自定义 DNS 服务器](#dns-vnet)。
* [在本地托管的自定义 DNS 服务器](#dns-on-premises)，通过 ExpressRoute 连接到 Azure 机器学习。

虽然你的体系结构可能不同于这些示例，但你可以将它们用作参考点。 这两个示例体系结构都提供了故障排除步骤，可帮助识别可能配置错误的组件。

另一种方法是在连接到 Azure 虚拟网络 (VNet)（其中包含你的工作区）的客户端上修改 `hosts` 文件。 有关详细信息，请参阅[主机文件](#hosts)部分。
### <a name="workspace-dns-resolution-path"></a>工作区 DNS 解析路径

通过与以下列出的完全限定的域（称为工作区 FQDN）进行通信，可以通过专用链接访问给定的 Azure 机器学习工作区：

Azure 公共区域：
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.azureml.ms```
- ```<compute instance name>.<region the workspace was created in>.instances.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.azure.net```

Azure 中国世纪互联区域：
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.cn```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.chinacloudapi.cn```

Azure 美国政府区域：
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.us```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.usgovcloudapi.net```

完全限定的域解析为以下名为工作区专用链接 FQDN 的规范名称 (CNAME)：

Azure 公共区域：
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.azure.net```

Azure 中国区域：
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.chinacloudapi.cn```

Azure 美国政府区域：
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.usgovcloudapi.net```

FQDN 解析为该区域中 Azure 机器学习工作区的 IP 地址。 但可以使用虚拟网络中托管的自定义 DNS 服务器来替代工作区专用链接 FQDN 的解析。 有关此体系结构的示例，请参阅 [vnet 中托管的自定义 DNS 服务器](#example-custom-dns-server-hosted-in-vnet)示例。

## <a name="manual-dns-server-integration"></a>手动 DNS 服务器集成

本部分讨论在 DNS 服务器中为哪些完全限定的域创建 A 记录，以及要将 A 记录的值设置为哪个 IP 地址。

### <a name="retrieve-private-endpoint-fqdns"></a>检索专用终结点 FQDN

#### <a name="azure-public-region"></a>Azure 公共区域

如果是在 Azure 公有云，则以下列表包含工作区使用的完全限定的域名 (FQDN)：

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > 此 FQDN 的工作区名称可能会被截断。 截断是为了使 `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 的字符小于或等于 63 个。
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * 只能从虚拟网络内访问计算实例。
    > * 此 FQDN 的 IP 地址不是计算实例的 IP 地址。 请改用工作区专用终结点的专用 IP 地址（`*.api.azureml.ms` 条目的 IP。）

#### <a name="azure-china-region"></a>Azure 中国区域

以下 FQDN 适用于 Azure 中国区域：

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > 此 FQDN 的工作区名称可能会被截断。 截断是为了使 `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 的字符小于或等于 63 个。
    
* `<instance-name>.<region>.instances.ml.azure.cn`

   * 此 FQDN 的 IP 地址不是计算实例的 IP 地址。 请改用工作区专用终结点的专用 IP 地址（`*.api.azureml.ms` 条目的 IP。）

#### <a name="azure-us-government"></a>Azure 美国政府

以下 FQDN 适用于 Azure 美国政府区域：

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.us`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.us`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.usgovcloudapi.net`

    > [!NOTE]
    > 此 FQDN 的工作区名称可能会被截断。 截断是为了使 `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 的字符小于或等于 63 个。
* `<instance-name>.<region>.instances.ml.azure.us`
    > * 此 FQDN 的 IP 地址不是计算实例的 IP 地址。 请改用工作区专用终结点的专用 IP 地址（`*.api.azureml.ms` 条目的 IP。）

### <a name="find-the-ip-addresses"></a>查找 IP 地址

若要在 VNet 中查找 FQDN 的内部 IP 地址，请使用以下方法之一：

> [!NOTE]
> 根据你的配置，完全限定的域名和 IP 地址会有所不同。 例如，域名中的 GUID 值将特定于你的工作区。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 若要获取专用终结点网络接口的 ID，请使用以下命令：

    ```azurecli
    az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'networkInterfaces[*].id' --output table
    ```

1. 若要获取 IP 地址和 FQDN 信息，请使用以下命令。 将 `<resource-id>` 替换为上一步中的 ID：

    ```azurecli
    az network nic show --ids <resource-id> --query 'ipConfigurations[*].{IPAddress: privateIpAddress, FQDNs: privateLinkConnectionProperties.fqdns}'
    ```

    输出将类似于以下文本：

    ```json
    [
        {
            "FQDNs": [
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms",
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms"
            ],
            "IPAddress": "10.1.0.5"
        },
        {
            "FQDNs": [
            "ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net"
            ],
            "IPAddress": "10.1.0.6"
        }
    ]
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

    > [!TIP]
    > 如果 DNS 设置未显示在页面底部，请使用页面左侧的“DNS 配置”链接查看 FQDN。

---

所有方法返回的信息是相同的；资源的 FQDN 和专用 IP 地址的列表。 以下示例来自 Azure 公有云：

| FQDN | IP 地址 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

下表显示 Azure 中国区域的示例 IP：

| FQDN | IP 地址 |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |

下表显示 Azure 美国政府区域的示例 IP：

| FQDN | IP 地址 |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.us` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.us` | `10.1.0.5` |
| `ml-mype-plt-usgovvirginia-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.usgovcloudapi.net` | `10.1.0.6` |

<a id='dns-vnet'></a>

### <a name="create-a-records-in-custom-dns-server"></a>在自定义 DNS 服务器中创建 A 记录

收集 FQDN 和相应 IP 地址的列表后，继续在已配置的 DNS 服务器中创建 A 记录。 请参阅 DNS 服务器的文档，确定如何创建 A 记录。 请注意，建议为整个 FQDN 创建唯一的区域，并在该区域的根中创建 A 记录。

## <a name="example-custom-dns-server-hosted-in-vnet"></a>示例：在 VNet 中托管的自定义 DNS 服务器

此体系结构使用常见的中心和分支虚拟网络拓扑。 一个虚拟网络包含 DNS 服务器，一个虚拟网络包含 Azure 机器学习工作区和关联资源的专用终结点。 这两个虚拟网络之间必须存在有效的路由。 例如，通过一系列对等互连虚拟网络。

:::image type="content" source="./media/how-to-custom-dns/custom-dns-topology.svg" alt-text="Azure 拓扑中托管的自定义 DNS 示意图":::

以下步骤说明了此拓扑的工作原理：

1. 创建专用 DNS 区域并链接到 DNS 服务器虚拟网络：

    确保自定义 DNS 解决方案可与 Azure 机器学习工作区一起使用的第一步是创建两个以下列域为根的专用 DNS 区域：

    Azure 公共区域：
    - ```privatelink.api.azureml.ms```
    - ```privatelink.notebooks.azure.net```
    
    Azure 中国区域：
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    Azure 美国政府区域：
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    创建专用 DNS 区域后，需要将其链接到 DNS 服务器虚拟网络。 包含 DNS 服务器的虚拟网络。

    专用 DNS 区域将替代区域根范围内的所有名称的名称解析。 此替代适用于专用 DNS 区域链接到的所有虚拟网络。 例如，如果以 `privatelink.api.azureml.ms` 为根的专用 DNS 区域链接到虚拟网络 foo，则虚拟网络 foo 中尝试解析 `bar.workspace.westus2.privatelink.api.azureml.ms` 的所有资源都将收到 `privatelink.api.azureml.ms` 区域中列出的任何记录。

    但是，专用 DNS 区域中列出的记录仅返回到使用默认 Azure DNS 虚拟服务器 IP 地址解析域的设备。 因此，自定义 DNS 服务器将解析分散在网络拓扑中的设备的域。 但是，自定义 DNS 服务器需要根据 Azure DNS 虚拟服务器 IP 地址解析与 Azure 机器学习相关的域。

2. 使用专用 DNS 集成创建专用终结点，该集成以链接到 DNS 服务器虚拟网络的专用 DNS 区域为目标：

    下一步是为 Azure 机器学习工作区创建专用终结点。 专用终结点以在步骤 1 中创建的两个专用 DNS 区域为目标。 这可确保与工作区的所有通信都是通过 Azure 机器学习虚拟网络中的专用终结点完成的。

    > [!IMPORTANT]
    > 专用终结点必须启用专用 DNS 集成，此示例才能正常运行。

3. 在 DNS 服务器中创建条件转发器以转发到 Azure DNS： 

    接下来，向 Azure DNS 虚拟服务器创建一个条件转发器。 条件转发器可确保 DNS 服务器始终查询与工作区相关的 FQDN 的 Azure DNS 虚拟服务器 IP 地址。 这意味着 DNS 服务器将从专用 DNS 区域返回相应的记录。

    下面列出了要有条件地转发的区域。 Azure DNS 虚拟服务器 IP 地址为 168.63.129.16：

    Azure 公共区域：
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    Azure 中国区域：
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    Azure 美国政府区域：
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > 此处不包含 DNS 服务器的配置步骤，因为有许多可用的 DNS 解决方案可以用作自定义 DNS 服务器。 请参阅 DNS 解决方案的文档，了解如何正确配置条件转发。

4. 解析工作区域：

    此时，所有设置都已完成。 现在，任何使用 DNS 服务器进行名称解析并具有到 Azure 机器学习专用终结点的路由的客户端都可以继续访问工作区。
    客户端将首先查询 DNS 服务器，查找以下 FQDN 的地址：

    Azure 公共区域：
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    Azure 中国区域：
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    Azure 美国政府区域：
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

5. **Azure DNS 以递归方式将工作区域解析为 CNAME**：

    DNS 服务器将从 Azure DNS 解析步骤 4 中的 FQDN。 Azure DNS 将使用步骤 1 中列出的其中一个域进行响应。

6. DNS 服务器以递归方式解析 Azure DNS 的工作区域 CNAME 记录：

    DNS 服务器将继续以递归方式解析步骤 5 中收到的 CNAME。 由于在步骤 3 中设置了条件转发器，因此 DNS 服务器将向 Azure DNS 虚拟服务器 IP 地址发送请求以进行解析。

7. Azure DNS 返回专用 DNS 区域中的记录：

    存储在专用 DNS 区域中的相应记录将返回到 DNS 服务器，这意味着 Azure DNS 虚拟服务器将返回专用终结点的 IP 地址。

8. 自定义 DNS 服务器将工作区域名解析为专用终结点地址：

    最后，自定义 DNS 服务器现在将专用终结点的 IP 地址返回到步骤 4 中的客户端。 这可确保流向 Azure 机器学习工作区的所有流量都通过专用终结点。

#### <a name="troubleshooting"></a>故障排除

如果无法从虚拟机访问工作区，或作业在虚拟网络中的计算资源上失败，请使用以下步骤确定原因：

1. 查找专用终结点上的工作区 FQDN：

    使用以下链接中的一个导航到 Azure 门户：
    - [Azure 公共区域](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure 中国区域](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure 美国政府区域](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    导航到 Azure 机器学习工作区的专用终结点。 工作区 FQDN 将在“概述”选项卡上列出。

1. 在虚拟网络拓扑中访问计算资源：

    继续访问 Azure 虚拟网络拓扑中的计算资源。 这可能需要访问与中心虚拟网络对等互连的虚拟网络中的虚拟机。 

1. 解析工作区 FQDN：

    打开命令提示符、shell 或 PowerShell。 然后，针对每个工作区 FQDN 运行以下命令：

    `nslookup <workspace FQDN>`
        
    每个 nslookup 的结果应该将专用终结点上的两个专用 IP 地址之一返回到 Azure 机器学习工作区。 如果没有返回，则说明自定义 DNS 解决方案中存在配置错误。

    可能的原因：
    - 运行故障排除命令的计算资源没有使用 DNS 服务器进行 DNS 解析
    - 创建专用终结点时选择的专用 DNS 区域未链接到 DNS 服务器 VNet
    - 未正确配置到 Azure DNS 虚拟服务器 IP 的条件转发器

<a id='dns-on-premises'></a>

## <a name="example-custom-dns-server-hosted-on-premises"></a>示例：在本地托管的自定义 DNS 服务器

此体系结构使用常见的中心和分支虚拟网络拓扑。 ExpressRoute 用于从本地网络连接到中心虚拟网络。 自定义 DNS 服务器托管在本地。 一个单独的虚拟网络包含 Azure 机器学习工作区和关联资源的专用终结点。 使用此拓扑，需要有另一个托管 DNS 服务器的虚拟网络，该服务器可以将请求发送到 Azure DNS 虚拟服务器 IP 地址。

:::image type="content" source="./media/how-to-custom-dns/custom-dns-express-route.svg" alt-text="在本地拓扑托管的自定义 DNS 示意图":::

以下步骤说明了此拓扑的工作原理：

1. 创建专用 DNS 区域并链接到 DNS 服务器虚拟网络：

    确保自定义 DNS 解决方案可与 Azure 机器学习工作区一起使用的第一步是创建两个以下列域为根的专用 DNS 区域：
    
    Azure 公共区域：
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    Azure 中国区域：
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    Azure 美国政府区域：
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    创建专用 DNS 区域后，需要将该区域链接到 DNS 服务器 VNet - 包含 DNS 服务器的虚拟网络。 

    > [!NOTE]
    > 虚拟网络中的 DNS 服务器独立于本地 DNS 服务器。

    专用 DNS 区域将替代区域根范围内的所有名称的名称解析。 此替代适用于专用 DNS 区域链接到的所有虚拟网络。 例如，如果以 `privatelink.api.azureml.ms` 为根的专用 DNS 区域链接到虚拟网络 foo，则虚拟网络 foo 中尝试解析 `bar.workspace.westus2.privatelink.api.azureml.ms` 的所有资源都将收到 privatelink.api.azureml.ms 区域中列出的任何记录。

    但是，专用 DNS 区域中列出的记录仅返回到使用默认 Azure DNS 虚拟服务器 IP 地址解析域的设备。 Azure DNS 虚拟服务器 IP 地址仅在虚拟网络的上下文中有效。 使用本地 DNS 服务器时，它无法查询 Azure DNS 虚拟服务器 IP 地址来检索记录。

    为避免此行为，请在虚拟网络中创建一个中间 DNS 服务器。 此 DNS 服务器可以查询 Azure DNS 虚拟服务器 IP 地址，以检索链接到虚拟网络的任何专用 DNS 区域的记录。

    虽然本地 DNS 服务器将解析分散在网络拓扑中的设备的域，但它会针对 DNS 服务器解析与 Azure 机器学习相关的域。 DNS 服务器将从 Azure DNS 虚拟服务器 IP 地址解析这些域。

2. 使用专用 DNS 集成创建专用终结点，该集成以链接到 DNS 服务器虚拟网络的专用 DNS 区域为目标：

    下一步是为 Azure 机器学习工作区创建专用终结点。 专用终结点以在步骤 1 中创建的两个专用 DNS 区域为目标。 这可确保与工作区的所有通信都是通过 Azure 机器学习虚拟网络中的专用终结点完成的。

    > [!IMPORTANT]
    > 专用终结点必须启用专用 DNS 集成，此示例才能正常运行。

3. 在 DNS 服务器中创建条件转发器以转发到 Azure DNS：

    接下来，向 Azure DNS 虚拟服务器创建一个条件转发器。 条件转发器可确保 DNS 服务器始终查询与工作区相关的 FQDN 的 Azure DNS 虚拟服务器 IP 地址。 这意味着 DNS 服务器将从专用 DNS 区域返回相应的记录。

    下面列出了要有条件地转发的区域。 Azure DNS 虚拟服务器 IP 地址为 168.63.129.16。

    Azure 公共区域：
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```     
    
    Azure 中国区域：
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```

    Azure 美国政府区域：
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > 此处不包含 DNS 服务器的配置步骤，因为有许多可用的 DNS 解决方案可以用作自定义 DNS 服务器。 请参阅 DNS 解决方案的文档，了解如何正确配置条件转发。

4. 在本地 DNS 服务器中创建条件转发器以转发到 DNS 服务器：

    接下来，在 DNS 服务器虚拟网络中向 DNS 服务器创建一个条件转发器。 此转发器适用于步骤 1 中列出的区域。 这类似于步骤 3，但本地 DNS 服务器将以 DNS 服务器的 IP 地址为目标，而不是转发到 Azure DNS 虚拟服务器 IP 地址。 由于本地 DNS 服务器不在 Azure 中，因此无法直接解析专用 DNS 区域中的记录。 在这种情况下，DNS 服务器代理将请求从本地 DNS 服务器转发到 Azure DNS 虚拟服务器 IP。 这允许本地 DNS 服务器检索链接到 DNS 服务器虚拟网络的专用 DNS 区域中的记录。 

    下面列出了要有条件地转发的区域。 要转发到的 IP 地址是 DNS 服务器的 IP 地址：

    Azure 公共区域：
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    Azure 中国区域：
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    Azure 美国政府区域：
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > 此处不包含 DNS 服务器的配置步骤，因为有许多可用的 DNS 解决方案可以用作自定义 DNS 服务器。 请参阅 DNS 解决方案的文档，了解如何正确配置条件转发。

5. 解析工作区域：

    此时，所有设置都已完成。 任何使用本地 DNS 服务器进行名称解析并具有到 Azure 机器学习专用终结点的路由的客户端都可以继续访问工作区。

    客户端将首先查询本地 DNS 服务器，查找以下 FQDN 的地址：

    Azure 公共区域：
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    Azure 中国区域：
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    Azure 美国政府区域：
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

6. **本地 DNS 服务器以递归形式解析工作区域**：

    本地 DNS 服务器将从 DNS 服务器解析步骤 5 中的 FQDN。 由于步骤 4 中设置了条件转发器，因此本地 DNS 服务器将向 DNS 服务器发送请求以进行解析。

7. **DNS 服务器从 Azure DNS 将工作区域解析为 CNAME**：

    DNS 服务器将从 Azure DNS 解析步骤 5 中的 FQDN。 Azure DNS 将使用步骤 1 中列出的其中一个域进行响应。

8. 本地 DNS 服务器以递归方式解析 DNS 服务器的工作区域 CNAME 记录：

    本地 DNS 服务器将继续以递归方式解析步骤 7 中收到的 CNAME。 由于在步骤 4 中设置了条件转发器，因此本地 DNS 服务器将向 DNS 服务器发送请求以进行解析。

9. DNS 服务器以递归方式解析 Azure DNS 的工作区域 CNAME 记录：

    DNS 服务器将继续以递归方式解析步骤 7 中收到的 CNAME。 由于在步骤 3 中设置了条件转发器，因此 DNS 服务器将向 Azure DNS 虚拟服务器 IP 地址发送请求以进行解析。

10. Azure DNS 返回专用 DNS 区域中的记录：

    存储在专用 DNS 区域中的相应记录将返回到 DNS 服务器，这意味着 Azure DNS 虚拟服务器将返回专用终结点的 IP 地址。

11. 本地 DNS 服务器将工作区域名解析为专用终结点地址：

    在步骤 8 中，从本地 DNS 服务器到 DNS 服务器的查询最终将与专用终结点关联的 IP 地址返回到 Azure 机器学习工作区。 这些 IP 地址将返回到原始客户端，该客户端现在将通过在步骤 1 中配置的专用终结点与 Azure 机器学习工作区通信。

<a id="hosts"></a>
## <a name="example-hosts-file"></a>示例：Hosts 文件

`hosts` 文件是一个文本文档，Linux、macOS 和 Windows 都使用它来替代本地计算机的名称解析。 该文件包含 IP 地址和相应主机名的列表。 当本地计算机尝试解析某个主机名时，如果该主机名已在 `hosts` 文件中列出，则它会解析为相应的 IP 地址。

> [!IMPORTANT]
> `hosts` 文件只会替代本地计算机的名称解析。 如果要在多台计算机中使用某个 `hosts` 文件，必须在每台计算机上单独修改该文件。

下表列出了 `hosts` 文件的位置：

| 操作系统 | 位置 |
| ----- | ----- |
| Linux | `/etc/hosts` |
| macOS | `/etc/hosts` |
| Windows | `%SystemRoot%\System32\drivers\etc\hosts` |

> [!TIP]
> 文件名是不带扩展名的 `hosts`。 编辑该文件时，请使用管理员访问权限。 例如，在 Linux 或 macOS 上，可以使用 `sudo vi`。 在 Windows 上，以管理员身份运行记事本。

下面是 Azure 机器学习的 `hosts` 文件条目示例：

```
# For core Azure Machine Learning hosts
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms
10.1.0.6    ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net

# For a compute instance named 'mycomputeinstance'
10.1.0.5    mycomputeinstance.eastus.instances.azureml.ms
```

有关 `hosts` 文件的详细信息，请参阅 [https://wikipedia.org/wiki/Hosts_(file)](https://wikipedia.org/wiki/Hosts_(file))。

#### <a name="troubleshooting"></a>故障排除

如果在运行上述步骤后，无法从虚拟机访问工作区，或者作业在包含 Azure 机器学习工作区专用终结点的虚拟网络中的计算资源上失败，请按照以下步骤尝试确定原因。

1. 查找专用终结点上的工作区 FQDN：

    使用以下链接中的一个导航到 Azure 门户：
    - [Azure 公共区域](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure 中国区域](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure 美国政府区域](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    导航到 Azure 机器学习工作区的专用终结点。 工作区 FQDN 将在“概述”选项卡上列出。

1. 在虚拟网络拓扑中访问计算资源：

    继续访问 Azure 虚拟网络拓扑中的计算资源。 这可能需要访问与中心虚拟网络对等互连的虚拟网络中的虚拟机。 

1. 解析工作区 FQDN：

    打开命令提示符、shell 或 PowerShell。 然后，针对每个工作区 FQDN 运行以下命令：

    `nslookup <workspace FQDN>`
        
    每个 nslookup 的结果应该将专用终结点上的两个专用 IP 地址之一提供给 Azure 机器学习工作区。 如果没有返回，则说明自定义 DNS 解决方案中存在配置错误。

    可能的原因：
    - 运行故障排除命令的计算资源没有使用 DNS 服务器进行 DNS 解析
    - 创建专用终结点时选择的专用 DNS 区域未链接到 DNS 服务器 VNet
    - 未正确配置从 DNS 服务器到 Azure DNS 虚拟服务器 IP 的条件转发器
    - 未正确配置从本地 DNS 服务器到 DNS 服务器的条件转发器

## <a name="next-steps"></a>后续步骤

本文是介绍如何保护 Azure 机器学习工作流的系列文章的一部分。 请参阅本系列中的其他文章：

* [虚拟网络概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [启用工作室功能](how-to-enable-studio-virtual-network.md)
* [使用防火墙](how-to-access-azureml-behind-firewall.md)

有关将专用终结点集成到 DNS 配置的信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。

有关使用自定义 DNS 名称或 TLS 安全性部署模型的信息，请参阅[使用 TLS 保护 Web 服务](how-to-secure-web-service.md)。
