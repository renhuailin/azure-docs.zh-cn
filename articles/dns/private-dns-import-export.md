---
title: 导入和导出 Azure 专用 DNS 的域区域文件 - Azure CLI
titleSuffix: Azure DNS
description: 了解如何通过使用 Azure CLI 导入和导出 Azure 专用 DNS 的 DNS 区域文件
services: dns
author: duongau
ms.service: dns
ms.date: 03/16/2021
ms.author: duau
ms.topic: how-to
ms.openlocfilehash: 2e5babb85dbf4aa7da707e22be5eeaf3ae89972d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450660"
---
# <a name="import-and-export-a-private-dns-zone-file-for-azure-private-dns"></a>导入和导出 Azure 专用 DNS 的专用 DNS 区域文件

本文介绍如何使用 Azure CLI 导入和导出 Azure DNS 的 DNS 区域文件。

## <a name="introduction-to-dns-zone-migration"></a>DNS 区域迁移简介

DNS 区域文件是一个文本文件，其中包含区域中每个域名系统 (DNS) 记录的详细信息。 它会遵循标准格式，使其适合在 DNS 系统之间传输 DNS 记录。 使用区域文件是从 Azure DNS 传入或传出 DNS 区域的快速、可靠和方便的方法。

Azure 专用 DNS 支持使用 Azure 命令行接口 (CLI) 导入和导出区域文件。 当前 **不** 支持通过 Azure PowerShell 或 Azure 门户导入区域文件。

