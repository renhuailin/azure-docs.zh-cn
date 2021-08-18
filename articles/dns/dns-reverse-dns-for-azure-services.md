---
title: Azure 服务的反向 DNS | Azure DNS
description: 利用此学习路径，开始为 Azure 中托管的服务配置反向 DNS 查找。
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/29/2021
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6010d165b1a66f968c66f52cf3bfac754df47111
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460027"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>为 Azure 中托管的服务配置反向 DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本文介绍如何为 Azure 中托管的服务配置反向 DNS 查找。

Azure 中的服务使用 Azure 分配的、Microsoft 所有的 IP 地址。 必须在相应的 Microsoft 拥有的反向 DNS 查找区域中创建这些反向 DNS 记录（PTR 记录）。

这种情况不同于在 Azure DNS 中[托管分配的 IP 范围的反向 DNS 查找区域](dns-reverse-dns-hosting.md)的能力。 在这种情况下，必须将由反向查找区域表示的 IP 范围分配给组织（通常通过 ISP 执行）。

阅读本文之前，应先熟悉 [Azure DNS 中的反向 DNS](dns-reverse-dns-overview.md)。

在 Azure DNS 中，计算资源（例如虚拟机、虚拟机规模集和 Service Fabric 群集）具有公共 IP 地址。 反向 DNS 查找是使用公共 IP 地址的“ReverseFqdn”属性配置的。

Azure 应用服务和应用程序网关目前不支持反向 DNS。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 记录的验证

第三方不应有权为映射到 DNS 域的 Azure 服务创建反向 DNS 记录。 这就是为什么仅当域名相同或解析为同一订阅中的公共 IP 地址时，Azure 才允许你创建反向 DNS 记录。 此限制也适用于云服务。

仅在设置或修改反向 DNS 记录时执行此验证。 定期重新验证未完成。

例如，假设公共 IP 地址资源具有 DNS 名称 `contosoapp1.northus.cloudapp.azure.com` 和 IP 地址 `23.96.52.53`。 可将此公共 IP 地址的反向 FQDN 指定为以下内容：

* 公共 IP 地址的 DNS 名称：`contosoapp1.northus.cloudapp.azure.com`。
* 同一订阅中另一个 PublicIpAddress 的 DNS 名称，例如 `contosoapp2.westus.cloudapp.azure.com`。
* 虚 DNS 名称，例如 `app1.contoso.com`。 只要首先将名称配置为指向 `contosoapp1.northus.cloudapp.azure.com` 的 CNAME。 该名称还可以指向同一订阅中的其他公共 IP 地址。
* 虚 DNS 名称，例如 `app1.contoso.com`。 只要首先将此名称配置为指向 IP 地址 23.96.52.53 的 A 记录。 该名称还可以指向同一订阅中的另一个 IP 地址。

相同的约束也适用于云服务的反向 DNS。

## <a name="reverse-dns-for-public-ip-address-resources"></a>公共 IP 地址资源的反向 DNS

本部分提供有关如何在资源管理器部署模型中为公用 IP 地址资源配置反向 DNS 的详细说明。 可以使用 Azure PowerShell、Azure 经典 CLI 或 Azure CLI 来完成此任务。 Azure 门户当前不支持为公共 IP 地址资源配置反向 DNS。

Azure 当前仅支持为公共 IPv4 地址资源配置反向 DNS。

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>将反向 DNS 添加到现有 PublicIpAddresses

#### <a name="azure-powershell"></a>Azure PowerShell

若要将反向 DNS 更新为现有 PublicIpAddress，请执行以下命令：

```azurepowershell-interactive
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

若要将反向 DNS 添加到尚不具有 DNS 名称的现有 PublicIpAddress，则还必须指定 DNS 名称：

```azurepowershell-interactive
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

若要将反向 DNS 添加到现有 PublicIpAddress，请执行以下操作：

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

若要将反向 DNS 添加到尚不具有 DNS 名称的现有 PublicIpAddress，则还必须指定 DNS 名称：

