---
title: 按管理任务委派角色 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中为标识任务委托角色
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65ce79252c25d187ed61de3b637182deaf225db
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136339"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>在 Azure Active Directory 中按管理员任务委托管理员角色

本文介绍了通过在 Azure Active Directory (Azure AD) 中分配最小特权角色来限制用户管理员权限所需的信息。 你将能查找按功能区域整理的管理员任务、执行每项任务所需的最小特权角色，以及可以执行任务的其他非全局管理员角色。

## <a name="application-proxy"></a>应用程序代理

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置应用程序代理应用 | 应用程序管理员 |  |
> | 配置连接器组属性 | 应用程序管理员 |  |
> | 对所有用户禁用功能时，创建应用程序注册 | 应用程序开发人员 | 云应用管理员<br/>应用程序管理员 |
> | 创建连接器组 | 应用程序管理员 |  |
> | 删除连接器组 | 应用程序管理员 |  |
> | 禁用应用程序代理 | 应用程序管理员 |  |
> | 下载连接器服务 | 应用程序管理员 |  |
> | 读取所有配置 | 应用程序管理员 |  |

## <a name="external-identitiesb2c"></a>外部标识/B2C

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 创建 Azure AD B2C 目录 | 所有非来宾用户（[请参阅文档](../fundamentals/users-default-permissions.md)） |  |
> | 创建 B2C 应用程序 | 全局管理员角色 |  |
> | 创建企业应用程序 | 云应用管理员 | 应用程序管理员 |
> | 创建、读取、更新和删除 B2C 策略 | B2C IEF 策略管理员 |  |
> | 创建、读取、更新和删除标识提供者 | 外部标识提供者管理员 |  |
> | 创建、读取、更新和删除密码重置用户流 | 外部 ID 用户流管理员 |  |
> | 创建、读取、更新和删除配置文件编辑用户流 | 外部 ID 用户流管理员 |  |
> | 创建、读取、更新和删除登录用户流 | 外部 ID 用户流管理员 |  |
> | 创建、读取、更新和删除注册用户流 |外部 ID 用户流管理员 |  |
> | 创建、读取、更新和删除用户特性 | 外部 ID 用户流属性管理员 |  |
> | 创建、读取、更新和删除用户 | 用户管理员 |  |
> | 配置 B2B 外部协作设置 | 全局管理员角色 |  |
> | 读取所有配置 | 全局读取者 |  |
> | 读取 B2C 审核日志 | 全局读取者（[请参阅文档](../../active-directory-b2c/faq.yml)） |  |

> [!NOTE]
> Azure AD B2C 全局管理员的权限与 Azure AD 全局管理员的权限不同。 如果你拥有 Azure AD B2C 全局管理员权限，请确保你位于 Azure AD B2C 目录（而不是 Azure AD 目录）中。

## <a name="company-branding"></a>公司品牌

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置公司品牌 | 全局管理员角色 |  |
> | 读取所有配置 | 目录读者 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |

## <a name="company-properties"></a>公司属性

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置公司属性 | 全局管理员角色 |  |

## <a name="connect"></a>连接

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 直通身份验证 | 全局管理员角色 |  |
> | 读取所有配置 | 全局读取者 | 全局管理员角色 |
> | 无缝单一登录 | 全局管理员角色 |  |

## <a name="cloud-provisioning"></a>云预配

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 直通身份验证 | 混合标识管理员 |  |
> | 读取所有配置 | 全局读取者 | 混合标识管理员 |
> | 无缝单一登录 | 混合标识管理员 |  |

## <a name="connect-health"></a>Connect Health

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 添加或删除服务 | 所有者（[请参阅文档](../hybrid/how-to-connect-health-operations.md)） |  |
> | 应用修复项来同步错误 | 参与者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | “所有者” |
> | 配置通知 | 参与者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | “所有者” |
> | 配置设置 | 所有者（[请参阅文档](../hybrid/how-to-connect-health-operations.md)） |  |
> | 配置同步通知 | 参与者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | “所有者” |
> | 读取 ADFS 安全报告 | 安全读取者 | 参与者<br/>所有者
> | 读取所有配置 | 读者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | 参与者<br/>所有者 |
> | 读取同步错误 | 读者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | 参与者<br/>所有者 |
> | 读取同步服务 | 读者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | 参与者<br/>所有者 |
> | 查看指标和警报 | 读者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | 参与者<br/>所有者 |
> | 查看指标和警报 | 读者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | 参与者<br/>所有者 |
> | 查看同步服务指标和警报 | 读者（[请参阅文档](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)） | 参与者<br/>所有者 |

