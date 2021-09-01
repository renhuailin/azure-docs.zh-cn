---
title: 诊断 Azure Key Vault 上的专用链接配置问题
description: 解决 Key Vault 的常见专用链接问题并深入探讨配置
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 85760971b8f4eb8bfbb431193636532a498d7236
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665594"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>诊断 Azure Key Vault 上的专用链接配置问题

## <a name="introduction"></a>简介

本文可帮助用户诊断并修复涉及 Key Vault 和专用链接功能的问题。 本指南提供配置方面的帮助，例如如何让专用链接首次生效，或者如何修复专用链接因某些更改而停止生效的情况。

如果不熟悉此功能，请参阅[将 Key Vault 与 Azure 专用链接集成](private-link-service.md)。

### <a name="problems-covered-by-this-article"></a>本文涵盖的问题

- DNS 查询仍返回密钥保管库的公共 IP 地址，而不是返回你由于使用专用链接功能而预期会收到的专用 IP 地址。
- 给定客户端发出的使用专用链接的所有请求都失败，出现超时或网络错误，且问题不是间歇性的。
- 密钥保管库有一个专用 IP 地址，但请求仍获得内部错误代码为 `ForbiddenByFirewall` 的 `403` 响应。
- 你使用的是专用链接，但密钥保管库仍接受来自公共 Internet 的请求。
- 密钥保管库有两个专用终结点。 使用一个专用终结点的请求正常，但使用另一个专用终结点的请求失败。
- 你有另一个使用专用链接的订阅、密钥保管库或虚拟网络。 你想要进行新的类似部署，但无法让专用链接在其中生效。

### <a name="problems-not-covered-by-this-article"></a>本文未涵盖的问题

- 存在间歇性连接问题。 在给定的客户端中，你会看到一些请求有效，一些请求无效。 间歇性问题通常不是由专用链接配置中的问题引起的；它们是网络或客户端过载的迹象。
- 你使用的是支持 BYOK（创建自己的密钥）、CMK（客户管理的密钥）的 Azure 产品，或者是支持对密钥保管库中存储的机密进行访问的 Azure 产品。 当你在密钥保管库设置中启用防火墙时，该产品无法访问密钥保管库。 请查看特定于产品的文档，确保它已明确指出对启用了防火墙的密钥保管库的支持，并根据需要与该特定产品的支持人员联系。

### <a name="how-to-read-this-article"></a>如何阅读本文

如果不熟悉专用链接或要评估复杂的部署，建议阅读整篇文章。 否则，你可以随意选择与你所面临的问题更相关的部分进行阅读。

让我们开始吧！

## <a name="1-confirm-that-you-own-the-client-connection"></a>1.确认你拥有客户端连接

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>确认客户端在虚拟网络上运行

本指南旨在帮助你修复从应用程序代码发起的到密钥保管库的连接。 示例包括在 Azure 虚拟机、Azure Service Fabric 群集、Azure 应用服务、Azure Kubernetes 服务 (AKS) 等位置中执行的应用程序和脚本。 本指南还适用于在 Azure 门户基于 Web 的用户界面中执行的访问，浏览器在该界面中直接访问密钥保管库。

按照专用链接的定义，应用程序、脚本或门户必须运行在满足特定条件的计算机、群集或环境中，即连接到部署了[专用终结点资源](../../private-link/private-endpoint-overview.md)的虚拟网络。

如果应用程序、脚本或门户在连接 Internet 的任意网络上运行，则本指南不适用，并且专用链接可能无法使用。 此限制也适用于在 Azure Cloud Shell 中执行的命令，因为它们在按需提供的远程 Azure 计算机而不是用户浏览器中运行。

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>如果使用托管解决方案，请参阅特定文档

