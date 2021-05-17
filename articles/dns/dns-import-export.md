---
title: 导入和导出域区域文件 - Azure CLI
titleSuffix: Azure DNS
description: 了解如何使用 Azure CLI 将 DNS（域名系统）区域文件导入和导出 Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/29/2021
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: 949e497057646507fcaa04907d032beb5b8ef47c
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316886"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>使用 Azure CLI 导入和导出 DNS 区域文件

本文介绍如何使用 Azure CLI 在 Azure DNS 中导入和导出 DNS 区域文件。

## <a name="introduction-to-dns-zone-migration"></a>DNS 区域迁移简介

DNS 区域文件是一种文本文件，其中包含区域中每个域名系统 (DNS) 记录的信息。 它会遵循标准格式，使其适合在 DNS 系统之间传输 DNS 记录。 使用区域文件是将 DNS 区域导入 Azure DNS 的一种快捷简便的方法。 还可将区域文件从 Azure DNS 导出以与其他 DNS 系统一起使用。

Azure DNS 支持通过使用 Azure 命令行接口 (CLI) 导入和导出区域文件。 当前不支持使用 Azure PowerShell 或 Azure 门户导入区域文件。

Azure CLI 是用于管理 Azure 服务的跨平台命令行工具。 它适用于 Windows、Mac 和 Linux，可以从 [Azure 下载页面](https://azure.microsoft.com/downloads/)获取。

## <a name="obtain-your-existing-dns-zone-file"></a>获取现有的 DNS 区域文件

将 DNS 区域文件导入 Azure DNS 之前，需要获取区域文件的副本。 此文件的来源取决于当前托管 DNS 区域的位置。

* 如果你的 DNS 区域当前由合作伙伴服务托管，则他们将为你提供下载 DNS 区域文件的方法。 合作伙伴服务包括域注册机构、专用 DNS 托管提供商或备用云提供商。
* 如果 DNS 区域是在 Windows DNS 上托管的，则区域文件的默认文件夹是 **%systemroot%\system32\dns**。 每个区域文件的完整路径还会显示在 DNS 控制台的“常规”选项卡上。
* 如果 DNS 区域是通过使用 BIND 托管的，则在 BIND 配置文件 named.conf 中会指定每个区域的区域文件位置。

## <a name="import-a-dns-zone-file-into-azure-dns"></a>将 DNS 区域文件导入 Azure DNS

导入区域文件时会在 Azure DNS 中新建一个区域（如果该区域尚不存在）。 如果区域已存在，则区域文件中的记录集将与现有的记录集合并。

### <a name="merge-behavior"></a>合并行为

* 默认情况下，新的记录集将与现有记录集合并。 合并的记录集内的相同记录不会重复。
* 合并记录集时，会使用以前存在的记录集的生存时间 (TTL)。
* 起始授权机构 (SOA) 参数（除 `host` 除外）始终取自导入的区域文件。 区域顶点处的名称服务器记录集也将始终使用从导入的区域文件中获取的 TTL。
* 导入的 CNAME 记录不会替换现有的同名 CNAME 记录。  
* CNAME 记录与另一同名但不同类型的记录发生冲突时，将使用现有记录。

### <a name="additional-information-about-importing"></a>有关导入的其他信息

下面几点提供有关区域导入过程的其他技术详细信息。

* `$TTL` 指令是可选的并受支持。 如未提供 `$TTL` 指令，会导入没有显式 TTL 的记录，且其默认 TTL 设置为 3600 秒。 当同一个记录集的两个记录指定不同的 TTL 时，会使用较低的值。
* `$ORIGIN` 指令是可选的并受支持。 如果未设置 `$ORIGIN`，则使用的默认值是命令行中指定的区域名称，包括结尾“.”。
* `$INCLUDE` 和 `$GENERATE` 指令不受支持。
* 支持以下记录类型：A、AAAA、CAA、CNAME、MX、NS、SOA、SRV 和 TXT。
* Azure DNS 会在创建区域时，自动创建 SOA 记录。 导入区域文件时，*除了*`host` 参数，所有的 SOA 参数都取自区域文件。 此参数使用 Azure DNS 提供的值，因为它需要引用 Azure DNS 提供的主名称服务器。
* Azure DNS 在创建区域时，也会在区域顶点处自动创建名称服务器记录集。 仅导入此记录集的 TTL。 这些记录包含由 Azure DNS 提供的名称服务器名称。 导入的区域文件中包含的值不会覆盖记录数据。
* 在公开预览版期间，Azure DNS 仅支持单字符串的 TXT 记录。 多字符串 TXT 记录会被连接在一起并截断为 255 个字符。

### <a name="cli-format-and-values"></a>CLI 格式和值

用于导入 DNS 区域的 Azure CLI 命令的格式为：

```azurecli-interactive-interactive
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

值：

* `<resource group>` 是 Azure DNS 中区域的资源组名称。
* `<zone name>` 是区域的名称。
* `<zone file name>` 是要导入的区域文件的路径/名称。

如果资源组中尚不存在具有此名称的区域，则将创建一个。 对于现有区域，则导入的记录集将与现有的记录集合并。 

### <a name="import-a-zone-file"></a>导入区域文件

导入区域 **contoso.com** 的区域文件。

1. 如果没有资源组，请创建一个资源组。

    ```azurecli-interactive
    az group create --resource-group myresourcegroup -l westeurope
    ```

1. 要将文件 contoso.com.txt 中的区域 contoso.com 导入到资源组 myresourcegroup 中的新 DNS 区域，请运行命令 `az network dns zone import`  。

    此命令将加载并分析区域文件。 此命令在 Azure DNS 服务上执行一系列操作，以便创建区域和区域中的所有记录集。 此命令会在控制台窗口中报告进度，以及任何错误或警告。 由于记录集是以序列方式创建的，因此导入大型区域文件可能需要几分钟。

    ```azurecli-interactive
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>验证该区域

导入文件后，可以使用下列方法之一来验证 DNS 区域：

* 若要列出记录，请使用以下 Azure CLI 命令：

    ```azurecli-interactive
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* 还可通过使用 Azure CLI 命令 `az network dns record-set ns list` 列出记录。
* 使用 `nslookup` 验证记录的名称解析。 如果尚未委派区域，则需要显式指定正确的 Azure DNS 名称服务器。 下面的示例演示如何检索已分配给该区域的名称服务器的名称。

    ```azurecli-interactive
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    使用 Windows 命令提示符，通过 `nslookup` 命令查询“www”记录。

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="update-dns-delegation"></a>更新 DNS 委派

验证该区域已正确导入后，需要更新 DNS 委派以指向 Azure DNS 名称服务器。 有关详细信息，请参阅[更新 DNS 委派](dns-domain-delegation.md)。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>从 Azure DNS 导出 DNS 区域文件

若要导出 DNS 区域，请使用以下 Azure CLI 命令：

```azurecli-interactive
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

值：

* `<resource group>` 是 Azure DNS 中区域的资源组名称。
* `<zone name>` 是区域的名称。
* `<zone file name>` 是要导出的区域文件的路径/名称。

和区域导入一样，必须先登录，选择订阅，然后配置 Azure CLI 以使用 Resource Manager 模式。

### <a name="to-export-a-zone-file"></a>导出区域文件

要将资源组 **myresourcegroup** 中现有的 Azure DNS 区域 **contoso.com** 导出至文件 **contoso.com.txt**（在当前文件夹中），请运行 `azure network dns zone export`。 此命令调用 Azure DNS 服务，以枚举该区域中的记录集，并将结果导出到兼容 BIND 的区域文件。

```azurecli-interactive
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>后续步骤

* 了解如何在 DNS 区域中[管理记录集和记录](./dns-getstarted-cli.md)。

* 了解如何[将域委派给 Azure DNS](dns-domain-delegation.md)。
