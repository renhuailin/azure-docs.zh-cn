---
title: 如何启用 Azure VMware 解决方案资源
description: 了解如何提交支持请求以启用 Azure VMware 解决方案资源。 你还可以在现有的 Azure VMware 解决方案私有云中请求更多主机。
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 68f8fe38c67ec5ddad7cf662a7996330ee2a1e73
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95794715"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>如何启用 Azure VMware 解决方案资源
了解如何提交支持请求以启用 [Azure VMware 解决方案](introduction.md) 资源。 你还可以在现有的 Azure VMware 解决方案私有云中请求更多主机。

## <a name="eligibility-criteria"></a>资格条件

需要 Azure 订阅中的 Azure 帐户。 Azure 订阅必须符合下列条件之一：

* Azure 企业协议中的订阅 [ (EA ](../cost-management-billing/manage/ea-portal-agreements.md) 与 Microsoft) 。
* 云解决方案提供商 (CSP) Azure 计划下的托管订阅。


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>为 EA 客户启用 Azure VMware 解决方案
在创建 Azure VMware 解决方案资源之前，你需要提交支持票证，以分配你的主机。 支持团队收到你的请求后，最多需要五个工作日来确认你的请求并分配你的主机。 如果你有现有的 Azure VMware 解决方案私有云，但需要分配更多的主机，你会经历相同的过程。


1. 在 Azure 门户中，在 " **帮助 + 支持**" 下创建 **[新的支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，并为票证提供以下信息：
   - **问题类型：** 技术方面
   - **订阅：** 选择你的订阅
   - **服务：** 所有服务 > Azure VMware 解决方案
   - **资源：** 一般问题 
   - **摘要：** 需要容量
   - **问题类型：** 容量管理问题
   - **问题子类型：** 客户请求额外的主机配额/容量

1. 在支持票证 **说明** 的 " **详细信息** " 选项卡上，提供：

   - POC 或生产 
   - 区域名称
   - 主机数
   - 任何其他详细信息

   >[!NOTE]
   >Azure VMware 解决方案建议至少使用三个主机来启动私有云，并为冗余 N + 1 主机提供支持。 

1. 选择 " **查看 + 创建** " 以提交请求。

   支持代表需要5个工作日内确认你的请求。

   >[!IMPORTANT] 
   >如果你已有现有的 Azure VMware 解决方案，但你请求其他主机，请注意，我们需要5个工作日来分配主机。 

1. 预配主机之前，请确保在 Azure 门户中注册了 **MICROSOFT AVS** 资源提供程序。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   有关注册资源提供程序的其他方式，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>为 CSP 客户启用 Azure VMware 解决方案 

Csp 必须使用 [Microsoft 合作伙伴中心](https://partner.microsoft.com) 为其客户启用 Azure VMware 解决方案。 

   >[!IMPORTANT] 
   >Azure VMware 解决方案服务不提供所需的多租户。 不支持承载需要它的合作伙伴。 

1. 在 " **合作伙伴中心**"，选择 " **CSP** " 以访问 " **客户** " 区域。

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft 合作伙伴中心客户区域" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. 选择您的客户，然后选择 " **添加产品**"。

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft 合作伙伴中心" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. 选择 " **Azure 计划** "，然后选择 " **添加到购物车**"。 

1. 查看并完成客户的 Azure 计划订阅的常规设置。 有关详细信息，请参阅 [Microsoft 合作伙伴中心文档](https://docs.microsoft.com/partner-center/azure-plan-manage)。

配置 Azure 计划并将所需的 vSphere RBAC 权限设置为 CSP 后，你将与 Microsoft 合作，以便为 Azure 计划订阅启用配额。 请代表 (AOBO) 过程，使用 **管理员** 访问合作伙伴中心的 Azure 门户。

1. 登录[合作伙伴中心](https://partner.microsoft.com)。

1. 选择 " **CSP** " 以访问 " **客户** " 区域。

1. 展开 "客户详细信息" 并选择 **Microsoft Azure 管理门户**。

1. 在 Azure 门户中，在 " **帮助 + 支持**" 下创建 **[新的支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，并为票证提供以下信息：
   - **问题类型：** 技术方面
   - **订阅：** 选择你的订阅
   - **服务：** 所有服务 > Azure VMware 解决方案
   - **资源：** 一般问题 
   - **摘要：** 需要容量
   - **问题类型：** 容量管理问题
   - **问题子类型：** 客户请求额外的主机配额/容量

1. 在支持票证 **说明** 的 " **详细信息** " 选项卡上，提供：

   - POC 或生产 
   - 区域名称
   - 主机数
   - 任何其他详细信息
   - 用于托管多个客户？

   >[!NOTE]
   >Azure VMware 解决方案建议至少使用三个主机来启动私有云，并为冗余 N + 1 主机提供支持。 

1. 选择 " **查看 + 创建** " 以提交请求。

   支持代表需要5个工作日内确认你的请求。

   >[!IMPORTANT] 
   >如果你已有现有的 Azure VMware 解决方案，但你请求其他主机，请注意，我们需要5个工作日来分配主机。 

1. 如果订阅由服务提供商进行管理，则他们的管理团队 **必须 (AOBO**) 过程从合作伙伴中心访问 Azure 门户。 其中一个 Azure 门户启动 [Cloud Shell](../cloud-shell/overview.md) 实例并注册 **Microsoft AVS** 资源提供程序，并继续部署 Azure VMware 解决方案私有云。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   有关注册资源提供程序的其他方式，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

1. 如果订阅直接由客户管理，则必须由在订阅中具有足够权限的用户来注册 **MICROSOFT AVS** 资源提供程序，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md) 了解更多详细信息和注册资源提供程序的方法。 


## <a name="next-steps"></a>后续步骤

启用 Azure VMware 解决方案资源并准备好适当的网络后，可以 [创建私有云](tutorial-create-private-cloud.md)。