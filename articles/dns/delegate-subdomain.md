---
title: 委托子域 - Azure DNS
description: 通过此学习路径，开始委托 Azure DNS 子域。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/03/2021
ms.author: rohink
ms.openlocfilehash: 11c9fd2e453db37a5aa985bcc53acdabf4a42aaf
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763300"
---
# <a name="delegate-an-azure-dns-subdomain"></a>委托 Azure DNS 子域

可以使用 Azure 门户委托 DNS 子域。 例如，如果拥有 contoso.com 域，可将名为“engineering”的子域委托给另一个可以独立于 contoso.com 区域进行管理的单独区域。

如果需要，也可以使用 [Azure PowerShell](delegate-subdomain-ps.md) 委托子域。

## <a name="prerequisites"></a>先决条件

若要委托 Azure DNS 子域，必须先将公共域委托给 Azure DNS。 有关如何为委托配置名称服务器的说明，请参阅[将域委托给 Azure DNS](./dns-delegate-domain-azure-dns.md)。 将域委托给 Azure DNS 区域后，可以委托子域。

> [!NOTE]
> 本文通篇都使用 contoso.com 作为示例。 请将 contoso.com 替换为你自己的域名。

## <a name="create-a-zone-for-your-subdomain"></a>为子域创建区域

首先，为 **engineering** 域创建区域。

1. 在 Azure 门户中，选择“+ 创建资源”。

1. 搜索“DNS 区域”，然后选择“创建”。

1. 在“创建 DNS 区域”页上，选择区域的资源组。 可以使用父区域所在的同一资源组将类似资源放到一起。

1.  输入 `engineering.contoso.com` 作为“名称”，然后选择“创建”。

1. 部署成功后，转到新区域。

## <a name="note-the-name-servers"></a>记下名称服务器

接下来，记下 engineering 子域的四个名称服务器。

在“engineering”区域概述页面中，记下区域的四个名称服务器。 稍后会需要这些名称服务器。

## <a name="create-a-test-record"></a>创建一条测试记录

创建用于测试的 **A** 记录。 例如，创建 **www** A 记录，并为其配置 **10.10.10.10** IP 地址。

## <a name="create-an-ns-record"></a>创建 NS 记录

接下来，为 **engineering** 区域创建名称服务器 (NS) 记录。

1. 导航到父域的区域。

1. 选择概述页面顶部的“+ 记录集”。

1. 在“添加记录集”页面中的“名称”文本框内，键入“engineering”。  

1. 对于“类型”，请选择“NS”。

1. 在“名称服务器”下，输入前面从“engineering”区域记录的四个名称服务器。

1. 选择“确定”保存记录。

## <a name="test-the-delegation"></a>测试委托

使用 nslookup 测试委托。

1. 打开 PowerShell 窗口。

1. 在命令提示符下，键入 `nslookup www.engineering.contoso.com.`

1. 应会收到一条非权威回复，其中显示了地址 **10.10.10.10**。

## <a name="next-steps"></a>后续步骤

了解如何[为 Azure 中托管的服务配置反向 DNS](dns-reverse-dns-for-azure-services.md)。