Azure CLI 是用于管理 Azure 服务的跨平台命令行工具。 它适用于 Windows、Mac 和 Linux 平台，可以从 [Azure 下载页](https://azure.microsoft.com/downloads/)获取。 跨平台支持对导入和导出区域文件很重要，因为最常见的名称服务器软件 [BIND](https://www.isc.org/downloads/bind/) 通常在 Linux 上运行。

## <a name="obtain-your-existing-dns-zone-file"></a>获取现有的 DNS 区域文件

将 DNS 区域文件导入 Azure DNS 之前，需要获取区域文件的副本。 此文件的来源取决于当前托管 DNS 区域的位置。

* 如果 DNS 区域由合作伙伴服务（如域注册机构、专用的 DNS 托管提供商或备用云提供商）托管，则该服务应提供下载 DNS 区域文件的功能。
* 如果 DNS 区域是在 Windows DNS 上托管的，则区域文件的默认文件夹是 **%systemroot%\system32\dns**。 每个区域文件的完整路径还会显示在 DNS 控制台的“常规”  选项卡上。
* 如果 DNS 区域是通过使用 BIND 托管的，则在 BIND 配置文件 **named.conf** 中会指定每个区域的区域文件位置。

## <a name="import-a-dns-zone-file-into-azure-private-dns"></a>将 DNS 区域文件导入 Azure 专用 DNS

导入区域文件时会在 Azure 专用 DNS 中新建一个区域（如果尚不存在区域）。 如果区域已存在，则区域文件中的记录集必须与现有的记录集合并。

### <a name="merge-behavior"></a>合并行为

* 默认情况下，将合并现有的和新的记录集。 合并的记录集内的相同记录会执行重复数据删除。
* 合并记录集时，会使用以前存在的记录集的生存时间 (TTL)。
* 起始授权机构 (SOA) 参数（除了 `host`）始终取自导入的区域文件。 同样，对于区域顶点处的名称服务器记录集，TTL 始终取自导入的区域文件。
* 导入的 CNAME 记录不会替换现有的同名 CNAME 记录。  
* CNAME 记录与另一同名但类型不同的记录（无论是现有还是新建记录）发生冲突时，都会保留现有的记录。 

### <a name="additional-information-about-importing"></a>有关导入的其他信息

下面几点提供有关区域导入过程的其他技术详细信息。

* `$TTL` 指令是可选的并受支持。 如未提供 `$TTL` 指令，会导入没有显式 TTL 的记录，且其默认 TTL 设置为 3600 秒。 当同一个记录集的两个记录指定不同的 TTL 时，会使用较低的值。
* `$ORIGIN` 指令是可选的并受支持。 如果未设置 `$ORIGIN`，则使用的默认值是在命令行上指定的区域名称（加上结尾 "."）。
* `$INCLUDE` 和 `$GENERATE` 指令不受支持。
* 支持以下记录类型：A、AAAA、CAA、CNAME、MX、NS、SOA、SRV 和 TXT。
* Azure DNS 会在创建区域时，自动创建 SOA 记录。 导入区域文件时，*除了*`host` 参数，所有的 SOA 参数都取自区域文件。 此参数会使用 Azure DNS 提供的值。 这是因为此参数必须引用 Azure DNS 提供的主名称服务器。
* Azure DNS 在创建区域时，也会在区域顶点处自动创建名称服务器记录集。 仅导入此记录集的 TTL。 这些记录包含由 Azure DNS 提供的名称服务器名称。 导入的区域文件中包含的值不会覆盖记录数据。
* 在公开预览版期间，Azure DNS 仅支持单字符串的 TXT 记录。 多字符串 TXT 记录会被连接在一起并截断为 255 个字符。

### <a name="cli-format-and-values"></a>CLI 格式和值

用于导入 DNS 区域的 Azure CLI 命令的格式为：

```azurecli
az network private-dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

值：

* `<resource group>` 是 Azure DNS 中区域的资源组名称。
* `<zone name>` 是区域的名称。
* `<zone file name>` 是要导入的区域文件的路径/名称。

如果资源组中不存在具有此名称的区域，会为你创建一个。 如果区域已存在，则导入的记录集会与现有的记录集合并。

### <a name="import-a-zone-file"></a>导入区域文件

导入区域 **contoso.com** 的区域文件。

1. 如果还没有 Resource Manager 资源组，则需要创建一个。

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. 要将文件 **contoso.com.txt** 中的区域 **contoso.com** 导入到资源组 **myresourcegroup** 中的新 DNS 区域，请运行命令 `az network private-dns zone import`。<BR>此命令将加载并分析区域文件。 此命令在 Azure DNS 服务上执行一系列命令，以便创建区域和区域中的所有记录集。 此命令在控制台窗口中报告进度，以及任何错误或警告。 由于记录集是以序列方式创建的，导入大型区域文件可能需要几分钟。

    ```azurecli
    az network private-dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>验证该区域

若要在导入文件后验证 DNS 区域，可以使用以下方法之一：

* 可以通过使用以下 Azure CLI 命令列出记录：

    ```azurecli
    az network private-dns record-set list -g myresourcegroup -z contoso.com
    ```

* 可以使用 `nslookup` 验证记录的名称解析。 由于尚未委派区域，因此需要显式指定正确的 Azure DNS 名称服务器。 下面的示例演示如何检索已分配给该区域的名称服务器的名称。 另外，还会演示如何使用 `nslookup` 查询“www”记录。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>从 Azure DNS 导出 DNS 区域文件

用于导出 DNS 区域的 Azure CLI 命令的格式为：

```azurecli
az network private-dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

值：

* `<resource group>` 是 Azure DNS 中区域的资源组名称。
* `<zone name>` 是区域的名称。
* `<zone file name>` 是要导出的区域文件的路径/名称。

和区域导入一样，必须先登录，选择订阅，然后配置 Azure CLI 以使用 Resource Manager 模式。

### <a name="to-export-a-zone-file"></a>导出区域文件

要将资源组 **myresourcegroup** 中现有的 Azure DNS 区域 **contoso.com** 导出至文件 **contoso.com.txt**（在当前文件夹中），请运行 `azure network private-dns zone export`。 此命令调用 Azure DNS 服务，以枚举该区域中的记录集，并将结果导出到兼容 BIND 的区域文件。

```azurecli
az network private-dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>后续步骤

* 了解如何在 DNS 区域中[管理记录集和记录](./private-dns-getstarted-cli.md)。
