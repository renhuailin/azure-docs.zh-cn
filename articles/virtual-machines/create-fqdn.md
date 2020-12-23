---
title: 在 Azure 门户中为 VM 创建 FQDN
description: 了解如何在 Azure 门户中为虚拟机创建完全限定的域名 (FQDN) 。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351869"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 门户中为 Linux VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.com)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然此门户不会创建[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)，但可以在创建 VM 后添加完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。 

## <a name="create-a-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以在门户中创建 [Linux](./linux/quick-create-portal.md) 或 [Windows](./windows/quick-create-portal.md) VM。 在 VM 正常运行后，请按照以下步骤操作：


1. 在门户中选择 VM。 在 " **DNS 名称** " 下，选择 " **配置** "。
2. 输入 DNS 名称，然后选择页面顶部的 " **保存** "。
3. 若要返回到 VM 概述边栏选项卡，请选择右上角的 " **X** " 关闭 " **配置** " 边栏选项卡。 
4. 验证 *DNS 名称* 现在是否正确显示。
   



## <a name="next-steps"></a>后续步骤
VM 具有公共 IP 和 DNS 名称后，便可以部署常见的应用程序框架或服务，例如 nginx、MongoDB 和 Docker。

也可以深入了解如何[使用 Resource Manager](../azure-resource-manager/management/overview.md)，以获取生成 Azure 部署的相关提示。