本指南不适用于 Microsoft 管理的解决方案，其中的密钥保管库由独立于客户虚拟网络而存在的 Azure 产品访问。 此类方案的示例包括：针对静态加密配置的 Azure 存储或 Azure SQL、使用客户提供的密钥来加密数据的 Azure 事件中心、访问在密钥保管库中存储的服务凭据的 Azure 数据工厂、从密钥保管库检索机密的 Azure Pipelines，以及其他类似方案。 在这些情况下，必须检查产品是否支持启用了防火墙的密钥保管库。 此支持通常与 Key Vault 防火墙的[受信任服务](overview-vnet-service-endpoints.md#trusted-services)功能一起执行。 不过，由于各种原因，许多产品并未包含在受信任服务的列表中。 在这种情况下，请联系特定于产品的支持人员。

少数 Azure 产品支持 *vnet 注入* 的概念。 简单地说，产品会将网络设备添加到客户虚拟网络，这样网络设备就可以发送请求，就如同被部署到虚拟网络一样。 一个重要示例是 [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。 类似于这样的产品可以使用专用链接向密钥保管库发出请求，本故障排除指南可能会有所帮助。

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2.确认连接已获得批准并成功

以下步骤验证专用终结点连接是否已获得批准并成功：

1. 打开 Azure 门户，然后打开密钥保管库资源。
2. 在左侧菜单中选择“网络”。
3. 单击“专用终结点连接”选项卡。此时会显示所有专用终结点连接及其各自的状态。 如果没有连接，或者虚拟网络的连接缺失，则必须创建新的专用终结点。 稍后会对此进行介绍。
4. 仍在“专用终结点连接”中查找你正在诊断的连接，并确认“连接状态”为“已批准”且“预配状态”为“已成功”。
    - 如果连接处于“挂起”状态，你可能可以直接批准它。
    - 如果连接处于“已拒绝”、“已失败”、“错误”、“已断开连接”状态或其他状态，则说明它根本未生效，你必须创建新的专用终结点资源。

为了保持整洁，最好是删除无效的连接。

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3.确认已正确配置密钥保管库防火墙

>[!IMPORTANT]
> 更改防火墙设置可能会从仍未使用专用链接的合法客户端中删除访问权限。 请确保了解防火墙配置中每项更改的含义。

一项重要的概念是，专用链接功能只提供对为了防止数据外泄而关闭的虚拟网络中的密钥保管库的访问。 它不删除任何现有访问权限。 为了有效地阻止从公共 Internet 进行的访问，必须显式启用密钥保管库防火墙：

1. 打开 Azure 门户，然后打开密钥保管库资源。
2. 在左侧菜单中选择“网络”。
3. 确保在顶部选择“防火墙和虚拟网络”选项卡。
4. 确保选择“专用终结点和所选网络”选项。 如果发现“所有网络”处于选中状态，你就会明白为何外部客户端仍能够访问密钥保管库。

以下声明也适用于防火墙设置：

- 专用链接功能不要求在密钥保管库防火墙设置中指定任何“虚拟网络”。 即使密钥保管库防火墙设置中未指定任何虚拟网络，使用密钥保管库专用 IP 地址（参见下一部分）的所有请求也必须有效。
- 专用链接功能不要求在密钥保管库防火墙设置中指定任何 IP 地址。 同样，即使防火墙设置中未指定任何 IP 地址，使用密钥保管库专用 IP 地址的所有请求也必须有效。

如果使用的是专用链接，则在密钥保管库防火墙中指定虚拟网络或 IP 地址的仅有的动机是：

- 你有一个混合系统，在其中，某些客户端使用专用链接，某些客户端使用服务终结点，某些客户端使用公共 IP 地址。
- 你要转换到专用链接。 在这种情况下，当确认所有客户端都使用专用链接后，应从密钥保管库防火墙设置中删除虚拟网络和 IP 地址。

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4.确保密钥保管库有专用 IP 地址

### <a name="difference-between-private-and-public-ip-addresses"></a>专用 IP 地址和公共 IP 地址之间的差异

专用 IP 地址始终采用以下格式之一：

- 10.x.x.x：示例：`10.1.2.3`、`10.56.34.12`。
- 172.16.x.x 到 172.32.x.x：示例：`172.20.1.1`、`172.31.67.89`。
- 192.168.x.x：示例：`192.168.0.1`、`192.168.100.7`

某些 IP 地址和范围已被保留：

- 224.x.x.x：多播
- 255.255.255.255：广播
- 127.x.x.x：环回
- 169.254.x.x：本地链路
- 168.63.129.16：内部 DNS

所有其他 IP 地址都是公共的。

浏览门户或运行某个显示 IP 地址的命令时，请确保可以确定该 IP 地址是专用的、公共的还是保留的。 要使专用链接生效，密钥保管库主机名必须解析为属于虚拟网络地址空间的专用 IP 地址。

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>在虚拟网络中查找密钥保管库专用 IP 地址

你需要诊断主机名解析，因此必须知道启用了专用链接的密钥保管库的确切专用 IP 地址。 若要查找该地址，请按照以下过程操作：

1. 打开 Azure 门户，然后打开密钥保管库资源。
2. 在左侧菜单中选择“网络”。
3. 单击“专用终结点连接”选项卡。此时会显示所有专用终结点连接及其各自的状态。
4. 找到你正在诊断的连接，并确认“连接状态”为“已批准” 且“预配状态”为“已成功”。 如果未看到上述状态，请返回到本文档前面的部分。
5. 找到正确的项后，请单击“专用终结点”列中的链接。 此时会打开“专用终结点”资源。
6. “概述”页可能会显示一个名为“自定义 DNS 设置”的部分。 确认只有一个匹配密钥保管库主机名的条目。 该条目显示密钥保管库专用 IP 地址。
7. 你还可以单击“网络接口”上的链接，并确认专用 IP 地址与上一步中显示的相同。 网络接口是表示密钥保管库的虚拟设备。

IP 地址是 VM 和在同一虚拟网络中运行的其他设备用来连接到密钥保管库的。 请记下 IP 地址，或让浏览器标签页保持打开状态，在进行进一步调查时不要触摸它。

>[!NOTE]
> 如果密钥保管库有多个专用终结点，则会有多个专用 IP 地址。 仅当有多个虚拟网络访问同一密钥保管库且每个虚拟网络都通过其自己的专用终结点（专用终结点属于单个虚拟网络）进行访问时，这才有用。 请确保为正确的虚拟网络诊断问题，并在上面的过程中选择正确的专用终结点连接。 此外，请勿为同一虚拟网络中的同一个 Key Vault 创建多个专用终结点。 创建多个专用终结点没有必要，并且会造成混乱。

## <a name="5-validate-the-dns-resolution"></a>5.验证 DNS 解析

DNS 解析是将密钥保管库主机名（例如 `fabrikam.vault.azure.net`）转换为 IP 地址（例如 `10.1.2.3`）的过程。 以下子部分展示了每个方案中的 DNS 解析的预期结果。

### <a name="key-vaults-without-private-link"></a>没有专用链接的密钥保管库

此部分专用于学习。 如果密钥保管库没有专用终结点连接处于“已批准”状态，则解析主机名会产生类似于下面这样的结果：

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux：

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

可以看到名称解析为公共 IP 地址，没有 `privatelink` 别名。 稍后我们会对此别名进行说明，现在不用管它。

不管计算机是连接到虚拟网络的计算机，还是使用 Internet 连接的任意计算机，以上结果都是预期的结果。 出现这种情况的原因是密钥保管库没有处于“已批准”状态的专用终结点连接，因此不需要密钥保管库支持专用链接。

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>使用专用链接从任意 Internet 计算机进行解析的密钥保管库

如果密钥保管库有一个或多个专用终结点连接处于“已批准”状态，而你从连接到 Internet 的任意计算机（一台未连接到专用终结点所在的虚拟网络的计算机）解析主机名，则会出现以下结果：

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux：

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

与上一方案明显不同的是，有一个值为 `{vaultname}.privatelink.vaultcore.azure.net` 的新别名。 这意味着密钥保管库数据平面已准备好接受来自专用链接的请求。

这并不意味着从虚拟网络之外的计算机执行的请求（例如刚才使用的请求）会使用专用链接 - 它们不会这样做。 可以从主机名仍解析为公共 IP 地址这一事实看到这一点。 只有连接到虚拟网络的计算机才能使用专用链接。 稍后会详述这一点。

如果看不到 `privatelink` 别名，则表示密钥保管库有 0 个处于“`Approved`”状态的专用终结点连接。 重试之前，请回到[此部分](#2-confirm-that-the-connection-is-approved-and-succeeded)。

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>使用专用链接从虚拟网络进行解析的密钥保管库

如果密钥保管库有一个或多个专用终结点连接处于“已批准”状态，而你从一台连接到虚拟网络（已在其中创建专用终结点）的计算机解析主机名，则预期的响应如下：

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux：

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

有两个明显的区别。 首先，名称解析为专用 IP 地址。 该地址必须是在本文[相应部分](#find-the-key-vault-private-ip-address-in-the-virtual-network)找到的 IP 地址。 其次，`privatelink` 别名后面没有其他别名。 出现这种情况的原因是，虚拟网络 DNS 服务器会截获别名链，并直接从名称 `fabrikam.privatelink.vaultcore.azure.net` 返回专用 IP 地址。 该条目实际上是专用 DNS 区域中的 `A` 记录。 稍后会详述这一点。

>[!NOTE]
> 上述结果仅发生在一台虚拟机上，该虚拟机已连接到在其中创建了专用终结点的虚拟网络。 如果没有在包含专用终结点的虚拟网络中部署 VM，请部署一台并通过远程方式连接到该 VM，然后执行上面的 `nslookup` 命令 (Windows) 或 `host` 命令 (Linux)。

如果在连接到创建了专用终结点的虚拟网络的虚拟机上运行这些命令，但它们未显示密钥保管库专用 IP 地址，则可参阅下一部分，了解如何解决此问题。

## <a name="6-validate-the-private-dns-zone"></a>6.验证专用 DNS 区域

如果 DNS 解析未按上一部分所述方式进行，则专用 DNS 区域可能存在问题，可参阅此部分来解决它。 如果 DNS 解析显示了正确的密钥保管库专用 IP 地址，则专用 DNS 区域可能是正确的。 你可以跳过这整个部分。

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>确认所需的专用 DNS 区域资源存在

Azure 订阅必须有[专用 DNS 区域](../../dns/private-dns-privatednszone.md)资源，该资源必须有以下确切名称：

`privatelink.vaultcore.azure.net`

可以通过转到门户中的订阅页并选择左侧菜单中的“资源”来检查该资源是否存在。 资源名称必须是 `privatelink.vaultcore.azure.net`，资源类型必须是“专用 DNS 区域”。

通常情况下，当你使用通用过程创建专用终结点时，系统会自动创建该资源。 但在某些情况下，系统不会自动创建该资源，你必须手动创建它。 该资源也可能已被意外删除。

如果没有该资源，请在订阅中创建新的专用 DNS 区域资源。 请记住，名称必须与 `privatelink.vaultcore.azure.net` 完全一致，不包含空格或其他点。 如果指定的名称不正确，则本文所述的名称解析将不起作用。 若要详细了解如何创建此资源，请参阅[使用 Azure 门户创建 Azure 专用 DNS 区域](../../dns/private-dns-getstarted-portal.md)。 如果按该页的要求操作，则可以跳过虚拟网络创建操作，因为你此时应该已经有一个虚拟网络。 还可以跳过通过虚拟机进行的验证过程。

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>确认专用 DNS 区域已链接到虚拟网络

单纯有一个专用 DNS 区域还不够。 还必须将它链接到包含专用终结点的虚拟网络。 如果专用 DNS 区域未链接到正确的虚拟网络，则从该虚拟网络进行的任何 DNS 解析都会忽略专用 DNS 区域。

打开专用 DNS 区域资源，然后单击左侧菜单中的“虚拟网络链接”选项。 此时会显示一个链接列表，每个链接都有一个订阅中的虚拟网络的名称。 包含专用终结点资源的虚拟网络必须在此处列出。 如果它不存在，请添加它。 有关详细步骤，请参阅[链接虚拟网络](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)。 可以让“启用自动注册”保持取消选中状态，该功能与专用链接无关。

专用 DNS 区域链接到虚拟网络后，源自虚拟网络的 DNS 请求会在专用 DNS 区域中查找名称。 这是在虚拟机上执行正确的地址解析所需的，这些虚拟机已连接到在其中创建了专用终结点的虚拟网络。

>[!NOTE]
> 如果你刚刚保存了该链接，则此操作可能需要一段时间才能生效，即使在门户指示操作完成后也是如此。 可能还需要重启用于测试 DNS 解析的 VM。

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>确认专用 DNS 区域包含正确的 A 记录

请使用门户打开名为 `privatelink.vaultcore.azure.net` 的专用 DNS 区域。 “概述”页会显示所有记录。 默认情况下，会有一条名称为 `@` 且类型 `SOA` 的记录，这表示“起始授权机构”。 请勿对其执行任何操作。

若要使密钥保管库名称解析生效，必须有一个 `A` 记录，该记录使用简单的保管库名称，不含后缀或点。 例如，如果主机名为 `fabrikam.vault.azure.net`，则必须存在名称为 `fabrikam` 且不含任何后缀或点的 `A` 记录。

此外，`A` 记录（IP 地址）的值必须是[密钥保管库专用 IP 地址](#find-the-key-vault-private-ip-address-in-the-virtual-network)。 如果找到 `A` 记录，但其中包含错误的 IP 地址，则必须删除错误的 IP 地址并添加新的 IP 地址。 建议删除整个 `A` 记录并添加一个新记录。

>[!NOTE]
> 删除或修改 `A` 记录时，计算机仍可能会解析为旧 IP 地址，因为 TTL（生存时间）值可能尚未过期。 建议你始终将 TTL 值指定为不小于 10 秒且不大于 600 秒（10 分钟）。 如果指定的值太大，则客户端可能需要很长时间才能从中断中恢复。

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>针对多个虚拟网络的 DNS 解析

如果有多个虚拟网络，每个虚拟网络都通过其自己的专用终结点资源来引用同一密钥保管库，则密钥保管库主机名需要根据网络解析为不同的专用 IP 地址。 这意味着还需要多个专用 DNS 区域，每个区域链接到不同的虚拟网络，并在 `A` 记录中使用不同的 IP 地址。

在更高级的方案中，虚拟网络可能已启用对等互连。 在这种情况下，只有一个虚拟网络需要专用终结点资源，尽管这两个虚拟网络都可能需要链接到专用 DNS 区域资源。 本文档不直接介绍这种情况。

### <a name="understand-that-you-have-control-over-dns-resolution"></a>明白你可以控制 DNS 解析

如[上一部分](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)所述，具有专用链接的密钥保管库在其公共注册中的别名为 `{vaultname}.privatelink.vaultcore.azure.net`。 虚拟网络所用的 DNS 服务器使用公共注册，但会检查每个别名是否有专用注册，在找到专用注册的情况下不再遵循在公共注册中定义的别名。

此逻辑意味着，如果虚拟网络链接到名为 `privatelink.vaultcore.azure.net` 的专用 DNS 区域，并且密钥保管库的公共 DNS 注册别名为 `fabrikam.privatelink.vaultcore.azure.net`（注意，密钥保管库主机名后缀与专用 DNS 区域名称精确匹配），则 DNS 查询会在专用 DNS 区域中查找名称为 `fabrikam` 的 `A` 记录。 如果找到 `A` 记录，则会在 DNS 查询中返回其 IP 地址，不再在公共 DNS 注册中进一步查找。

可以看到，你可以控制名称解析。 之所以进行此设计，根本原因是：

- 你可能有一个复杂的方案，该方案涉及自定义 DNS 服务器以及与本地网络的集成。 在这种情况下，需要控制将名称转换为 IP 地址的方式。
- 你可能需要访问没有专用链接的密钥保管库。 在这种情况下，从虚拟网络解析主机名时必须返回公共 IP 地址，这是因为没有专用链接的密钥保管库在名称注册中没有 `privatelink` 别名。

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7.验证对密钥保管库的请求是否使用专用链接

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>查询密钥保管库的 `/healthstatus` 终结点

密钥保管库提供可用于诊断的 `/healthstatus` 终结点。 响应头包含源 IP 地址，就像密钥保管库服务所看到的那样。 可以使用以下命令调用该终结点（请记得使用密钥保管库主机名）：

Windows (PowerShell)：

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux 或最新版本的 Windows 10，其中包含 `curl`：

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

如果未获得类似于这样的输出，或者出现网络错误，则意味着无法通过指定的主机名（在示例中为 `fabrikam.vault.azure.net`）访问密钥保管库。 主机名未解析为正确的 IP 地址，或者在传输层出现连接性问题。 这可能是由路由问题、包删除和其他原因造成的。 需要进一步调查。

响应必须包含 `x-ms-keyvault-network-info` 头：

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

`x-ms-keyvault-network-info` 头中的 `addr` 字段显示请求源的 IP 地址。 此 IP 地址可能是以下地址之一：

- 进行请求的计算机的专用 IP 地址。 这表明请求使用的是专用链接或服务终结点。 这是专用链接的预期结果。
- 某个其他的专用 IP 地址，不是来自进行请求的计算机。 这表明某个自定义路由有效。 这仍然表明请求使用的是专用链接或服务终结点。
- 某个公共 IP 地址。 这表明请求将通过网关 (NAT) 设备路由到 Internet。 这表明请求未使用专用链接，某些问题需要修复。 这种情况的常见原因是：1) 专用终结点未处于“已批准”和“已成功”状态；2) 主机名未解析为密钥保管库的专用 IP 地址。 本文包括这两种情况下的故障排除操作。

>[!NOTE]
> 如果向 `/healthstatus` 发出的请求有效，但缺少 `x-ms-keyvault-network-info` 头，则终结点可能不是由密钥保管库提供服务的。 由于上述命令也验证 HTTPS 证书，缺少头可能表明存在篡改操作。

### <a name="query-the-key-vault-ip-address-directly"></a>直接查询密钥保管库 IP 地址

>[!IMPORTANT]
> 不经过 HTTPS 证书验证就访问密钥保管库是危险的，只能用于学习目的。 如果没有进行此客户端验证，则生产代码不得访问密钥保管库。 即使你只是诊断问题，但如果你经常在对密钥保管库发出的请求中禁用 HTTPS 证书验证，则也可能会受到那些不会显示的篡改尝试的攻击。

如果安装了 PowerShell 的最新版本，则可使用 `-SkipCertificateCheck` 跳过 HTTPS 证书检查，然后直接将[密钥保管库 IP 地址](#find-the-key-vault-private-ip-address-in-the-virtual-network)设定为目标：

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

如果使用 `curl`，则可使用 `-k` 参数执行相同操作：

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

响应必须与上一部分的相同，这意味着它必须包含具有相同值的 `x-ms-keyvault-network-info` 头。 `/healthstatus` 终结点不“在意”你使用的是密钥保管库主机名还是 IP 地址。

如果你看到 `x-ms-keyvault-network-info` 针对使用密钥保管库主机名的请求返回一个值，针对使用 IP 地址的请求返回另一个值，则表明每个请求针对的是不同的终结点。 请参阅上一部分中来自 `x-ms-keyvault-network-info` 的 `addr` 字段的说明，以确定哪种情况是错误的，需要修复。

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8.造成影响的其他更改和自定义

以下各项并非详尽的调查操作。 这些操作会告知你查找其他问题的位置，但你必须具备高级网络知识才能修复这些方案中的问题。

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>在虚拟网络中诊断自定义 DNS 服务器

在门户中，打开虚拟网络资源。 在左侧菜单中，打开“DNS 服务器”。 如果使用的是“自定义”，则 DNS 解析可能不符合本文档所述。 必须对 DNS 服务器解析密钥保管库主机名的方式进行诊断。

如果使用的是 Azure 提供的默认 DNS 服务器，则这整个文档都适用。

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>在虚拟机上诊断 hosts 重写或自定义 DNS 服务器

许多操作系统允许按主机名设置显式的固定 IP 地址，这通常通过更改 `hosts` 文件来实现。 这些系统可能还允许重写 DNS 服务器。 如果你使用这些方案之一，则请使用特定于系统的诊断选项。

### <a name="promiscuous-proxies-fiddler-etc"></a>混合代理（Fiddler 等）

除非明确指出，否则本文中的诊断选项仅适用于环境中不存在混合代理的情况。 尽管这些代理通常以独占方式安装在所诊断的计算机中（Fiddler 是最常见的例子），但高级管理员可以覆盖根证书颁发机构 (CA)，并在为网络中的多台计算机提供服务的网关设备中安装混合代理。 这些代理可能会显著影响安全性和可靠性。 Microsoft 不支持那些使用此类产品的配置。

### <a name="other-things-that-may-affect-connectivity"></a>可能影响连接性的其他因素

这是可以在高级或复杂方案中找到的项的不完整列表：

- 防火墙设置，不管是连接到虚拟网络的 Azure 防火墙，还是在虚拟网络或计算机中部署的自定义防火墙解决方案。
- 网络对等互连，这可能会对使用哪些 DNS 服务器以及如何路由流量造成影响。
- 自定义网关 (NAT) 解决方案，这可能会影响流量（包括来自 DNS 查询的流量）的路由方式。