## <a name="custom-domain-names"></a>自定义域名

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 管理域 | 域名管理员 |  |
> | 读取所有配置 | 目录读者 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |

## <a name="domain-services"></a>域服务

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 创建 Azure AD 域服务实例 | 全局管理员 |  |
> | 执行所有 Azure AD 域服务任务 | Azure AD DC 管理员组（[请参阅文档](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)） |  |
> | 读取所有配置 | 包含 AD DS 服务的 Azure 订阅的读者 |  |

## <a name="devices"></a>设备

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 禁用设备 | 云设备管理员 |  |
> | 启用设备 | 云设备管理员 |  |
> | 读取基本配置 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |  |
> | 读取 BitLocker 密钥 | 安全读取者 | 密码管理员<br/>安全管理员 |

## <a name="enterprise-applications"></a>企业应用程序

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 同意任何委托的权限 | 云应用管理员 | 应用程序管理员 |
> | 同意应用程序权限（不包括 Microsoft Graph） | 云应用管理员 | 应用程序管理员 |
> | 同意 Microsoft Graph 的应用程序权限 | 特权角色管理员 |  |
> | 同意应用程序访问自己的数据 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |  |
> | 创建企业应用程序 | 云应用管理员 | 应用程序管理员 |
> | 管理应用程序代理 | 应用程序管理员 |  |
> | 管理用户设置 | 全局管理员角色 |  |
> | 读取组或应用的访问评审 | 安全读取者 | 安全管理员<br/>用户管理员 |
> | 读取所有配置 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |  |
> | 更新企业应用程序分配 | 企业应用程序所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 云应用管理员<br/>应用程序管理员 |
> | 更新企业应用程序所有者 | 企业应用程序所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 云应用管理员<br/>应用程序管理员 |
> | 更新企业应用程序属性 | 企业应用程序所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 云应用管理员<br/>应用程序管理员 |
> | 更新企业应用程序预配 | 企业应用程序所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 云应用管理员<br/>应用程序管理员 |
> | 更新企业应用程序自助服务 | 企业应用程序所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 云应用管理员<br/>应用程序管理员 |
> | 更新单一登录属性 | 企业应用程序所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 云应用管理员<br/>应用程序管理员 |

## <a name="entitlement-management"></a>权利管理

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 将资源添加到目录 | Identity Governance 管理员 | 通过权利管理，你可将此任务委派给目录所有者（[参阅文档](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)） |
> | 向目录添加 SharePoint Online 站点 | SharePoint 管理员 |  |

## <a name="groups"></a>组

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 分配许可证 | 用户管理员 |  |
> | 创建组 | 组管理员 | 用户管理员 |
> | 创建、更新或删除组或应用的访问评审 | 用户管理员 |  |
> | 管理组到期时间 | 用户管理员 |  |
> | 管理组设置 | 组管理员 | 用户管理员 |
> | 读取所有配置（隐藏成员身份除外） | 目录读者 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |
> | 读取隐藏成员身份 | 组成员 | 组所有者<br/>密码管理员<br/>Exchange 管理员<br/>SharePoint 管理员<br/>Teams 管理员<br/>用户管理员 |
> | 读取具有隐藏成员身份的组的成员身份 | 支持管理员 | 用户管理员<br/>Teams 管理员 |
> | 撤销许可证 | 许可证管理员 | 用户管理员 |
> | 更新组成员身份 | 组所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 用户管理员 |
> | 更新组所有者 | 组所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 用户管理员 |
> | 更新组属性 | 组所有者（[请参阅文档](../fundamentals/users-default-permissions.md)） | 用户管理员 |
> | 删除组 | 组管理员 | 用户管理员 |

## <a name="identity-protection"></a>标识保护

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置警报通知| 安全管理员 |  |
> | 配置并启用/禁用 MFA 策略| 安全管理员 |  |
> | 配置并启用/禁用登录风险策略| 安全管理员 |  |
> | 配置并启用/禁用用户风险策略 | 安全管理员 |  |
> | 配置每周摘要 | 安全管理员 |  |
> | 消除所有风险检测 | 安全管理员 |  |
> | 修复或消除漏洞 | 安全管理员 |  |
> | 读取所有配置 | 安全读取者 |  |
> | 读取所有风险检测 | 安全读取者 |  |
> | 读取漏洞 | 安全读取者 |  |

