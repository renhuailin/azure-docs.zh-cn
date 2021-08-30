---
title: Azure 虚拟机扩展和功能
description: 详细了解 Azure VM 扩展
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: a7a11dde69554ae732590ddf36f8cf65929197fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737408"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 虚拟机扩展和功能
扩展是小型应用程序，用于在 Azure VM 上提供部署后配置和自动化。 Azure 平台可承载许多扩展，涵盖 VM 配置、监视、安全性和实用工具应用程序。 发布服务器采用某个应用程序，将其包装到扩展中，对安装进行简化。 你只需提供必需的参数。 

## <a name="how-can-i-find-what-extensions-are-available"></a>如何了解哪些扩展可用？
若要查看可用扩展，可以先在左侧菜单中选择 VM，然后选择“扩展”。 若要拉取扩展的完整列表，请参阅[了解适用于 Linux 的 VM 扩展](features-linux.md)和[了解适用于 Windows 的 VM 扩展](features-windows.md)。

## <a name="how-can-i-install-an-extension"></a>如何安装扩展？
可以通过 Azure CLI、PowerShell、资源管理器模板和 Azure 门户托管 Azure VM 扩展。 若要试用扩展，请转到 Azure 门户，选择“自定义脚本扩展”，然后传入运行扩展所需的命令或脚本。

有关详细信息，请参阅 [Windows 自定义脚本扩展](custom-script-windows.md)和 [Linux 自定义脚本扩展](custom-script-linux.md)。

## <a name="how-do-i-manage-extension-application-lifecycle"></a>如何管理扩展应用程序生命周期？
不需要直接连接到 VM 即可安装或删除扩展。 Azure 扩展生命周期在 VM 外管理，已集成到 Azure 平台中。

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>关于扩展，有什么其他需要考虑的内容？
某些单独的 VM 扩展应用程序可能有其自己的环境先决条件，如对终结点的访问权限。 每个扩展都有一篇文章，其中会介绍先决条件，包括支持哪些操作系统。

## <a name="troubleshoot-extensions"></a>排查扩展问题

如果你需要了解 Windows VM 扩展的常规故障排除步骤，请参阅 [Azure Windows VM 扩展故障排除](troubleshoot.md)。

此外，还可以在扩展概述的“故障排除和支持”部分中找到每个扩展的特定故障排除信息。 下面列出了可用的故障排除信息：

| 命名空间 | 故障排除 |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [适用于 Linux 的 Azure Monitor 依赖项](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [适用于 Windows 的 Azure Monitor 依赖项](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [适用于 Linux 的 Azure 磁盘加密](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [适用于 Windows 的 Azure 磁盘加密](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [适用于 Windows 的自定义脚本](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [适用于 Linux 的 Desired State Configuration](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [适用于 Windows 的 Desired State Configuration](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [适用于 Linux 的 NVIDIA GPU 驱动程序扩展](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [适用于 Windows 的 NVIDIA GPU 驱动程序扩展](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [适用于 Windows 的反恶意软件扩展](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [用于 Linux 的 Azure Monitor](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [用于 Windows 的 Azure Monitor](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [适用于 Linux 的 Stackify Retrace](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [重置 Linux 密码](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [适用于 Linux 的快照](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [适用于 Windows 的快照](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>后续步骤
* 有关 Linux 代理和扩展工作原理的详细信息，请参阅[适用于 Linux 的 Azure VM 扩展和功能](features-linux.md)。
* 有关 Windows 来宾代理和扩展工作原理的详细信息，请参阅[适用于 Windows 的 Azure VM 扩展和功能](features-windows.md)。  
* 若要安装 Windows 来宾代理，请参阅 [Azure Windows 虚拟机代理概述](agent-windows.md)。  
* 若要安装 Linux 代理，请参阅 [Azure Linux 虚拟机代理概述](agent-linux.md)。  

