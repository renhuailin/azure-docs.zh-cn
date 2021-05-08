---
title: 配置 Windows 虚拟桌面负载均衡 - Azure
description: 如何为 Windows 虚拟桌面环境配置负载均衡方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0e742a046d43fef6e8263f73fc2ca8460848ad40
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448111"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>配置 Windows 虚拟桌面负载均衡方法

为主机池配置负载均衡方法，可以调整 Windows 虚拟桌面环境，从而更好地满足用户的需求。

>[!NOTE]
> 这种操作不适用于永久性桌面主机池，因为用户始终能够 1:1 映射到主机池中的某个会话主机。

## <a name="prerequisites"></a>先决条件

本文假设已按照“[设置 Windows 虚拟桌面 powershell 模块](powershell-module.md)”中的说明，下载和安装 PowerShell 模块并登录到 Azure 帐户。

## <a name="configure-breadth-first-load-balancing"></a>配置广度优先负载均衡

广度优先负载均衡是新的非持久性主机池的默认配置。 广度优先负载均衡可在主机池中所有可用会话主机之间分配新的用户会话。 配置广度优先负载均衡时，可以为主机池中的每个会话主机设置最大会话限制。

若要配置主机池，使其在不调整最大会话限制的情况下执行广度优先负载均衡，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

然后，为确保已设置了广度优先负载均衡方法，请运行以下 cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

若要配置主机池，使其执行广度优先负载均衡并使用新的最大会话限制，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>配置深度优先负载均衡

深度优先负载均衡可将新的用户会话分发到可用会话主机，这些会话主机具有最大连接数但未达到最大会话限制阈值。

>[!IMPORTANT]
>配置深度优先负载均衡时，必须为主机池中的每个会话主机设置最大会话限制。

若要将主机池配置为执行深度优先负载均衡，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> 深度优先负载均衡算法可根据最大会话主机限制 (`-MaxSessionLimit`) 将会话分发到会话主机。 此参数的默认值为`999999`，这也是此变量可能设置的最大数字。 使用深度优先负载均衡算法时，此参数是必需的。 为了获得最佳的用户体验，请务必将最大会话主机限制参数更改为最适合环境的数字。

若要确保已更新设置，请运行以下 cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>使用 Azure 门户配置负载均衡

还可以使用 Azure 门户配置负载均衡。

配置负载均衡：

1. 登录 Azure 门户：https://portal.azure.com。
2. 搜索并选择服“服务”下面的“**Windows 虚拟桌面**”。
3. 在“Windows 虚拟桌面”页中，选择“主机池”。
4. 选择要编辑的主机池的名称。
5. 选择“属性”。
6. 在该字段中输入“**最大会话限制**”，并在下拉菜单中选择要用于此主机池的 **负载均衡算法**。
7. 选择“保存”。 这会应用新的负载均衡设置。