## <a name="licenses"></a>许可证

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 分配许可证 | 许可证管理员 | 用户管理员 |
> | 读取所有配置 | 目录读者 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |
> | 撤销许可证 | 许可证管理员 | 用户管理员 |
> | 试用或购买订阅 | 计费管理员 |  |

## <a name="monitoring---audit-logs"></a>监视 - 审核日志

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 读取审核日志 | 报告读者 | 安全读取者<br/>安全管理员 |

## <a name="monitoring---sign-ins"></a>监视 - 登录

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 读取登录日志 | 报告读者 | 安全读取者<br/>安全管理员<br/> 全局读取者 |

## <a name="multi-factor-authentication"></a>多重身份验证

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 删除选定用户生成的所有现有应用密码 | 全局管理员角色 |  |
> | 禁用 MFA | 身份验证管理员（通过 PowerShell） | 特权身份验证管理员（通过 PowerShell） |
> | 启用 MFA | 身份验证管理员（通过 PowerShell） | 特权身份验证管理员（通过 PowerShell） | 
> | 管理 MFA 服务设置 | 身份验证策略管理员 |  |
> | 要求选定的用户再次提供联系方法 | 身份验证管理员 |  |
> | 在所有记住的设备上还原多重身份验证  | 身份验证管理员 |  |

## <a name="mfa-server"></a>MFA 服务器

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 阻止/解除阻止用户 | 身份验证策略管理员 |  |
> | 配置帐户锁定 | 身份验证策略管理员 |  |
> | 配置缓存规则 | 身份验证策略管理员 |  |
> | 配置欺诈警报 | 身份验证策略管理员 |  |
> | 配置通知 | 身份验证策略管理员 |  |
> | 配置免验证一次 | 身份验证策略管理员 |  |
> | 配置电话呼叫设置 | 身份验证策略管理员 |  |
> | 配置提供程序 | 身份验证策略管理员 |  |
> | 配置服务器设置 | 身份验证策略管理员 |  |
> | 读取活动报表 | 全局读取者 |  |
> | 读取所有配置 | 全局读取者 |  |
> | 读取服务器状态 | 全局读取者 |  |

## <a name="organizational-relationships"></a>组织关系

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 管理标识提供者 | 外部标识提供者管理员 |  |
> | 管理设置 | 全局管理员角色 |  |
> | 管理使用条款 | 全局管理员角色 |  |
> | 读取所有配置 | 全局读取者 |  |

## <a name="password-reset"></a>密码重置

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置身份验证方法 | 全局管理员角色 |  |
> | 配置自定义 | 全局管理员角色 |  |
> | 配置通知 | 全局管理员角色 |  |
> | 配置本地集成 | 全局管理员角色 |  |
> | 配置密码重置属性 | 用户管理员 | 全局管理员角色 |
> | 配置注册 | 全局管理员角色 |  |
> | 读取所有配置 | 安全管理员 | 用户管理员 |

## <a name="privileged-identity-management"></a>Privileged identity management

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 将用户分配到角色 | 特权角色管理员 |  |
> | 配置角色设置 | 特权角色管理员 |  |
> | 查看审核活动 | 安全读取者 |  |
> | 查看角色成员身份 | 安全读取者 |  |

## <a name="roles-and-administrators"></a>角色和管理员

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 管理角色分配 | 特权角色管理员 |  |
> | 读取 Azure AD 角色的访问评审  | 安全读取者 | 安全管理员<br/>特权角色管理员 |
> | 读取所有配置 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |  |

## <a name="security---authentication-methods"></a>安全性 - 身份验证方法

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置身份验证方法 | 全局管理员角色 |  |
> | 配置密码保护 | 安全管理员 |  |
> | 配置智能锁定 | 安全管理员 |
> | 读取所有配置 | 全局读取者 |  |

