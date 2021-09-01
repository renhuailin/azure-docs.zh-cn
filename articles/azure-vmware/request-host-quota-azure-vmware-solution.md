---
title: 为 Azure VMware 解决方案请求主机配额
description: 了解如何为 Azure VMware 解决方案请求主机配额/容量。 还可以在现有 Azure VMware 解决方案私有云中请求更多主机。
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 08/06/2021
ms.openlocfilehash: 92ebc79193525802c1e7c7ede9287e3300799a10
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322454"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>为 Azure VMware 解决方案请求主机配额

在此操作说明中，你将为 [Azure VMware 解决方案](introduction.md)请求主机配额/容量。 你将提交支持工单以分配主机，无论该主机是用于新部署还是现有部署。 

如果已有 Azure VMware 解决方案私有云，并想要分配更多主机，请执行相同的过程。

>[!IMPORTANT]
>分配主机可能最多需要五个工作日的时间，具体取决于请求的数量。  因此，应请求预配所需的资源，以避免经常请求提高配额。

## <a name="eligibility-criteria"></a>资格条件

需要 Azure 订阅中的 Azure 帐户，该订阅满足以下条件之一：

- Microsoft [Azure 企业协议 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 下的订阅。
- 通过现有 CSP Azure 产品/服务合同或 Azure 计划购买的云解决方案提供商 (CSP) 托管订阅。
- 与 Microsoft 签署的[现代商业协议](../cost-management-billing/understand/mca-overview.md)。

## <a name="request-host-quota-for-ea-customers"></a>为 EA 客户请求主机配额

1. 在 Azure 门户中的“帮助和支持”下，[新建支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)，并提供以下信息：
   - **问题类型：** 技术方面
   - **订阅：** 选择自己的订阅
   - **服务：** “所有服务”>“Azure VMware 解决方案”
   - **资源：** 一般问题 
   - **摘要：** 需要容量
   - **问题类型：** 容量管理问题
   - **问题子类型：** 客户请求额外的主机配额/容量

1. 在“详情”选项卡上，在支持票证的“说明”部分提供以下信息：

   - POC 或生产 
   - 区域名称
   - 主机数
   - 任何其他详细信息

   >[!NOTE]
   >Azure VMware 解决方案至少需要三个主机，并建议使用 N+1 个主机的冗余。 

1. 选择“查看 + 创建”以提交请求。


## <a name="request-host-quota-for-csp-customers"></a>为 CSP 客户请求主机配额 

CSP 必须使用 [Microsoft 合作伙伴中心](https://partner.microsoft.com)来为其客户启用 Azure VMware 解决方案。 本文以 [CSP Azure 计划](/partner-center/azure-plan-lp)为例来演示合作伙伴的购买过程。

使用 Admin On Behalf Of (AOBO) 过程从合作伙伴中心访问 Azure 门户。

>[!IMPORTANT] 
>Azure VMware 解决方案服务不提供所需的多租户。 不支持托管需要该服务的合作伙伴。 

1. 配置 CSP Azure 计划：

   1. 在“合作伙伴中心”，选择“CSP”以访问“客户”区域  。
   
      :::image type="content" source="media/pre-deployment/csp-customers-screen.png" alt-text="显示 Microsoft 合作伙伴中心客户区域的屏幕截图。" lightbox="media/pre-deployment/csp-customers-screen.png":::
   
   1. 选择你的客户，然后选择“添加产品”。
   
      :::image type="content" source="media/pre-deployment/csp-partner-center.png" alt-text="显示在 Microsoft 合作伙伴中心选中“Azure 计划”的屏幕截图。" lightbox="media/pre-deployment/csp-partner-center.png":::
   
   1. 选择“Azure 计划”，然后选择“添加到购物车” 。 
   
   1. 查看并完成客户的 Azure 计划订阅的常规设置。 有关详细信息，请参阅 [Microsoft 合作伙伴中心文档](/partner-center/azure-plan-manage)。

1. 配置 Azure 计划并拥有订阅所需的 [Azure RBAC 权限](/partner-center/azure-plan-manage)后，可为 Azure 计划订阅请求配额。 

   1. 使用 Admin On Behalf Of (AOBO) 过程从 [Microsoft 合作伙伴中心](https://partner.microsoft.com)访问 Azure 门户。
   
   1. 选择“CSP”以访问“客户”区域。
   
   1. 展开客户详细信息并选择“Microsoft Azure 管理门户”。
   
   1. 在 Azure 门户中的“帮助和支持”下，[新建支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)，并提供以下信息：
      - **问题类型：** 技术方面
      - **订阅：** 选择自己的订阅
      - **服务：** “所有服务”>“Azure VMware 解决方案”
      - **资源：** 一般问题 
      - **摘要：** 需要容量
      - **问题类型：** 容量管理问题
      - **问题子类型：** 客户请求额外的主机配额/容量
   
   1. 在“详情”选项卡上，在支持票证的“说明”部分提供以下信息：
   
      - POC 或生产 
      - 区域名称
      - 主机数
      - 任何其他详细信息
      - 是否用于托管多个客户？
   
      >[!NOTE]
      >Azure VMware 解决方案至少需要三个主机，并建议使用 N+1 个主机的冗余。 
   
   1. 选择“查看 + 创建”以提交请求。


## <a name="next-steps"></a>后续步骤

在部署 Azure VMware 解决方案前，必须先为[资源提供程序](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)注册 Azure 订阅以启用服务。   
