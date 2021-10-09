---
title: 了解 Azure 安全中心内的实时虚拟机访问
description: 本文档说明 Azure 安全中心中的实时 VM 访问如何帮助你控制对 Azure 虚拟机的访问
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 8a6fe163ade61df65f6ff0d9ba4f2862866094b7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631678"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>了解实时 (JIT) VM 访问

本页介绍 Azure 安全中心的实时 (JIT) VM 访问功能背后的原理和建议背后的逻辑。

若要了解如何使用 Azure 门户（安全中心或 Azure 虚拟机）或以编程方式向 VM 应用 JIT，请参阅[如何使用 JIT 保护管理端口](security-center-just-in-time.md)。


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>虚拟机上的开放管理端口的风险

威胁参与者会主动搜寻带有开放管理端口（如 RDP 或 SSH）的可访问计算机。 你的所有虚拟机都是潜在的攻击目标。 VM 在被成功入侵后将会用作进一步攻击环境中资源的入口点。



## <a name="why-jit-vm-access-is-the-solution"></a>为什么 JIT VM 访问会是解决方案 

与所有网络安全防护技术一样，你的目标应该是减小攻击面。 在这种情况下，这意味着开放的端口更少，特别是管理端口。

你的合法用户也使用这些端口，因此将其保持关闭状态是不切实际的。

为了解决此难题，Azure 安全中心提供了 JIT。 使用 JIT，可以锁定发往 VM 的入站流量，降低遭受攻击的可能性，同时在需要时还允许轻松连接到 VM。



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>JIT 如何与网络安全组和 Azure 防火墙一起运行

启用实时 VM 访问时，可以选择 VM 上要阻止入站流量的端口。 安全中心确保你选择的端口在[网络安全组](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) 和 [Azure 防火墙规则](../firewall/rule-processing.md)中有“拒绝所有入站流量”规则。 这些规则限制对 Azure VM 管理端口的访问，并防止其受到攻击。 

如果所选端口已有了其他规则，则现有的这些规则优先于新的“拒绝所有入站流量”规则。 如果所选端口没有现有的规则，则新规则在 NSG 和 Azure 防火墙中的优先级最高。

当用户请求访问 VM 时，安全中心会检查用户是否对该 VM 具有 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) 权限。 如果请求获得批准，安全中心将配置网络安全组 (NSG) 和 Azure 防火墙，以便允许在指定的时间量内从相关 IP 地址（或范围）发往所选端口的入站流量。 在该时间到期后，安全中心会将 NSG 还原为以前的状态。 已经建立的连接不会中断。

> [!NOTE]
> JIT 不支持由 [Azure 防火墙管理器](../firewall-manager/overview.md)控制的 Azure 防火墙保护的 VM。  Azure 防火墙必须配置规则（经典），并且不能使用防火墙策略。




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>安全中心如何确定哪些 VM 应当应用 JIT

下图显示了在决定如何对受支持的 VM 分类时安全中心应用的逻辑： 

[![实时 (JIT) 虚拟机 (VM) 逻辑流。](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

当安全中心发现可以受益于 JIT 的计算机时，它会将该计算机添加到建议的“不正常的资源”选项卡中。 

![实时 (JIT) 虚拟机 (VM) 访问建议。](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---just-in-time-virtual-machine-access"></a>常见问题解答 - 实时虚拟机访问

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>配置和使用 JIT 时需要哪些权限？

JIT 要求在订阅上启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。 

“读取者”角色和“安全读取者”角色都可以查看 JIT 状态和参数。

如果要创建可用于 JIT 的自定义角色，则需要下表中的详细信息。

> [!TIP]
> 若要为那些需要请求对 VM 进行 JIT 访问而不执行其他 JIT 操作的用户创建最小特权角色，请使用安全中心 GitHub 社区页面中的 [Set-JitLeastPrivilegedRole 脚本](https://github.com/Azure/Azure-Security-Center/tree/main/Powershell%20scripts/JIT%20Scripts/JIT%20Custom%20Role)。

| 使用户能够： | 要设置的权限|
| --- | --- |
|配置或编辑 VM 的 JIT 策略 | *将这些“操作”分配给角色：*  <ul><li>在与 VM 关联的订阅或资源组的范围内：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在 VM 的订阅或资源组的范围内： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|请求 JIT 对 VM 的访问权限 | *将这些“操作”分配给用户：*  <ul><li>在与 VM 关联的订阅或资源组的范围内：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在与 VM 关联的订阅或资源组的范围内：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在订阅、资源组或 VM 的范围内：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在订阅、资源组或 VM 的范围内：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|读取 JIT 策略| *将这些“操作”分配给用户：*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>后续步骤

本页说明了为何应使用实时 (JIT) 虚拟机 (VM) 访问。 若要了解如何启用 JIT 并请求访问已启用 JIT 的 VM，请参阅以下内容：

> [!div class="nextstepaction"]
> [如何使用 JIT 保护管理端口](security-center-just-in-time.md)
