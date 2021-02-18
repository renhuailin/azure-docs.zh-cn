---
title: 请求主机配额并启用 Azure VMware 解决方案
description: 了解如何请求主机配额/容量并启用 Azure VMware 解决方案资源提供程序。 你还可以在现有的 Azure VMware 解决方案私有云中请求更多主机。
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653154"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>请求主机配额并启用 Azure VMware 解决方案

在本操作方法指南中，你将了解如何请求主机配额/容量并启用 [Azure VMware 解决方案](introduction.md) 资源提供程序，该提供程序可启用此服务。 在启用 Azure VMware 解决方案之前，你需要提交支持票证，以分配你的主机。 如果你有现有的 Azure VMware 解决方案私有云，并且想要分配更多主机，你将遵循相同的过程。

>[!IMPORTANT]
>根据请求的数量，分配主机可能需要几天。  因此，请请求设置所需的资源，这样就不需要经常请求配额增长。


总体过程很简单，其中包括两个步骤：
- 为[EA 客户](#request-host-quota-for-ea-customers)或[CSP 客户](#request-host-quota-for-csp-customers)请求额外的主机配额/容量 
- 启用 Microsoft AVS 资源提供程序

## <a name="eligibility-criteria"></a>资格条件

需要 Azure 订阅中的 Azure 帐户。 Azure 订阅必须遵循以下条件之一：

- Azure 企业协议中的订阅 [ (EA ](../cost-management-billing/manage/ea-portal-agreements.md) 与 Microsoft) 。
- 云解决方案提供商 (CSP) 在现有 CSP 下托管订阅 Azure 提供合同或 Azure 计划。

## <a name="request-host-quota-for-ea-customers"></a>EA 客户的请求主机配额

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


## <a name="request-host-quota-for-csp-customers"></a>为 CSP 客户请求主机配额 

Csp 必须使用 [Microsoft 合作伙伴中心](https://partner.microsoft.com) 为其客户启用 Azure VMware 解决方案。 本文使用 [CSP Azure 计划](/partner-center/azure-plan-lp) 作为示例来说明合作伙伴的购买过程。

使用 **管理员代表** 合作伙伴中心 (AOBO) 过程来访问 Azure 门户。

>[!IMPORTANT] 
>Azure VMware 解决方案服务不提供所需的多租户。 不支持承载需要它的合作伙伴。 

1. 配置 CSP Azure 计划：

   1. 在 " **合作伙伴中心**"，选择 " **CSP** " 以访问 " **客户** " 区域。
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft 合作伙伴中心客户区域" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. 选择您的客户，然后选择 " **添加产品**"。
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft 合作伙伴中心" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. 选择 " **Azure 计划** "，然后选择 " **添加到购物车**"。 
   
   1. 查看并完成客户的 Azure 计划订阅的常规设置。 有关详细信息，请参阅 [Microsoft 合作伙伴中心文档](/partner-center/azure-plan-manage)。

1. 配置 Azure 计划并拥有订阅所需的 [AZURE RBAC 权限](/partner-center/azure-plan-manage) 后，你将请求 azure 计划订阅的配额。 

   1. 使用 **管理员代表** (AOBO) 过程访问 [Microsoft 合作伙伴中心](https://partner.microsoft.com)的 Azure 门户。
   
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

## <a name="register-the-microsoftavs-resource-provider"></a>注册 **MICROSOFT AVS** 资源提供程序

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>后续步骤

启用资源并准备好适当的网络后，可以 [创建私有云](tutorial-create-private-cloud.md)。
