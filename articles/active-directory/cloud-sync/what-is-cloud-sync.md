---
title: 什么是 Azure AD Connect 云同步 | Microsoft Docs
description: 介绍 Azure AD Connect 云同步。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/30/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: efa2c16f83fc0a85e3c2d346aed5d7fdf848c873
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355424"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>什么是 Azure AD Connect 云同步？
Azure AD Connect 云同步是 Microsoft 提供的一个新产品/服务，旨在满足和实现在 Azure AD 中同步用户、组与联系人的混合标识目标。  它使用 Azure AD 云预配代理来实现此目标，而不是使用 Azure AD Connect 应用程序。  但是，它可以与 Azure AD Connect 同步一起使用，并具有以下优势：
    
- 支持从多林离线 Active Directory 林环境同步到 Azure AD 租户：常见的应用场景包括企业并购和收购（其中，被收购公司的 AD 林独立于父公司的 AD 林），并且这些公司在过去拥有多个 AD 林。
- 使用轻型预配代理简化安装：代理充当 AD 与 Azure AD 之间的桥梁，所有同步配置托管在云中。 
- 可以使用多个预配代理来简化高可用性部署，这对于依赖于在 AD 与 Azure AD 之间实现密码哈希同步的组织而言尤其重要。
- 支持具有多达 5 万成员的大型组。 建议在同步大型组时仅使用 OU 范围筛选器。

![什么是 Azure AD Connect](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Azure AD Connect 云同步与 Azure AD Connect 同步有何不同？
使用 Azure AD Connect 云同步时，从 AD 到 Azure AD 的预配将在 Microsoft Online Services 中经过协调。 组织只需在其本地或 IaaS 托管环境中部署一个轻量级代理充当 Azure AD 与 AD 之间的桥梁。 预配配置存储在 Azure AD 中，并作为服务的一部分进行管理。

## <a name="azure-ad-connect-cloud-sync-video"></a>Azure AD Connect 云同步视频
下面的短视频简单扼要地介绍了 Azure AD Connect 云同步：

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Azure AD Connect 与云同步之间的比较

下表提供 Azure AD Connect 与 Azure AD Connect 云同步之间的比较：

| 功能 | Azure Active Directory Connect 同步| Azure Active Directory Connect 云同步 |
|:--- |:---:|:---:|
|连接到单个本地 AD 林|● |● |
| 连接到多个本地 AD 林 |● |● |
| 连接到多个离线本地 AD 林 | |● |
| 轻型代理安装模式 | |● |
| 用于实现高可用性的多个活动代理 | |● |
| 连接到 LDAP 目录|●| | 
| 支持用户对象 |● |● |
| 支持组对象 |● |● |
| 支持联系人对象 |● |● |
| 支持设备对象 |● | |
| 允许对属性流进行基本自定义 |● |● |
| 同步 Exchange Online 属性 |● |● |
| 同步扩展属性 1-15 |● |● |
| 同步客户定义的 AD 属性（目录扩展） |● | |
| 支持密码哈希同步 |●|●|
| 支持直通身份验证 |●||
| 支持联合身份验证 |●|●|
| 无缝单一登录|● |●|
| 支持在域控制器上安装 |● |● |
| Windows Server 2016 的支持|● |● |
| 按域/OU/组进行筛选 |● |● |
| 按对象的属性值进行筛选 |● | |
| 允许同步极少量的属性 (MinSync) |● |● |
| 允许删除从 AD 流向 Azure AD 的属性 |● |● |
| 允许对属性流进行高级自定义 |● | |
| 支持写回（密码、设备、组） |● | |
| Azure AD 域服务支持|● | |
| [Exchange 混合写回](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| 每个 AD 域的对象数不受限制 |● | |
| 支持对每个 AD 域配置多达 150,000 个对象 |● |● |
| 成员数超过 50,000 的组 |● |● |
| 成员数超过 250,000 的大型组 |● |  |
| 跨域引用|● | |
| 按需预配|● |● |
| 支持美国政府|● |● |

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [安装云同步](how-to-install.md)
