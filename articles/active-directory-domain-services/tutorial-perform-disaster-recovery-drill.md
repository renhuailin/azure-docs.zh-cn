---
title: 教程 - 在 Azure AD 域服务中执行灾难恢复 | Microsoft Docs
description: 了解如何在 Azure AD 域服务中使用副本集执行灾难恢复演练
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: justinha
ms.openlocfilehash: 1816c4615e96a1e88c0a76d2defcb2c6d6686650
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701178"
---
# <a name="tutorial-perform-a-disaster-recovery-drill-using-replica-sets-in-azure-active-directory-domain-services"></a>教程：在 Azure Active Directory 域服务中使用副本集执行灾难恢复演练

本主题说明如何使用副本集对 Azure AD 域服务 (Azure AD DS) 执行灾难恢复 (DR) 演练。  这会通过对网络虚拟网络属性进行更改来阻止客户端对其中一个副本集进行访问，从而模拟该副本集离线。  这并不是真正的 DR 演练，因为副本集并不会离线。 

DR 演练将涉及： 

1. 客户端计算机已连接到给定副本集。 它可以向域进行身份验证并执行 LDAP 查询。
1. 客户端与副本集的连接将终止。 这会通过限制网络访问来进行。
1. 客户端随后会与其他副本集建立新连接。 一旦发生这种情况，客户端能够向域进行身份验证并执行 LDAP 查询。 
1. 域成员将重新启动，并且域用户能够在重新启动后登录。
1. 网络限制将被消除，客户端能够连接到原始副本集。 

## <a name="prerequisites"></a>先决条件 

必须满足以下要求才能完成 DR 演练： 

- 部署了活动 Azure AD DS 实例，并且至少实施了一个额外副本集。 域必须处于正常状态。 
- 已加入 Azure AD DS 托管域的客户端计算机。  客户端必须在其自己的虚拟网络中，启用了具有两个副本集虚拟网络的虚拟网络对等互连，并且虚拟网络必须具有 DNS 中列出的副本集中所有域控制器的 IP 地址。 

## <a name="environment-validation"></a>环境验证 

1. 使用域帐户登录客户端计算机。 
1. 安装 Active Directory 域服务 RSAT 工具。 
1. 启动提升的 PowerShell 窗口。
1. 执行基本域验证检查： 
   - 运行 `nslookup [domain]` 以确保 DNS 解析正常工作 
   - 运行 `nltest /dsgetdc:` 以返回成功并指出当前所使用的域控制器
   - 运行 `nltest /dclist:` 以返回目录中的域控制器的完整列表 
1. 在目录中的每个域控制器上执行基本域控制器验证（可以从“nltest /dclist:”的输出获取完整列表）： 
   - 运行 `nltest /sc_reset:[domain name]\[domain controller name]` 以与域控制器建立安全连接。 
   - 运行 `Get-AdDomain` 以检索基本目录设置。 

## <a name="perform-the-disaster-recovery-drill"></a>执行灾难恢复演练 

你将对 Azure AD DS 实例中的每个副本集执行这些操作。 这将模拟每个副本集的中断。 当无法访问域控制器时，客户端会自动故障转移到可访问的域控制器，并且此体验对于最终用户或工作负载应该是无缝的。 因此，应用程序和服务不指向特定域控制器，这一点至关重要。 

1. 标识要模拟离线的副本集中的域控制器。 
1. 在客户端计算机上，使用 `nltest /sc_reset:[domain]\[domain controller name]` 连接到域控制器之一。 
1. 在 Azure 门户中，转到客户端虚拟网络对等互连并更新属性，使客户端与副本集之间的所有流量都受到阻止。 
   1. 选择要更新的对等互连网络。 
   1. 选择阻止进入或离开虚拟网络的所有网络流量。 
      ![如何在 Azure 门户中阻止流量的屏幕截图](./media/tutorial-perform-disaster-recovery-drill/block-traffic.png)
1. 在客户端计算机上，尝试使用相同 nltest 命令与步骤 2 中的两个域控制器重新建立安全连接。 这些操作应失败，因为网络连接已受到阻止。 
1. 运行 `Get-AdDomain` 和 `Get-AdForest` 以获取基本目录属性。 这些调用会成功，因为它们会自动转到另一个副本集中的一个域控制器。 
1. 重新启动客户端，并使用相同域帐户登录。 这表明身份验证仍按预期方式工作，登录未受到阻止。 
1. 在 Azure 门户中，转到客户端虚拟网络对等互连并更新属性，以便取消阻止所有流量。 这会还原在步骤 3 中进行的更改。 
1. 在客户端计算机上，尝试使用相同 nltest 命令与步骤 2 中的域控制器重新建立安全连接。 这些操作应成功，因为网络连接已取消阻止。 

这些操作表明域仍可用，即使客户端无法访问其中一个副本集。 对 Azure AD DS 实例中的每个副本集执行这组步骤。 

## <a name="summary"></a>总结 

完成这些步骤后，在无法访问 Azure AD DS 中的某个副本集时，你会看到域成员可继续访问目录。 你可以通过对副本集（而不是客户端计算机）阻止所有网络访问来模拟相同行为，但不建议这样做。 它不会从客户端的角度更改行为，但会影响 Azure AD DS 实例的运行状况，直到网络访问恢复。 

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 验证与副本集中的域控制器的客户端连接
> * 阻止客户端与副本集之间的网络流量
> * 验证与另一个副本集中的域控制器的客户端连接

有关更多概念信息，请了解副本集在 Azure AD DS 中的工作原理。

> [!div class="nextstepaction"]
> [副本集概念和功能][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