```azurecli-interactive
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

若要将反向 DNS 添加到现有 PublicIpAddress，请执行以下操作：

```azurecli-interacgive
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

若要将反向 DNS 添加到尚不具有 DNS 名称的现有 PublicIpAddress，则还必须指定 DNS 名称：

```azurecli-interactive
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>创建具有反向 DNS 的公共 IP 地址

若要创建具有已指定的反向 DNS 属性的新 PublicIpAddress，请执行以下操作：

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>查看现有 PublicIpAddress 的反向 DNS

若要查看现有 PublicIpAddress 的配置值，请执行以下操作：

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>从现有公共 IP 地址中删除反向 DNS

若要从现有 PublicIpAddress 中删除反向 DNS 属性，请执行以下操作：

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```

## <a name="configure-reverse-dns-for-cloud-services"></a>配置云服务的反向 DNS

本部分提供有关如何使用 Azure PowerShell 在经典部署模型中配置云服务的反向 DNS 的详细说明。 不支持通过 Azure 门户、Azure 经典 CLI 或 Azure CLI 为云服务配置反向 DNS。

### <a name="add-reverse-dns-to-existing-cloud-services"></a>将反向 DNS 添加到现有云服务

若要将反向 DNS 记录添加到现有云服务，请执行以下操作：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>使用反向 DNS 创建云服务

若要创建具有已指定的反向 DNS 属性的新云服务，请执行以下操作：

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>查看现有云服务的反向 DNS

若要查看现有云服务的反向 DNS 属性，请执行以下操作：

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>从现有云服务中删除反向 DNS

若要从现有云服务中删除反向 DNS 属性，请执行以下操作：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>常见问题解答

### <a name="how-much-do-reverse-dns-records-cost"></a>反向 DNS 记录的费用如何？

完全免费！ 反向 DNS 记录或查询不会产生额外的费用。

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>反向 DNS 记录是否从 Internet 解析？

是的。 为 Azure 服务设置反向 DNS 属性后，Azure 将管理所需的所有 DNS 委托和 DNS 区域，以确保可为所有 Internet 用户进行解析。

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>是否为 Azure 服务创建默认的反向 DNS 记录？

不是。 反向 DNS 是可选功能。 如果选择不配置反向 DNS，则不会创建任何默认的反向 DNS 记录。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完全限定的域名 (FQDN) 的格式是什么？

FQDN 以正向顺序指定，且必须以点号结尾（例如“app1.contoso.com.”）。

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>如果指定的反向 DNS 验证检查失败，会发生什么情况？

如果反向 DNS 验证检查失败，则配置反向 DNS 记录的操作也会失败。 请根据需要更正反向 DNS 值，然后重试。

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>是否可以为 Azure 应用服务配置反向 DNS？

不是。 Azure 应用服务不支持反向 DNS。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>是否可以为 Azure 服务配置多个反向 DNS 记录？

不是。 Azure 仅支持为每个 Azure 云服务或 PublicIpAddress 配置一个反向 DNS 记录。

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>是否可以为 IPv6 PublicIpAddress 资源配置反向 DNS？

不是。 Azure 当前仅支持为 IPv4 PublicIpAddress 资源和云服务配置反向 DNS。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>是否可以从 Azure 计算服务将电子邮件发送到外部域？

直接从 Azure 部署发送电子邮件的技术能力取决于订阅类型。 无论是哪种订阅类型，Microsoft 都建议使用受信任的邮件中继服务来发送邮件。 有关详细信息，请参阅[增强在 Azure 中发送电子邮件的安全性 - 2017 年 11 月更新](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)。

## <a name="next-steps"></a>后续步骤

* 有关反向 DNS 的详细信息，请参阅[反向 DNS 查找](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)。
* 了解如何[在 Azure DNS 中为 ISP 分配的 IP 范围托管反向查找区域](dns-reverse-dns-hosting.md)。
