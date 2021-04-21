---
title: 在 Azure 门户中为 VM 创建 FQDN
description: 了解如何在 Azure 门户中为虚拟机创建完全限定的域名 (FQDN)。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42c53e72de7f6b2943086544f607042957f4b849
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220073"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 门户中为 Linux VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.com)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然此门户不会创建[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)，但可以在创建 VM 后添加完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。 

## <a name="create-a-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以在门户中创建 [Linux](./linux/quick-create-portal.md) 或 [Windows](./windows/quick-create-portal.md) VM。 在 VM 正常运行后，请按照以下步骤操作：


1. 在门户中选择 VM。 
1. 在左侧菜单中，选择“属性”
1. 在“公共 IP 地址\DNS 名称标签”下，选择你的 IP 地址。
2. 在“DNS 名称标签”下，输入要使用的前缀。
3. 在页面顶部选择“保存”。
4. 在左侧菜单中选择“概述”，以返回到 VM 概述边栏选项卡。
5. 验证是否正确显示“DNS 名称”。 

## <a name="next-steps"></a>后续步骤

你还可以使用 [Azure DNS 区域](../dns/dns-getstarted-portal.md)来管理 DNS。

