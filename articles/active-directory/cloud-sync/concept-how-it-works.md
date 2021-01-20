---
title: Azure AD Connect 云同步深入探讨-工作原理
description: 本主题提供有关云同步工作原理的详细信息。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613188"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>云同步深入探讨-工作原理

## <a name="overview-of-components"></a>组件概览

![工作原理](media/concept-how-it-works/how-1.png)

云同步建立在 Azure AD 服务之上，具有2个关键组件：

- **预配代理**： Azure AD Connect 云预配代理与 Workday 入站相同，并建立在与应用代理相同的服务器端技术上，并通过身份验证。 它仅需要和出站连接，并且自动更新代理。 
- **预配服务**：与使用基于计划程序的模型的出站预配和 Workday 入站设置相同的预配服务。 如果是云同步，则会每隔2分钟预配更改。


## <a name="initial-setup"></a>初始设置
在初始安装过程中，将完成一些操作，使云同步发生。  这些是： 

- 在 **代理安装过程中**：为要从其预配的 AD 域配置代理。  此配置将注册混合标识服务中的域，并建立与侦听请求的服务总线的出站连接。
- **启用预配时**：选择 AD 域并启用每2分钟运行一次的预配。 或者，您可以选择 "密码哈希同步" 和 "定义通知电子邮件"。 你还可以使用 Microsoft Graph Api 来管理属性转换。


## <a name="agent-installation"></a>代理安装
下面是安装云预配代理时发生的情况。

- 首先，安装程序将在虚拟服务帐户下安装代理二进制文件和运行的代理服务 (网络 SERVICE\AADProvisioningAgent) 。  虚拟服务帐户是一种特殊类型的帐户，它没有密码且由 Windows 管理。
- 然后，安装程序将启动向导。
- 向导将提示输入 Azure AD 凭据，然后将进行身份验证并检索令牌。
- 然后，该向导要求提供当前计算机域管理员凭据。
- 使用这些凭据，可以创建或查找此域 (GMSA) 的代理常规托管服务帐户，如果该帐户已存在，则重复使用。
- 现在，代理服务将重新配置为在 GMSA 下运行。
- 向导现在要求提供域配置，同时为你希望代理服务的每个域提供企业管理员 (EA) /Domain Admin (DA) 帐户。
- 然后，使用允许 it 访问上面输入的每个域的权限更新 GMSA 帐户。
- 接下来，向导将触发代理注册
- 代理会创建一个证书并使用 Azure AD 令牌，使用混合标识服务向其) 注册服务注册自身和证书 (
- 向导将触发 AgentResourceGrouping 调用。 此对其管理服务的调用是将代理分配给其配置中的一个或多个 AD 域。
- 向导现在会重新启动代理服务。
- 代理会在重新启动时调用启动服务 (并且此后每10分钟) 检查配置更新。  启动服务将验证代理标识。  它还更新上次启动时间。  这一点很重要，因为如果代理未启动，则它们不会获得更新的服务总线终结点，并且可能无法接收请求。 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>什么是跨域身份管理系统 (SCIM)？

[SCIM 规范](https://tools.ietf.org/html/draft-scim-core-schema-01)是一种标准，用于在标识域之间自动交换用户或组标识信息，如 Azure AD。 SCIM 正在成为预配的事实标准，与联合标准（例如 SAML 或 OpenID Connect）结合使用时，可为管理员提供基于标准的端到端访问权限管理解决方案。

Azure AD Connect 云预配代理使用带有 Azure AD 的 SCIM 来设置和取消设置用户和组。

## <a name="synchronization-flow"></a>同步流
![](media/concept-how-it-works/provisioning-4.png)如果已安装代理并启用预配，则会发生以下流。

1.  配置后，Azure AD 预配服务将调用 Azure AD 混合服务，以将请求添加到服务总线。 代理会持续保持与服务总线的出站连接，从而侦听请求并为跨域标识管理 (SCIM) 请求提供系统。 
2.  代理会根据对象类型将请求分解为单独的查询。 
3.  AD 会将结果返回给代理，代理会在将此数据发送到 Azure AD 之前对其进行筛选。  
4.  代理会将 SCIM 响应返回到 Azure AD。  这些响应基于代理内发生的筛选。  代理使用范围来筛选结果。 
5.  预配服务会将更改写入 Azure AD。
6. 如果这是增量同步，而不是完全同步，则使用 cookie/水印。 新查询将从该 cookie/水印向前获取更改。

## <a name="supported-scenarios"></a>支持的方案：
云同步支持以下方案。


- **具有新林的现有混合客户**： Azure AD Connect 同步用于主林。 云同步用于从 AD 林进行预配， (包括断开连接的) 。 有关详细信息，请参阅 [此处](tutorial-existing-forest.md)的教程。

 ![现有混合](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **新的混合客户**：未使用 Azure AD Connect 同步。 云同步用于从 AD 林进行设置。  有关详细信息，请参阅 [此处](tutorial-single-forest.md)的教程。
 
 ![新客户](media/tutorial-single-forest/diagram-2.png)

- **现有混合客户**： Azure AD Connect 同步用于主林。 对于 [此处](tutorial-existing-forest.md)的主林的一小部分用户，云同步将进行试验。

 ![现有试点](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

有关详细信息，请参阅 [支持的拓扑](plan-cloud-sync-topologies.md)。



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
