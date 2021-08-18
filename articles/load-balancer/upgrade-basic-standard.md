---
title: 从基本公共版升级到标准公共版 - Azure 负载均衡器
description: 本文介绍如何将 Azure 公共负载均衡器从基本 SKU 升级到标准 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 98a74903f5472807ff7eb562ea9db9e12ceeb4be
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108686"
---
# <a name="upgrade-azure-public-load-balancer"></a>升级 Azure 公共负载均衡器
[Azure 标准负载均衡器](load-balancer-overview.md)通过区域冗余提供丰富的功能和高可用性。 有关负载均衡器 SKU 的详细信息，请参阅[比较表](./skus.md#skus)。

升级分为两个阶段：

1. 将 IP 分配方法由动态更改为静态。
2. 运行 PowerShell 脚本以完成升级和流量迁移。

## <a name="upgrade-overview"></a>升级概述

我们提供了一个用于执行以下操作的 Azure PowerShell 脚本：

* 使用在基本标准负载均衡器的同一资源组中指定的位置创建标准 SKU 负载均衡器。
* 将公共 IP 地址从基本 SKU 就地升级到标准 SKU。
* 将基本 SKU 负载均衡器的配置无缝复制到新建的标准负载均衡器。
* 创建允许出站连接的默认出站规则。

### <a name="caveatslimitations"></a>注意事项/限制

* 脚本仅支持公共负载均衡器升级。 若要进行内部基本负载均衡器升级，请参阅[此页](./upgrade-basicinternal-standard.md)以获取说明。
* 在运行脚本之前，必须将公共 IP 地址的分配方法更改为“静态”。 
* 如果负载均衡器没有任何前端 IP 配置或后端池，则运行脚本时可能会遇到错误。 请确保负载均衡器不是空的。

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>将公共 IP 地址的分配方法更改为“静态”

* **下面是我们的建议步骤：

    1. 若要执行本快速入门中的任务，请登录 [Azure 门户](https://portal.azure.com)。
 
    1. 在左侧菜单中选择“所有资源”，然后从资源列表中选择“与基本负载均衡器关联的基本公共 IP 地址” 。
   
    1. 在“设置”下，选择“配置” 。
   
    1. 在“分配”下，选择“静态”。
    1. 选择“保存” 。
    >[!NOTE]
    >对于使用公共 IP 的 VM，在不保证 IP 地址相同的情况下，需要先创建标准 IP 地址。 将 VM 与基本 IP 取消关联，并将 VM 关联到新建的标准 IP 地址。 然后，即可按照说明将 VM 添加到标准负载均衡器的后端池。 

* **创建要添加到新建标准公共负载均衡器的后端池的新 VM**。
    * 在[此处](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)可以找到有关如何创建 VM 并将关联到标准负载均衡器的详细说明。


## <a name="download-the-script"></a>下载脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/5.0)下载迁移脚本。
## <a name="use-the-script"></a>使用脚本

根据本地 PowerShell 环境的设置和首选项，可以使用两个选项：

* 如果你尚未安装 Azure Az 模块或者不介意卸载 Azure Az 模块，最佳做法是使用 `Install-Script` 选项运行该脚本。
* 如果需要保留 Azure Az 模块，则最佳做法是下载并直接运行该脚本。

若要确定是否安装了 Azure Az 模块，请运行 `Get-InstalledModule -Name az`。 如果未看到任何已安装的 Az 模块，可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用 Install-Script 方法安装

只有尚未在计算机上安装 Azure Az 模块时才能使用此选项。 如果已安装，则以下命令将显示错误。 可以卸载 Azure Az 模块，或者另一个选项手动下载并运行该脚本。
  
使用以下命令运行该脚本：

`Install-Script -Name AzurePublicLBUpgrade`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用脚本安装

如果已安装某些 Azure Az 模块并且无法卸载它们（或者不想卸载），可以使用脚本下载链接中的“手动下载”选项卡手动下载该脚本。 此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动下载包](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 检查所需的参数：

   * **oldRgName: [String]:必需** – 这是要升级的现有基本负载均衡器的资源组。 若要查找此字符串值，请导航到 Azure 门户，选择你的基本负载均衡器源，然后单击该负载均衡器的“概览”。 资源组位于该页上。
   * **oldLBName: [String]:必需** – 这是要升级的现有基本负载均衡器的名称。 
   * **newLBName: [String]:必需** – 这是要创建的标准负载均衡器的名称。
1. 使用相应的参数运行脚本。 完成该脚本可能需要 5 到 7 分钟时间。

    **示例**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>为出站连接创建出站规则

按照[说明](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration)创建出站规则，以便执行以下操作：
* 从头开始定义出站 NAT。
* 缩放和优化现有出站 NAT 的行为。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>用于将配置从 v1 迁移到 v2 的 Azure PowerShell 脚本是否存在任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="how-long-does-the-upgrade-take"></a>升级需要多长时间？

该脚本通常需要 5-10 分钟才能完成，并且可能需要更长的时间，具体取决于负载均衡器配置的复杂性。 因此，请记住故障时间，并在必要时做出故障转移的计划。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 脚本是否还会将流量从基本负载均衡器切换到新建的标准负载均衡器？

是的。 Azure PowerShell 脚本不仅升级公共 IP 地址，将配置从基本负载均衡器复制到标准负载均衡器，而且还将 VM 迁移到新建标准公共负载均衡器的后面。 

## <a name="next-steps"></a>后续步骤

[了解标准负载均衡器](load-balancer-overview.md)
