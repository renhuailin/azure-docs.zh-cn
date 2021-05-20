---
title: VM 见解来宾运行状况疑难解答（预览）
description: 介绍了在 VM 见解运行状况出现问题时可以采取的故障排除步骤。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051933"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>VM 见解来宾运行状况疑难解答（预览）
本文介绍了在 VM 见解运行状况出现问题时可以采取的故障排除步骤。

## <a name="error-message-that-no-data-is-available"></a>指示没有数据可用的错误消息 

![无数据](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>验证虚拟机是否满足配置要求

1. 验证虚拟机是否为 Azure 虚拟机。 当前不支持 Azure Arc for servers。
2. 验证虚拟机是否正在运行[受支持的操作系统](vminsights-health-enable.md?current-limitations.md)。
3. 验证虚拟机是否安装在[受支持的区域](vminsights-health-enable.md?current-limitations.md)中。
4. 验证 Log Analytics 工作区是否安装在[受支持的区域](vminsights-health-enable.md?current-limitations.md)中。

### <a name="verify-that-the-vm-is-properly-onboarded"></a>验证 VM 是否已正确加入
验证是否在虚拟机上成功预配了 Azure Monitor 代理扩展和来宾 VM 运行状况代理。 从 Azure 门户中的虚拟机菜单中选择“扩展”，并确保列出了两个代理。

![VM 扩展](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>验证是否在虚拟机上启用了系统分配的标识
验证是否在虚拟机上启用了系统分配的标识。 从 Azure 门户中的虚拟机菜单中选择“标识”。 如果启用了用户托管标识，则无论系统托管标识的状态如何，Azure Monitor 代理都无法与配置服务通信，并且来宾运行状况扩展将不起作用。

![系统分配标识](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>验证数据收集规则
验证将运行状况扩展指定为数据源的数据收集规则是否与虚拟机关联。

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>指示由于权限不足而导致请求错误的错误消息
此错误指示未在订阅中注册 Microsoft.WorkloadMonitor 资源提供程序。 有关注册此资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。 

![错误的请求](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>后续步骤

- [获取 VM 见解的来宾运行状况功能的概述](vminsights-health-overview.md)