## <a name="security---conditional-access"></a>安全性 - 条件访问

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 配置 MFA 受信任的 IP | 条件访问管理员 |  |
> | 创建自定义控件 | 条件访问管理员 | 安全管理员 |
> | 创建命名位置 | 条件访问管理员 | 安全管理员 |
> | 创建策略 | 条件访问管理员 | 安全管理员 |
> | 创建使用条款 | 条件访问管理员 | 安全管理员 |
> | 创建 VPN 连接证书 | 条件访问管理员 | 安全管理员 |
> | 删除经典策略 | 条件访问管理员 | 安全管理员 |
> | 删除使用条款 | 条件访问管理员 | 安全管理员 |
> | 删除 VPN 连接证书 | 条件访问管理员 | 安全管理员 |
> | 禁用经典策略 | 条件访问管理员 | 安全管理员 |
> | 管理自定义控件 | 条件访问管理员 | 安全管理员 |
> | 管理命名位置 | 条件访问管理员 | 安全管理员 |
> | 管理使用条款 | 条件访问管理员 | 安全管理员 |
> | 读取所有配置 | 安全读取者 | 安全管理员 |
> | 读取命名位置 | 安全读取者 | 条件访问管理员<br/>安全管理员 |

## <a name="security---identity-security-score"></a>安全性 - 标识安全分数

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 | 
> | ---- | --------------------- | ---------------- |
> | 读取所有配置 | 安全读取者 | 安全管理员 |
> | 读取安全分数 | 安全读取者 | 安全管理员 |
> | 更新事件状态 | 安全管理员 |  |

## <a name="security---risky-sign-ins"></a>安全性 - 风险登录

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 读取所有配置 | 安全读取者 |  |
> | 读取有风险的登录 | 安全读取者 |  |

## <a name="security---users-flagged-for-risk"></a>安全性 - 已标记为存在风险的用户

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 清除所有事件 | 安全管理员 |  |
> | 读取所有配置 | 安全读取者 |  |
> | 读取已标记为存在风险的用户 | 安全读取者 |  |

## <a name="users"></a>用户

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 将用户添加到目录角色 | 特权角色管理员 |  |
> | 将用户添加到组 | 用户管理员 |  |
> | 分配许可证 | 许可证管理员 | 用户管理员 |
> | 创建来宾用户 | 来宾邀请者 | 用户管理员 |
> | 创建用户 | 用户管理员 |  |
> | 删除用户 | 用户管理员 |  |
> | 使受限管理员的刷新令牌失效（请参阅文档） | 用户管理员 |  |
> | 使非管理员的刷新令牌失效（请参阅文档） | 密码管理员 | 用户管理员 |
> | 使特权管理员的刷新令牌失效（请参阅文档） | 特权身份验证管理员 |  |
> | 读取基本配置 | 默认用户角色（[请参阅文档](../fundamentals/users-default-permissions.md)） |  |
> | 重置受限管理员的密码（请参阅文档） | 用户管理员 |  |
> | 重置非管理员的密码（请参阅文档） | 密码管理员 | 用户管理员 |
> | 重置特权管理员的密码 | 特权身份验证管理员 |  |
> | 撤销许可证 | 许可证管理员 | 用户管理员 |
> | 更新除用户主体名称之外的所有属性 | 用户管理员 |  |
> | 更新受限管理员的用户主体名称（请参阅文档） | 用户管理员 |  |
> | 更新特权管理员的用户主体名称属性（请参阅文档） | 全局管理员角色 |  |
> | 更新用户设置 | 全局管理员角色 |  |
> | 更新身份验证方法 | 身份验证管理员 | 特权身份验证管理员<br/>全局管理员角色 |

## <a name="support"></a>支持

> [!div class="mx-tableFixed"]
> | 任务 | 最小特权角色 | 其他角色 |
> | ---- | --------------------- | ---------------- |
> | 提交支持票证 | 服务支持管理员 | 应用程序管理员<br/>Azure 信息保护管理员<br/>计费管理员<br/>云应用管理员<br/>符合性管理员<br/>Dynamics 365 管理员<br/>桌面分析管理员<br/>Exchange 管理员<br/>Intune 管理员<br/>密码管理员<br/>Power BI 管理员<br/>特权身份验证管理员<br/>SharePoint 管理员<br/>Skype for Business 管理员<br/>Teams 管理员<br/>Teams 通信管理员<br/>用户管理员<br/>工作区分析管理员 |

## <a name="next-steps"></a>后续步骤

* [如何分配或删除 Azure AD 管理员角色](manage-roles-portal.md)
* [Azure AD 内置角色](permissions-reference.md)
