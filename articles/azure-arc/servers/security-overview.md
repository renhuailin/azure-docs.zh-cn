---
title: 安全概述
description: 有关已启用 Azure Arc 的服务器的安全信息。
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 113eaaf779409cd77e66b253074146dfaa0ff0ab
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390212"
---
# <a name="azure-arc-for-servers-security-overview"></a>Azure Arc for servers 安全概述

本文介绍在企业中部署已启用 Azure Arc 的服务器之前应评估的安全配置和注意事项。

## <a name="identity-and-access-control"></a>标识和访问控制

每个已启用 Azure Arc 的服务器都有一个托管标识作为 Azure 订阅中资源组的一部分，此标识表示在本地或其他云环境中运行的服务器。 对此资源的访问由标准 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)来控制。 在 Azure 门户中的[访问控制 (IAM)](../../role-based-access-control/role-assignments-portal.md) 页上，可验证谁有权访问已启用 Azure Arc 的服务器。

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="已启用 Azure Arc 的服务器访问控制" border="false" lightbox="./media/security-overview/access-control-page.png":::

授予了对资源的[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色或管理员角色访问权限的用户和应用程序可以对资源进行更改，包括在计算机上部署或删除[扩展](manage-vm-extensions.md)。 扩展可以包含在特权上下文中运行的任意脚本，因此请考虑将 Azure 资源上的任何参与者作为服务器的间接管理员。

Azure Connected Machine Onboarding 角色可用于进行大规模加入，只能在 Azure 中读取或创建新的已启用 Arc 的服务器。 它不能用于删除已注册的服务器或用于管理扩展。 我们建议仅将此角色分配给用于大规模加入计算机的 Azure Active Directory (Azure AD) 服务主体，这是最佳做法。

如果用户是 Azure Connected Machine 资源管理员角色的成员，则可以读取、修改、重新加入和删除计算机。 此角色旨在支持对已启用 Arc 的服务器进行管理，但不支持对资源组或订阅中的其他资源进行管理。

## <a name="agent-security-and-permissions"></a>安全性和权限

若要在 Windows 上管理 Azure Connected Machine 代理 (azcmagent)，你的用户帐户需要是本地管理员组的成员。 在 Linux 上，你必须具有根访问权限。

Azure Connected Machine 代理由三个服务组成，这些服务在计算机上运行。

* Hybrid Instance Metadata Service (himds) 服务负责处理 Arc 的各项核心功能。这包括向 Azure 发送检测信号，为其他应用公开本地实例元数据服务来了解计算机的 Azure 资源 ID，还有检索 Azure AD 令牌来对其他 Azure 服务进行身份验证。 此服务在 Windows 上作为无特权虚拟服务帐户运行，在 Linux 上作为 himds 用户运行。

* 来宾配置服务 (GCService) 负责评估计算机上的 Azure Policy。

* 来宾配置扩展服务 (ExtensionService) 负责在计算机上安装、更新和删除扩展（代理、脚本或其他软件）。

来宾配置和扩展服务在 Windows 上作为本地系统运行，在 Linux 上作为根运行。

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>将托管标识用于已启用 Arc 的服务器

默认情况下，Arc 使用的 Azure Active Directory 系统分配的标识仅可用于在 Azure 中更新已启用 Arc 的服务器的状态。 例如，最后见到的检测信号的状态。 如果服务器上的应用程序使用系统分配的标识来访问其他 Azure 服务，则可以根据需要向该标识分配其他角色。

尽管计算机上运行的任何应用程序都可以访问 Hybrid Instance Metadata Service，但只有经过授权的应用程序才能为系统分配的标识请求 Azure AD 令牌。 第一次尝试访问令牌 URI 时，服务会在文件系统上仅受信任的调用方可以读取的位置中生成随机生成的加密 blob。 然后调用方必须读取文件（证明其具有适当的权限），然后使用授权标头中的文件内容重试请求，以成功检索 Azure AD 令牌。

*  在 Windows 上，调用方必须是本地管理员组或混合代理扩展应用程序组的成员才能读取 blob。

* 在 Linux 上，调用方必须是 himds 组的成员才能读取 blob。

## <a name="using-disk-encryption"></a>使用磁盘加密

Azure Connected Machine 代理使用公钥身份验证与 Azure 服务进行通信。 将服务器加入 Azure Arc 后，私钥将保存到磁盘，并在代理与 Azure 通信时使用。 如果被盗，则可以在另一台服务器上使用私钥与服务进行通信，让该服务器充当原始服务器。 这包括获取对系统分配的标识以及标识有权访问的任何资源的访问权限。 私钥文件受到保护，只允许 himds 帐户访问读取它。 为了防止脱机攻击，我们强烈建议在服务器的操作系统卷上使用完整磁盘加密（例如 BitLocker、dm-crypt 等）。

## <a name="next-steps"></a>后续步骤

* 在多台混合计算机中评估或启用已启用了 Arc 的服务器之前，请先查看[连接的计算机代理概述](agent-overview.md)，以了解要求、有关代理的技术详细信息以及部署方法。

* 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。
